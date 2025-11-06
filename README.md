[![Build Stable](https://github.com/frappe/frappe_docker/actions/workflows/build_stable.yml/badge.svg)](https://github.com/frappe/frappe_docker/actions/workflows/build_stable.yml)
[![Build Develop](https://github.com/frappe/frappe_docker/actions/workflows/build_develop.yml/badge.svg)](https://github.com/frappe/frappe_docker/actions/workflows/build_develop.yml)

# Frappe on Docker (PostgreSQL 16)

This repository delivers a **clean Frappe framework environment** that runs exclusively on **PostgreSQL 16**. ERPNext is intentionally excluded so you can bootstrap benches for custom apps without unwanted dependencies or MariaDB containers.

If you need a deeper architectural overview, browse the [Getting Started Guide](docs/getting-started.md). Continue below for a step-by-step installation walkthrough that has been validated end-to-end.

## Clean installation — step by step

1. **Install prerequisites**
   - [Docker Engine](https://docs.docker.com/get-docker/) with the Compose plugin
   - [Git](https://docs.github.com/en/get-started/getting-started-with-git/set-up-git)

2. **Clone the repository**

   ```bash
   git clone https://github.com/frappe/frappe_docker.git
   cd frappe_docker
   ```

3. **Create your environment file**

   ```bash
   cp example.env .env
   ```

   Edit `.env` and provide at least these values:

   ```bash
   DB_PASSWORD="change_me"          # Required — used by the bundled PostgreSQL 16 server
   POSTGRES_DB="frappe"             # Optional — defaults to "postgres"
   POSTGRES_USER="frappe"           # Optional — defaults to "postgres"
   BENCH_GET_APPS=""                # Optional — semicolon separated bench get-app commands
   ```

   > **Tip:** Pre-install apps (for example `ekgfn_core`) by filling `BENCH_GET_APPS` with commands such as `bench get-app https://gitlab.com/kz-dev/adizit/ugq/ucssf/ekgfn_core.git --branch feature/qr_egov`.

4. **Build and start the Frappe stack (PostgreSQL only)**

   ```bash
   docker compose up --build -d
   ```

   The first run builds a local `frappe/frappe` image (Frappe framework only) before launching backend workers, the nginx frontend, Socket.IO, and a dedicated PostgreSQL 16 database. Confirm everything is healthy with `docker compose ps`.

5. **Create your first site**

   ```bash
   docker compose exec backend bench new-site site.local \
     --db-type postgres \
     --db-name site_local \
     --admin-password admin \
     --db-root-username "${POSTGRES_USER:-postgres}" \
     --db-root-password "$DB_PASSWORD"
   ```

   Replace passwords and names as desired. The command seeds PostgreSQL, creates `sites/site.local`, and registers it as the default site.

6. **(Optional) Install custom apps**

   ```bash
   docker compose exec backend bench get-app https://gitlab.com/kz-dev/adizit/ugq/ucssf/ekgfn_core.git --branch feature/qr_egov
   docker compose exec backend bench --site site.local install-app ekgfn_core
   ```

   Repeat for additional apps or populate `BENCH_GET_APPS` to automate downloads during container start-up.

7. **Access Frappe**

   - Add `site.local` to your hosts file pointing at `127.0.0.1`
   - Browse to [http://site.local:8080](http://site.local:8080)
   - Log in using the administrator password you set in step 5

When you are finished, stop the stack with `docker compose down`. Persistent data lives in Docker volumes (`sites`, `db-data`).

### Need ERPNext?

ERPNext is no longer bundled by default. Build the ERPNext stage in [`docker-bake.hcl`](docker-bake.hcl) and override `compose.yaml` manually if you require it, keeping the default stack pristine for Postgres-only benches.

## Helpful commands

```bash
# Check service health
docker compose ps

# Follow logs for a specific service
docker compose logs -f backend

# Run a PostgreSQL shell
docker compose exec db psql -U "${POSTGRES_USER:-postgres}" -d "${POSTGRES_DB:-frappe}"
```

## Documentation

### [Getting Started Guide](docs/getting-started.md)

### [PyCharm Quick Start](docs/pycharm-getting-started.md)

### [Frequently Asked Questions](https://github.com/frappe/frappe_docker/wiki/Frequently-Asked-Questions)

### [Production](#production)

- [List of containers](docs/container-setup/01-overview.md)
- [Single Compose Setup](docs/single-compose-setup.md)
- [Environment Variables](docs/container-setup/env-variables.md)
- [Single Server Example](docs/single-server-example.md)
- [Setup Options](docs/setup-options.md)
- [Site Operations](docs/site-operations.md)
- [Backup and Push Cron Job](docs/backup-and-push-cronjob.md)
- [Port Based Multi Tenancy](docs/port-based-multi-tenancy.md)
- [Migrate from multi-image setup](docs/migrate-from-multi-image-setup.md)
- [running on linux/mac](docs/setup_for_linux_mac.md)
- [TLS for local deployment](docs/tls-for-local-deployment.md)

### [Custom Images](#custom-images)

- [Custom Apps](docs/container-setup/02-build-setup.md)
- [Build Version 10 Images](docs/build-version-10-images.md)

### [Development](#development)

- [Development using containers](docs/development.md)
- [Bench Console and VSCode Debugger](docs/bench-console-and-vscode-debugger.md)
- [Connect to localhost services](docs/connect-to-localhost-services-from-containers-for-local-app-development.md)

### [Troubleshoot](docs/troubleshoot.md)

## Contributing

If you want to contribute to this repo refer to [CONTRIBUTING.md](CONTRIBUTING.md)

This repository is only for container related stuff. You also might want to contribute to:

- [Frappe framework](https://github.com/frappe/frappe#contributing),
- [ERPNext](https://github.com/frappe/erpnext#contributing),
- [Frappe Bench](https://github.com/frappe/bench).
