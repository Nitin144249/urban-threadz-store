# URBAN THREADZ project audit

Audit date: 2026-11-06

## Architecture and retained data

- Static HTML/CSS/JavaScript storefront in `frontend/`; Django REST Framework API and Django admin in `backend/`; PostgreSQL database; 37 retained catalogue SKUs in `backend/catalog_seed.json`.
- Existing migration history and database were preserved. No catalogue rows were reset or overwritten.
- The workspace has no `.git` directory. Django is not installed in the current host Python environment.

## Implemented customer and commerce flows

- The storefront calls the Django API for product listing/search/detail, customer account, cart, checkout, order history, cancellation, and UTR submission. Browser-only demo OTP, localStorage checkout/cart/orders, and fake admin/payment flows were removed.
- Customer auth uses email/password and JWT access/refresh tokens. Frontend tokens live in tab-scoped session storage. API auth, login/register throttles, profile and private address routes are present.
- Checkout is authenticated and idempotent. Server-calculated prices and row-locked known stock are used; successful orders decrement stock and write adjustment records.
- Customers can only view their own orders. Eligible unpaid pending orders can be cancelled; inventory restoration and status changes are audited. Pending UTR or paid orders cannot be customer-cancelled. Django admin has payment review and staged fulfilment actions.
- UTRs remain pending until staff review; duplicate references are rejected. Test mode is the default.
- Product gallery uploads are managed through Django admin. JPG/JPEG/PNG/WebP files up to 5 MB and 6000 pixels per side are accepted and only approved images are returned publicly. Cloudflare R2 is configured as media storage when all required R2 environment values are set; non-debug startup fails without them.
- Public product API omits supplier IDs, URLs, supplier cost, and supplier-hosted image URLs. The product seed remains server-side.

## Remaining launch blockers

- The API base URL in `frontend/index.html` must be changed from localhost and exact Pages/Render origins configured in CORS/CSRF settings.
- Provider accounts, Neon database, Render service, Pages site, R2 bucket/public media domain, and initial admin credentials need external setup. No deployment has been performed.
- Current catalogue prices, inventory, descriptions, sizes, and approved product photos require business/admin review. Existing ten user-supplied photos are preserved under `backend/private_media_import/products/`, outside the Pages output. Their visible supplier watermarks mean they must not be uploaded or approved as public product media as-is.
- Password reset delivery, customer notifications, policy/contact pages, backup restore testing, monitoring/error reporting, and deployment security checks remain.
- Storefront behavior and Django/PostgreSQL behavior have not been runtime-tested. Cancellation/restock and idempotency logic need full database test execution.

## Verification

- `node --check frontend/storefront.js`: passed.
- Python AST syntax parse: passed for 29 backend files; HTML parsing passed for `frontend/index.html`.
- `python backend/manage.py test store`: unable to start because Django is missing. The suite has 36 test methods; 0 executed, 0 passed, 0 failed, 36 not run.
- No claim of production readiness is made. See `PRODUCTION_TODO.md`, `SECURITY_CHECKLIST.md`, `DEPLOYMENT_GUIDE.md`, and `TEST_REPORT.md`.
