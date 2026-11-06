# Free-tier deployment guide

Prepared for Cloudflare Pages (static frontend), Render (Django), Neon (PostgreSQL), and Cloudflare R2 (object storage). No provider account was created and no deployment was performed. **The storefront is API-connected, but production deployment and the live customer flow have not been verified. The app is not ready for live orders.**

## Before creating services

1. Push this project to a private GitHub repository. The current workspace has no `.git` directory; initialize and review the files before pushing. Do not commit `.env`, credentials, database dumps, or `backend/catalog_seed.json` to a public repository.
2. Complete all critical items in [PRODUCTION_TODO.md](PRODUCTION_TODO.md), especially R2 setup, public product media, storefront configuration, policy pages, and runtime verification.
3. Confirm product photos, prices, stock, shipping/returns, legal policies, support details, and supplier fulfillment.

## 1. Create Neon PostgreSQL

1. Create a Neon account and a project in a region near the Render service.
2. Create a database and role from the Neon dashboard; retain the pooled or direct connection string securely.
3. The connection string should use `postgresql://USER:PASSWORD@HOST/DBNAME?sslmode=require`. The Django settings accept `DATABASE_URL` and require TLS for non-local hosts.
4. Do not use Render Free PostgreSQL for the permanent database. Neon Free quotas can change; check current account usage before launch.

## 2. Create the Render backend

1. In Render, create a **Web Service** from the repository.
2. Set root directory to `backend`, use `render.yaml` or enter the same settings manually.
3. Select the Free instance only for a low-traffic trial. Its idle sleep and ephemeral filesystem make it unsuitable for guaranteed always-on service or local uploads.
4. Set secret/environment values from [ENVIRONMENT_VARIABLES.md](ENVIRONMENT_VARIABLES.md). Set `DATABASE_URL` to Neon, `PAYMENT_MODE=TEST`, `DJANGO_ALLOWED_HOSTS` to the Render host, and leave `SECURE_SSL_REDIRECT=1`.
5. Set `CORS_ALLOWED_ORIGINS` and `CSRF_TRUSTED_ORIGINS` to the exact HTTPS Pages origin once created. For a custom domain, add that origin too.
6. Wait for service health at `/health/`.
7. Render Free services do not provide shell access. Before the first deploy, add temporary secrets `INITIAL_ADMIN_USERNAME`, `INITIAL_ADMIN_EMAIL`, and `INITIAL_ADMIN_PASSWORD` in the Render environment dashboard. The startup hook creates a superuser only when none exists. After the first successful deployment, remove all three variables and redeploy. The command never resets an existing admin password.

## 3. Create Cloudflare Pages

1. Connect the repository to Cloudflare Pages.
2. Keep project root at the repository root, leave build command blank, and set build output directory to `frontend`; there is no frontend build step for the current static preview.
3. Record the assigned `https://<project>.pages.dev` origin and put it in the Render CORS and CSRF variables.
4. Set the `api-base` meta value in `frontend/index.html` to the Render HTTPS origin before publishing. Add the exact Pages origin to backend CORS settings. The customer UI calls Django; the separate browser-only admin preview has been removed.

## 4. Create Cloudflare R2

1. In Cloudflare, create a Standard storage bucket for approved product/banner media.
2. Create an R2 API token restricted to that bucket. Store access key and secret only as Render environment variables.
3. Configure a public read path/custom domain only for approved media. Keep write credentials private and never put them in the frontend.
4. Set all five R2 variables from [ENVIRONMENT_VARIABLES.md](ENVIRONMENT_VARIABLES.md) on Render before deploying. Non-debug startup fails without complete R2 configuration, preventing uploads from relying on Render ephemeral storage. Existing photos are preserved under `backend/private_media_import/products/` and contain visible supplier watermarks; do not approve them for public display as-is.

## 5. Configure environment and migrate

1. Add the values described in [ENVIRONMENT_VARIABLES.md](ENVIRONMENT_VARIABLES.md) to the Render service.
2. Keep `PAYMENT_MODE=TEST` until the end-to-end flow and UPI display are reviewed. Test mode does not initiate a real UPI transfer.
3. Migrations run on service startup. To create the first admin on a free service, use the one-time environment procedure in step 2 above; do not place credentials in the repository or deployment file.
4. `seed_catalog` is insert-only and will preserve existing admin-managed fields. Run it only if you need to add missing catalogue SKUs.

## 6. Deploy and verify

1. Deploy the Render service and Pages project.
2. Verify `GET https://<backend>/health/`, `/api/products/`, `/api/banners/`, and `/admin/` over HTTPS.
3. Verify the public product response contains no supplier fields/URLs and includes only priced, available products.
4. Register a test customer and exercise sign-in, catalogue browsing, cart, checkout, cancellation/restocking, UTR submission, customer order isolation, and staff payment/fulfilment actions before accepting orders.
5. In TEST mode, create a test order, submit a test UTR, confirm it remains pending, verify it as staff, and confirm the audit entry.
6. Only after operational and security review, set LIVE mode and verify the UPI intent amount/payee using a controlled test. UTR entry alone must never mark an order paid.
7. Test mobile and desktop, cold starts, failure paths, and customer isolation. Record the live URL and results in `TEST_REPORT.md` only after actually running them.

## Local checks

```powershell
docker compose exec backend python manage.py makemigrations --check --dry-run
docker compose exec backend python manage.py migrate --check
docker compose exec backend python manage.py collectstatic --noinput
docker compose exec backend python manage.py test store
```

For production settings, run `python manage.py check --deploy` in an environment with `DJANGO_DEBUG=0`, a real secret, exact hosts, and HTTPS settings. Do not use the debug settings from local Compose for this check.

These commands have not passed in this workspace because Django dependencies are not installed and Docker is not available to this session.
