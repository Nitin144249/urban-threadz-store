# Test report

Date: 2026-11-06

## Execution results

- `docker compose run --build --rm backend python manage.py test store` — passed; 36 tests run, 36 passed, 0 failed.
- The previously failing `CustomerAccountTests.test_customer_addresses_are_private_and_only_one_is_default` passed as part of the complete suite.

## Not verified

- Migration consistency checks, `check --deploy`, and live R2 upload/download.
- Browser interaction, responsive behavior, complete checkout/payment/admin flow, deployment, production database backup/restore, and real customer flow.
