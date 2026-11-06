# Free hosting limitations

Provider quotas can change; these notes were checked on 2026-11-06. Review current provider dashboards before relying on them.

- **Render Free Web Service:** sleeps after 15 minutes without inbound activity and can take about one minute to wake. Its filesystem is ephemeral, so uploaded files disappear after restart/redeploy. Free instance hours are shared monthly limits. See [Render free services](https://render.com/docs/free).
- **Neon Free:** quotas are per project and subject to change. Neon announced 1 GB database storage per project and 100 CU-hours per project/month in October 2026. Check the [current Neon plans](https://neon.com/blog/neon-free-plan-1-gb-per-project) and dashboard usage.
- **Cloudflare R2 Standard:** current free allowance lists 10 GB-month storage, 1 million Class A operations and 10 million Class B operations monthly; internet egress is free. Infrequent Access is not covered. See [R2 pricing](https://developers.cloudflare.com/r2/pricing/).
- **Cloudflare Pages Free:** static asset requests are free/unlimited; the current limits page lists 500 builds/month and 20,000 files per site, with 25 MiB max per file. See [Pages limits](https://developers.cloudflare.com/pages/platform/limits/) and [Pages pricing](https://developers.cloudflare.com/pages/functions/pricing/).

Free services can sleep, hit quotas, or change terms. This architecture is a low-cost trial path, not guaranteed always-on production hosting. No paid service or billing setup was selected or activated.
