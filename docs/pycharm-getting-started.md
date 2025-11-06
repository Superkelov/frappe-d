# Running Frappe Docker from PyCharm

This quick start guide walks through the minimum commands you need to run the Frappe Docker stack from PyCharm's integrated terminal while using the built-in PostgreSQL 16 service and automatically fetching a custom app.

## 1. Prepare environment variables

1. Open the project in PyCharm and locate the built-in terminal (``Alt+F12`` / ``Option+F12``).
2. Copy the example environment file and edit it in PyCharm to match your desired configuration:

   ```bash
   cp example.env .env
   ```

3. In the new `.env` file set the database password, optional PostgreSQL username/database, and add your custom app to `BENCH_GET_APPS`. Provide Git credentials when cloning from a private host by filling `BENCH_GIT_CREDENTIALS` with `.netrc` style entries. For the app mentioned by the customer, the entry should look like this:

   ```dotenv
   DB_PASSWORD=your-strong-password
   POSTGRES_USER=postgres
   POSTGRES_DB=postgres
   BENCH_GET_APPS=--branch feature/qr_egov https://gitlab.com/kz-dev/adizit/ugq/ucssf/ekgfn_core.git
   BENCH_GIT_CREDENTIALS=machine gitlab.com login oauth2 password glpat-xxxxxxxxxxxxxxxx
   ```

   > **Tip:** PyCharm has first-class support for editing `.env` files, so you can rely on IntelliJ's "Open in Editor" action from the terminal output. Remember to keep tokens secret—PyCharm's `.env` support hides them from VCS by default.

## 2. Start the stack with PostgreSQL 16

Run the compose stack directly from the PyCharm terminal:

```bash
docker compose up --build -d
```

This command:

- builds the local `frappe/frappe` image the first time it runs so Docker Hub access is not required,
- starts the containers defined in `compose.yaml`, including PostgreSQL 16,
- automatically waits for the PostgreSQL health check before running the configurator, and
- populates the bench with only the Frappe framework so no ERPNext code ships in the container.

PyCharm will keep the terminal session open so you can track progress. When the command finishes, the core Frappe services are running in the background.

## 3. Verify site creation and install the app

1. Follow the site creation logs to ensure the containers finish the initial setup:

   ```bash
   docker compose logs -f create-site
   ```

2. Once the site is ready, confirm that your custom application is listed:

   ```bash
   docker compose exec backend bench --site ${FRAPPE_SITE_NAME_HEADER:-site1.local} list-apps
   ```

   Replace `site1.local` if you changed the default site name in your `.env` file.

3. If you need to re-run the custom app installation manually, use the same command the configurator executed:

   ```bash
   docker compose exec backend bench get-app --branch feature/qr_egov https://gitlab.com/kz-dev/adizit/ugq/ucssf/ekgfn_core.git
   ```

## 4. Stop the stack when finished

When you are done working, stop the containers from PyCharm's terminal:

```bash
docker compose down
```

Your volumes (including the PostgreSQL data directory) remain intact, so a subsequent `docker compose up` reuses the existing database.
