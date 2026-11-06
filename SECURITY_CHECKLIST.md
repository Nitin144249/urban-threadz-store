# Security checklist

Audit date: 2026-11-06. This is a status record, not a security certification.

## Implemented or present

- [x] Public DRF product serializer excludes supplier cost, source IDs, supplier URLs, and supplier image URL.
- [x] Public product listing requires active product, available stock status, and a selling price.
- [x] Order API calculates line totals from database product prices inside a transaction.
- [x] Admin order listing and payment review require Django staff permissions.
- [x] UTR is unique when non-empty and payment stays pending until staff verification.
- [x] Payment submission and staff review produce audit rows.
- [x] Production settings require `DJANGO_SECRET_KEY` when debug is off, default debug is off, and host/CORS values are explicit.
- [x] HTTPS proxy, secure session/CSRF cookies, HSTS, content-type sniffing, and frame protection are configured in non-debug mode.
- [x] Sensitive API routes receive anonymous-request throttling.
- [x] Initial Render admin can be created via one-time environment secrets; existing superusers are never reset.
- [x] Customer passwords use Django hashing/validation; API bearer access tokens expire after ten minutes, refresh tokens rotate and are blacklisted at logout.
- [x] Profile and address endpoints scope reads and changes to the authenticated customer.
- [x] Checkout locks product rows, checks known stock, decrements stock transactionally, and requires a per-customer idempotency key.
- [x] Customer order list/detail and UTR submission are restricted to the order owner.

## Remaining launch checks

- [x] Require authenticated customer accounts for checkout and UTR submission; attach orders to users and scope order lookup.
- [x] Remove browser-only OTP, customer checkout, and localStorage admin behavior from the storefront.
- [ ] Set exact production hosts, CORS and CSRF origins; verify cookie/session behavior in target browsers.
- [x] Add anonymous throttling to registration and login.
- [ ] Add password reset delivery and throttling when configured.
- [x] Restore stock safely on eligible unpaid cancellation and audit customer/staff status changes.
- [x] Validate image content through Django ImageField, restrict file extensions and uploads to 5 MB, and expose only staff-approved images from configured R2 storage. Image dimensions are limited to 6000 pixels per side.
- [x] Add order status and stock adjustment audit history for fulfilment changes and cancellations. Product change audit beyond stock adjustments and settings audit remain.
- [ ] Run dependency vulnerability and Django deployment checks against the actual production environment.
- [ ] Verify backups, restore procedure, monitoring, and customer-data retention.
- [ ] Review privacy policy, terms, shipping/returns, support contact, and local legal/tax obligations with the business owner.

## Known security boundaries

- The storefront is API-connected; do not direct customers to it until the production API origin, CORS settings, R2 domain, and deployed flow are verified.
- Django admin is staff-protected. Customer-facing pages expose no admin payment controls.
- R2 storage and product image approval are implemented in code but require bucket credentials, a public read domain, and approved uploads.
