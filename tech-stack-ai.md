### 1a. Config Files

| Config File | Location | Config Value | What it's for | How it's used |
|---|---|---|---|---|
| `.env` | `learn-ops-api/` | `DEBUG` | Enables Django debug mode | Django reads this to show detailed error pages and disable caching in development |
| `.env` | `learn-ops-api/` | `LEARN_OPS_HOST` | PostgreSQL host | Django's `DATABASES` setting resolves the DB host via Docker's internal DNS name |
| `.env` | `learn-ops-api/` | `LEARN_OPS_PORT` | PostgreSQL port | Django connects to the DB on this port inside the Docker network |
| `.env` | `learn-ops-api/` | `LEARN_OPS_DJANGO_SECRET_KEY` | Django cryptographic secret | Used by Django to sign sessions, cookies, and CSRF tokens |
| `.env` | `learn-ops-api/` | `LEARN_OPS_CLIENT_ID` | GitHub OAuth App client ID | Passed to GitHub during OAuth login to identify this application |
| `.env` | `learn-ops-api/` | `VALKEY_HOST` | Cache host name | Django connects to this host for caching and session storage |
| `.env` | `learn-ops-api/` | `LEARN_OPS_ALLOWED_HOSTS` | Django allowed hosts whitelist | Prevents HTTP Host header attacks; Django rejects requests from unlisted hosts |
| `.env.template` | `learn-ops-api/` | `LEARN_OPS_CLIENT_ID` | Placeholder for GitHub OAuth client ID | Developers copy this file to `.env` and fill in real values without committing secrets |
| `.env.template` | `learn-ops-api/` | `LEARN_OPS_DJANGO_SECRET_KEY` | Placeholder for Django secret key | Documents that a real secret is required without leaking the actual value |
| `.env.template` | `learn-ops-api/` | `LEARN_OPS_SUPERUSER_NAME` | Placeholder for Django admin username | The entrypoint script reads this to auto-create the superuser on first run |
| `.env.template` | `learn-ops-api/` | `LEARN_OPS_SUPERUSER_PASSWORD` | Placeholder for Django admin password | Paired with the username to seed the initial admin account |
| `.env.template` | `learn-ops-api/` | `VALKEY_HOST` | Cache host (safe default) | Shows non-sensitive values can be pre-filled in a template |
| `.gitignore` | `learn-ops-api/` | `.env*` | Ignore all `.env` files | Prevents committing secrets; `!.env.template` exception keeps the template tracked |
| `.gitignore` | `learn-ops-api/` | `!.env.template` | Exception to the `.env*` rule | Explicitly re-tracks the template so new devs know what variables are required |
| `.gitignore` | `learn-ops-api/` | `*.pyc` | Ignore compiled Python bytecode | Keeps the repo clean of machine-generated files that differ per environment |
| `.gitignore` | `learn-ops-api/` | `*.sqlite3` | Ignore local SQLite databases | Prevents accidentally committing local dev DB data |
| `.gitignore` | `learn-ops-api/` | `.vscode/settings.json` | Ignore personal VS Code settings | Avoids overwriting teammates' editor preferences |
| `Dockerfile` | `learn-ops-api/` | `FROM python:3.11.11` | Base image version pin | Docker pulls this exact Python image ensuring reproducible builds |
| `Dockerfile` | `learn-ops-api/` | `PYTHONDONTWRITEBYTECODE` | Disable `.pyc` file generation | Keeps the container filesystem clean; bytecode is unnecessary in containers |
| `Dockerfile` | `learn-ops-api/` | `PYTHONUNBUFFERED` | Force stdout/stderr unbuffered | Ensures Python logs appear in `docker logs` in real time |
| `Dockerfile` | `learn-ops-api/` | `EXPOSE 8000` | Declares the container's listening port | Documents and enables port mapping for the Django server |
| `Dockerfile` | `learn-ops-api/` | `ENTRYPOINT` | Sets the container startup script | Runs migrations and seeds the superuser before launching Django |
| `nginx.conf` | `learn-ops-api/config/nginx/` | `worker_processes` | Number of Nginx worker processes | Nginx auto-detects CPU cores and spawns one worker per core |
| `nginx.conf` | `learn-ops-api/config/nginx/` | `client_max_body_size` | Max HTTP request body size | Rejects uploads above the limit, protecting against large payload attacks |
| `nginx.conf` | `learn-ops-api/config/nginx/` | `keepalive_timeout` | TCP keep-alive timeout in seconds | Allows connection reuse before closing, reducing TLS handshake overhead |
| `nginx.conf` | `learn-ops-api/config/nginx/` | `gzip` | Enable gzip compression | Compresses HTTP responses to reduce bandwidth and speed up page loads |
| `nginx.conf` | `learn-ops-api/config/nginx/` | `ssl_protocols` | Permitted TLS versions | Restricts HTTPS to allowed TLS versions; older protocols are rejected |
| `.env` | `learn-ops-client/` | `REACT_APP_API_URI` | Backend API base URL | React reads this at build time; all `fetch` calls are prefixed with this URL |
| `.env` | `learn-ops-client/` | `REACT_APP_ENV` | Current environment name | The app uses this to toggle dev-only features like verbose logging |
| `.env` | `learn-ops-client/` | `CHOKIDAR_USEPOLLING` | File-system polling for hot reload | Forces Create React App's watcher to poll instead of inotify — required in Docker/WSL |
| `.env` | `learn-ops-client/` | `GENERATE_SOURCEMAP` | Disable JS source map generation | Speeds up builds and avoids exposing internal source code in the browser |
| `.gitignore` | `learn-ops-client/` | `/node_modules` | Ignore installed npm packages | Node modules are reproducible via `npm install`; must not be committed |
| `.gitignore` | `learn-ops-client/` | `/build` | Ignore production build output | Build artifacts are generated on demand; committing them causes large noisy diffs |
| `.gitignore` | `learn-ops-client/` | `npm-debug.log*` | Ignore npm error logs | Debug logs are ephemeral per-machine files that should never be tracked |
| `Dockerfile` | `learn-ops-client/` | `FROM node:22.13.0` | Base image version pin | Locks the Node.js version so every developer and CI run uses the same runtime |
| `Dockerfile` | `learn-ops-client/` | `WORKDIR` | Sets the working directory inside the container | All subsequent `COPY`, `RUN`, and `CMD` instructions are relative to this path |
| `Dockerfile` | `learn-ops-client/` | `COPY package*.json` | Copy package manifests before source | Docker caches the `npm install` layer; source changes won't re-trigger a full install |
| `Dockerfile` | `learn-ops-client/` | `EXPOSE 3000` | Declares React dev server port | Used by Docker Compose to map host port 3000 to the container |
| `Dockerfile` | `learn-ops-client/` | `CMD` | Default container start command | Launches Create React App's development server when the container starts |
| `package.json` | `learn-ops-client/` | `version` | App semantic version | Tracks the release version; referenced in build metadata and CI pipelines |
| `package.json` | `learn-ops-client/` | `engines.node` | Required Node.js version | npm warns if the developer's local Node version doesn't match |
| `package.json` | `learn-ops-client/` | `react` | React library version | Pinned to 16.x to avoid breaking changes from React 17/18 |
| `package.json` | `learn-ops-client/` | `react-router-dom` | Client-side routing library | Provides `<Route>`, `<Link>`, and `useHistory` used throughout the SPA |
| `package.json` | `learn-ops-client/` | `tailwindcss` | Utility-first CSS framework | PostCSS processes Tailwind classes at build time, purging unused styles in production |
| `.env` | `learn-ops-infrastructure/` | `POSTGRES_DB` | Database name to create | Docker's postgres image reads this to initialize the database on first start |
| `.env` | `learn-ops-infrastructure/` | `POSTGRES_USER` | Database superuser username | The Postgres container creates this user; the Django API authenticates with it |
| `.env` | `learn-ops-infrastructure/` | `POSTGRES_PASSWORD` | Database user password | Used in the DSN connection string and by Django's DB password setting |
| `.env` | `learn-ops-infrastructure/` | `DATA_SOURCE_NAME` | PostgreSQL DSN for the exporter | The `postgres_exporter` Prometheus service uses this to scrape DB metrics |
| `docker-compose.yml` | `learn-ops-infrastructure/` | `image: postgres:16` | PostgreSQL version pin | Locks the database engine version so schema compatibility is guaranteed |
| `docker-compose.yml` | `learn-ops-infrastructure/` | `ports` (API) | Host-to-container port mapping | Exposes Django on the host so the browser can reach the API |
| `docker-compose.yml` | `learn-ops-infrastructure/` | `ports` (Prometheus) | Prometheus metrics UI port | Exposes the Prometheus web UI on the host for querying collected metrics |
| `docker-compose.yml` | `learn-ops-infrastructure/` | `ports` (Grafana) | Grafana dashboard port | Maps Grafana's internal port to a host port avoiding conflict with the React client |
| `docker-compose.yml` | `learn-ops-infrastructure/` | `cap_add: SYS_PTRACE` | Linux capability for the debugger | Required by `debugpy` to attach to the running Django process for remote debugging |
| `docker-compose.yml` | `learn-ops-infrastructure/` | `networks.external` | Pre-existing Docker network | All services join this shared network so the Valkey container is also reachable |
| `docker-compose.yml` | `learn-ops-infrastructure/valkey/` | `image: valkey/valkey` | Valkey cache image | Docker pulls this to run the cache and session store used by Django |
| `docker-compose.yml` | `learn-ops-infrastructure/valkey/` | `ports` | Valkey port mapping | Exposes the cache on the standard Redis port so Django can connect |
| `docker-compose.yml` | `learn-ops-infrastructure/valkey/` | `--save` | RDB persistence rule | Valkey saves a snapshot to disk if keys changed within the configured time window |
| `docker-compose.yml` | `learn-ops-infrastructure/valkey/` | `restart` | Container restart policy | The cache restarts after crashes but stays stopped if manually halted |
| `Makefile` | `learn-ops-infrastructure/` | `setup` | Full environment bootstrap | Runs the setup script which creates the Docker network, volumes, and builds all images |
| `Makefile` | `learn-ops-infrastructure/` | `doctor` | Environment health check | Verifies Docker, required ports, and dependencies are available before starting |
| `Makefile` | `learn-ops-infrastructure/` | `teardown` | Full environment cleanup | Stops containers, removes volumes, and deletes the Docker network |
| `Makefile` | `learn-ops-infrastructure/` | `reset` | Wipe and rebuild everything | Runs a full teardown with volume removal then rebuilds for a clean-slate environment |
| `.env` | `service-monarch/` | `GH_PAT` | GitHub Personal Access Token | Monarch uses this token to authenticate GitHub API calls |
| `.env` | `service-monarch/` | `VALKEY_HOST` | Valkey host name | Monarch connects here to cache GitHub API responses and reduce rate limit usage |
| `.env` | `service-monarch/` | `SLACK_WEBHOOK_URL` | Incoming Slack webhook URL | Monarch posts notifications to Slack by POSTing a JSON payload to this URL |
| `.env` | `service-monarch/` | `SLACK_TOKEN` | Slack Bot OAuth token | Used for Slack API calls that require authentication beyond a simple webhook |
| `.env.template` | `service-monarch/` | `GH_PAT` | Placeholder for GitHub PAT | Guides developers to generate and insert their own token without exposing a real one |
| `.env.template` | `service-monarch/` | `SLACK_WEBHOOK_URL` | Placeholder for Slack webhook | Documents the required variable so new developers know a webhook URL must be created |
| `.env.template` | `service-monarch/` | `VALKEY_PORT` | Cache port (safe default pre-filled) | Non-sensitive values are pre-filled in the template to reduce setup steps |
| `.gitignore` | `service-monarch/` | `__pycache__/` | Ignore Python bytecode cache | Machine-generated files that differ per environment; not useful in version control |
| `.gitignore` | `service-monarch/` | `.venv` | Ignore virtual environment directory | Hundreds of MB of installed packages reproducible with `pip install` |
| `.gitignore` | `service-monarch/` | `.coverage` | Ignore test coverage data file | Coverage reports are generated on-demand and shouldn't clutter git history |
| `Dockerfile` | `service-monarch/` | `FROM python:3.11-slim` | Slim Python base image | Uses the minimal Debian-based image to reduce the final container size |
| `Dockerfile` | `service-monarch/` | `EXPOSE 8080` | Prometheus metrics port | The Prometheus scraper connects here to collect Monarch's operational metrics |
| `Dockerfile` | `service-monarch/` | `EXPOSE 8081` | Log web interface port | Exposes a browser-accessible log viewer for inspecting Monarch's runtime output |
| `Dockerfile` | `service-monarch/` | `CMD` | Service entry point | Starts the Monarch Python service when the container launches |
| `docker-compose.yml` | `service-monarch/` | `container_name` | Fixed container name | Gives the container a predictable name so other services can reference it |
| `docker-compose.yml` | `service-monarch/` | `restart` | Auto-restart policy | Keeps the long-running monitor alive after crashes without manual intervention |
| `docker-compose.yml` | `service-monarch/` | `networks` | Shared Docker network | Lets Monarch reach the Valkey cache and other services by container name |
| `pytest.ini` | `learn-ops-api/` | `DJANGO_SETTINGS_MODULE` | Points pytest-django at the settings file | Without this, pytest can't bootstrap Django; every test run imports settings from this path |
| `pytest.ini` | `learn-ops-api/` | `--reuse-db` | Reuse the test database between runs | Skips dropping and re-creating the test DB, making repeated test cycles faster |
| `pytest.ini` | `learn-ops-api/` | `--nomigrations` | Skip migrations during tests | Builds the test schema directly from models, cutting suite startup time significantly |
| `pytest.ini` | `learn-ops-api/` | `testpaths` | Restrict pytest's search path | Only tests under this folder are collected; prevents scanning the entire project |
| `pytest.ini` | `learn-ops-api/` | `markers` | Custom test markers | Developers tag tests with `@pytest.mark.unit` etc. to run targeted subsets with `-m` |
