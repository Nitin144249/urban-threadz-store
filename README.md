# URBAN THREADZ

URBAN THREADZ — WEAR YOUR ATTITUDE. This workspace contains the retained product catalogue, an API-connected static storefront, and a Django REST API using PostgreSQL.

## Current status

This is an implementation in progress and is **not ready to accept live customer orders**. The customer storefront now calls Django for published products, accounts, addresses, cart, checkout, order history, cancellation, and UTR submission. Customer tokens are held in tab-scoped session storage. Set the `api-base` meta value in `frontend/index.html` to the deployed API origin before publishing the Pages site.

The backend has customer authentication and address management, searchable product list/detail, server cart, authenticated/idempotent checkout with transactional inventory, customer cancellation rules, owner-scoped order history, approved product-image uploads, persistent Cloudflare R2 storage configuration, and manual UPI review. UTR submission remains pending until staff verifies it. Payment TEST mode is the default. See [PROJECT_AUDIT.md](PROJECT_AUDIT.md), [PRODUCTION_TODO.md](PRODUCTION_TODO.md), and [TEST_REPORT.md](TEST_REPORT.md).

## Local development

Requirements: Docker Desktop with Docker Compose, or Python 3.12 and PostgreSQL 16.

```powershell
Copy-Item .env.example .env
# Edit .env and set unique local development secrets.
docker compose up --build
```

- Storefront: http://localhost:8080 (API base defaults to http://localhost:8000)
- API: http://localhost:8000/api/products/ and `/api/banners/`
- Health check: http://localhost:8000/health/
- Django admin: http://localhost:8000/admin/

Create an admin and run checks:

```powershell
docker compose exec backend python manage.py createsuperuser
docker compose exec backend python manage.py check
docker compose exec backend python manage.py test store
docker compose exec backend python manage.py showmigrations
```

`seed_catalog` inserts any missing retained catalogue records and preserves existing admin-managed values:

```powershell
docker compose exec backend python manage.py seed_catalog
```

The frontend can also be opened directly, but API requests require the configured API origin to allow the page's origin in `CORS_ALLOWED_ORIGINS`.

## API outline

- `GET /api/products/?category=Hoodie` — public, priced and available products; supplier cost and supplier-hosted image references are omitted.
- `GET /api/banners/` — active banners inside their date window.
- `POST /api/auth/register/`, `/api/auth/login/`, `/api/auth/logout/` — customer accounts use email/password and expiring bearer tokens.
- `GET /api/cart/`, `POST /api/cart/items/` — server-backed customer cart.
- `POST /api/orders/` — authenticated checkout; requires an `Idempotency-Key` UUID and rechecks server prices and stock.
- `GET /api/customer/orders/` — authenticated customer order history.
- `POST /api/payments/utr/` — order owner may submit a UTR; it remains pending until staff verification.
- `POST /api/admin/orders/{order_number}/payment/` — Django staff only; `{"decision":"verify"}` or `{"decision":"reject"}` records a review audit event.
- `GET /api/admin/orders/` — Django staff only.

See [docs/API.md](docs/API.md) for response and security details.

## Deployment

Deployment preparation and exact provider steps are in [DEPLOYMENT_GUIDE.md](DEPLOYMENT_GUIDE.md). Current deployment is not verified; configure the frontend API origin and complete all critical TODOs before accepting orders. Do not accept live orders until all critical TODOs are completed and the full customer/admin flow is tested against the deployed environment.
