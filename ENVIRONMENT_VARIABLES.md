# Environment variables

Use placeholders only in committed examples. Never commit service credentials.

| Variable | Required | Purpose |
|---|---:|---|
| `DJANGO_SECRET_KEY` | Yes in production | Long random Django signing secret. Settings fail at startup if absent with debug disabled. |
| `DJANGO_DEBUG` | Yes | `0` in production; local Compose overrides this to `1`. |
| `DJANGO_ALLOWED_HOSTS` | Yes | Comma-separated exact backend hostnames. |
| `DATABASE_URL` | Production | Neon PostgreSQL connection URL with TLS. |
| `POSTGRES_DB` | Local fallback | Local database name. |
| `POSTGRES_USER` | Local fallback | Local database role. |
| `POSTGRES_PASSWORD` | Local fallback | Local database password. |
| `POSTGRES_HOST` | Local fallback | Local database host (`db` in Compose). |
| `POSTGRES_PORT` | Local fallback | PostgreSQL port (default `5432`). |
| `CORS_ALLOWED_ORIGINS` | Yes for Pages | Comma-separated exact frontend origins. |
| `CSRF_TRUSTED_ORIGINS` | If using Django admin cross-origin | Comma-separated HTTPS origins. |
| `SECURE_SSL_REDIRECT` | Production | Defaults to enabled when debug is disabled. |
| `SECURE_HSTS_SECONDS` | Production | Defaults to one year when debug is disabled; use only with confirmed HTTPS. |
| `PAYMENT_MODE` | Yes | `TEST` by default; set `LIVE` only after payment flow review. |
| `PAYMENT_UPI_ID` | Optional | Defaults to owner-provided `Q06781216@ybl`. |
| `PAYMENT_MERCHANT_NAME` | Optional | Defaults to `URBAN THREADZ`; verify bank-app payee display before live use. |
| `R2_ACCOUNT_ID` | Production | Cloudflare account identifier used for the S3-compatible R2 endpoint. |
| `R2_ACCESS_KEY_ID` | Production | Private, bucket-scoped R2 access key; never expose to browser. |
| `R2_SECRET_ACCESS_KEY` | Production | Private R2 secret key; never expose to browser. |
| `R2_BUCKET_NAME` | Production | R2 bucket for product and banner uploads. |
| `R2_PUBLIC_BASE_URL` | Production | Public HTTPS custom domain or base host for reading approved media. |

See `.env.example` for local placeholders.

When `DJANGO_DEBUG=0`, provide all five R2 values together. Startup fails if they are missing or incomplete. Product uploads accept JPG, JPEG, PNG, or WebP up to 5 MB.

The static storefront API origin is configured in the `api-base` meta tag in `frontend/index.html`; set it to the HTTPS backend origin before publishing Pages. Add the exact Pages origin to `CORS_ALLOWED_ORIGINS`.
