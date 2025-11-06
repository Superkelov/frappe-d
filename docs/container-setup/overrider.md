Overrides extend the base compose.yaml with additional services or modify existing behavior. Include them in your compose command using multiple -f flags.

```bash
docker compose -f compose.yaml -f overrides/compose.postgres.yaml -f overrides/compose.redis.yaml config > compose.custom.yaml
```

> **Database note:** The default stack already bundles PostgreSQL 16. Only use the overrides below if you need to expose that database externally or if you are migrating an older MariaDB deployment.

| Overrider                    | Purpose                                                       | Additional Info                                                      |
| ---------------------------- | ------------------------------------------------------------- | -------------------------------------------------------------------- |
| **PostgreSQL**               |                                                               |                                                                      |
| compose.postgres.yaml        | Override PostgreSQL defaults (ports, user, database name)     | Set `DB_PASSWORD` and optional `POSTGRES_*` variables                |
| **Legacy MariaDB (deprecated)** |                                                           |                                                                      |
| compose.mariadb.yaml         | Bring back a MariaDB database service                         | Provide `DB_PASSWORD`; intended only for historical compatibility    |
| compose.mariadb-secrets.yaml | Same as above but reads the password from a Docker secret     | Set `DB_PASSWORD_SECRETS_FILE` to the path of your secret file       |
| compose.mariadb-shared.yaml  | Expose the legacy MariaDB service on a shared Docker network  | Provide `DB_PASSWORD`; intended only for historical compatibility    |
| **Proxy**                      |                                                                                                                                                                     |                                                                                                       |
| compose.noproxy.yaml           | Exposes the application directly on port `:8080` without a reverse proxy                                                                                            |                                                                                                       |
| compose.proxy.yaml             | Uses Traefik as HTTP reverse proxy on port `:80`                                                                                                                    | You can change the published port by setting `HTTP_PUBLISH_PORT`                                      |
| compose.https.yaml             | Uses Traefik as HTTPS reverse proxy on Port `:443` with automatic HTTP-to-HTTPS redirect                                                                            | `SITES` and `LETSENCRYPT_EMAIL` must be set. `HTTP_PUBLISH_PORT` and `HTTPS_PUBLISH_PORT` can be set. |
| **Redis**                      |                                                                                                                                                                     |                                                                                                       |
| compose.redis.yaml             | Adds Redis service for caching and background job queuing                                                                                                           |
| **TBD**                        | **The following overrides are available but lack documentation. If you use them and understand their purpose, please consider contributing to this documentation.** |
| compose.backup-cron.yaml       |                                                                                                                                                                     |                                                                                                       |
| compose.custom-domain-ssl.yaml |                                                                                                                                                                     |                                                                                                       |
| compose.custom-domain.yaml     |                                                                                                                                                                     |                                                                                                       |
| compose.multi-bench-ssl.yaml   |                                                                                                                                                                     |                                                                                                       |
| compose.multi-bench.yaml       |                                                                                                                                                                     |                                                                                                       |
| compose.traefik-ssl.yaml       |                                                                                                                                                                     |                                                                                                       |
| compose.traefik.yaml           |                                                                                                                                                                     |                                                                                                       |
