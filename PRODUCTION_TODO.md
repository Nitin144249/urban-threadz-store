# Production TODO

Status reflects the implementation review on 2026-11-06. Checked items are present in code; unchecked items remain work, runtime verification, or external setup.

## Data and catalogue

- [x] Preserve the existing 37 catalogue records and migration history.
- [x] Keep selling prices blank until an administrator sets them.
- [x] Make catalogue seeding preserve existing admin-managed values.
- [x] Exclude supplier cost from the public DRF product serializer.
- [x] Make the storefront fetch catalogue data from the backend.
- [x] Add admin-managed stock quantities, transactional stock decrement under row locks, and adjustment history.
- [x] Add approved product gallery images, upload type/size validation, and configurable Cloudflare R2 storage.
- [ ] Require an approved persistent product image before publication; currently missing images show a placeholder.
- [x] Change seed command so repeat runs cannot overwrite operator-managed fields.

## Customer and commerce

- [x] Add backend registration, login/logout, profile, and address management using expiring bearer tokens.
- [ ] Add password reset delivery.
- [x] Require authenticated checkout and enforce customer-specific order access.
- [x] Connect storefront cart, checkout, account, and order history to the API.
- [ ] Add shipping/discount configuration and authoritative server-side totals.
- [x] Add checkout idempotency and stock validation.
- [x] Add product detail endpoint and API category/search/sort filters.
- [ ] Add policy/contact pages and final customer-facing content.

## Manual UPI payment and orders

- [x] Implement backend UPI intent generation using configured merchant identity and exact server-calculated amount (LIVE mode only).
- [x] Accept and uniquely constrain UTR references; keep payment pending until staff review.
- [x] Add payment audit records and staff-only verify/reject actions.
- [x] Add staff fulfilment status transitions and status audit history; order tracking number remains manually managed.
- [x] Add an explicit TEST/LIVE mode with safe test behavior and no fake paid state.

## Admin and security

- [x] Protect the existing admin-order API with Django staff permissions.
- [ ] Add dashboard metrics and reports; core product/banner, stock, payment, and status tools already exist in Django admin.
- [x] Require environment-provided production secrets and fail closed when missing.
- [x] Configure HTTPS, secure cookies, CSRF/CORS origins, host validation, and security headers from environment-controlled settings; set exact production values during deploy.
- [x] Add scoped registration/login throttling and API error handling. Pagination remains to be configured as catalogue/order volume grows.
- [ ] Verify admin field-level access for customer data, supplier cost, and payment review.
- [ ] Run dependency and application security checks.

## Hosting and operations

- [ ] Configure Render production WSGI service and health check.
- [ ] Configure Neon PostgreSQL URL, backups, and migration procedure.
- [ ] Set the storefront API origin and exact CORS/CSRF origins in the deployment environment.
- [ ] Configure Cloudflare R2 with private credentials and safe public image delivery.
- [x] Add static-file handling and deployment build/start/health configuration.
- [x] Add R2 media storage configuration and approved image uploads. Monitoring/logging remain.
- [ ] Create provider accounts and configure domain, credentials, and policies.
- [ ] Deploy and verify the live customer flow before accepting orders.

## Quality and launch

- [ ] Install backend dependencies and run all backend tests/migration checks; current environment cannot access Django or Docker.
- [ ] Execute browser/responsive checks against a running API.
- [ ] Exercise registration through customer order status and admin verification end-to-end against a running deployment.
- [ ] Approve all product prices, photos, stock, sizes, copy, policies, and supplier fulfillment.
- [ ] Verify legal business details, support contact, shipping/returns, and privacy terms.
- [ ] Decide fulfillment/cancellation/refund operations and customer notifications.
