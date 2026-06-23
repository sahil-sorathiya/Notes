# 10 — Docker Compose

## 10.1 What Compose is and why it exists

Running one container by hand is fine. Running a web app + database + cache + worker, each with its own ports, volumes, env vars, networks, and start order, by typing long `docker run` commands is miserable and error-prone.

**Docker Compose** lets you define a whole multi-container application **declaratively in a single YAML file** (`compose.yaml`) and manage it with one command. It's the standard tool for **local development** and **single-host deployments**.

> **V2 note:** Compose is now a Docker CLI plugin invoked as **`docker compose`** (two words). The old standalone Python tool `docker-compose` (hyphen) is deprecated. The file can be named `compose.yaml` (preferred) or `docker-compose.yml` (still supported). Also: the top-level `version:` key is now obsolete and can be omitted.

## 10.2 A complete, annotated example

```yaml
# compose.yaml
services:
  web:
    build:
      context: .
      dockerfile: Dockerfile
      target: runtime          # build a specific multi-stage target
    image: myorg/web:dev       # name the built image
    ports:
      - "8080:3000"            # host:container
    environment:
      - NODE_ENV=development
    env_file:
      - .env                   # load vars from a file
    volumes:
      - ./src:/app/src         # bind mount source for live reload (dev)
    depends_on:
      db:
        condition: service_healthy   # wait until db is HEALTHY, not just started
    restart: unless-stopped
    networks:
      - appnet

  db:
    image: postgres:16-alpine
    environment:
      POSTGRES_USER: app
      POSTGRES_PASSWORD: secret
      POSTGRES_DB: appdb
    volumes:
      - db-data:/var/lib/postgresql/data   # named volume for persistence
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U app"]
      interval: 10s
      timeout: 5s
      retries: 5
      start_period: 10s
    networks:
      - appnet

  cache:
    image: redis:7-alpine
    networks:
      - appnet

volumes:
  db-data:                     # declare the named volume

networks:
  appnet:                      # declare the shared network
    driver: bridge
```

Everything you learned in sections 04–09 maps directly onto Compose keys: images, ports, volumes, networks, env, healthchecks, restart policies — just expressed as YAML instead of CLI flags.

## 10.3 Core lifecycle commands

```bash
docker compose up               # create & start everything (foreground, streams logs)
docker compose up -d            # detached (background)
docker compose up --build       # rebuild images first
docker compose down             # stop and remove containers + networks
docker compose down -v          # ...also remove named volumes (deletes data!)
docker compose start / stop     # start/stop without creating/removing
docker compose restart [svc]
docker compose ps               # status of this project's services
docker compose logs -f [svc]    # follow logs (all or one service)
docker compose exec web sh      # shell into a running service
docker compose run --rm web npm test   # one-off command in a NEW container
docker compose build [svc]      # build images only
docker compose pull             # pull images for services that use `image:`
docker compose config           # render & validate the final merged config
docker compose top              # processes per service
```

`up`/`down` operate on the whole **project** (by default named after the directory). Compose adds labels so it knows exactly which containers, networks, and volumes belong to that project.

`run` vs `exec`: **`exec`** runs a command in an *already running* service container; **`run`** spins up a *new, one-off* container of a service (great for migrations, tests, REPLs).

## 10.4 Build vs image

A service can either **use a prebuilt image** or **build one**:

```yaml
services:
  api:
    image: myorg/api:1.2.0      # pull/use this image
  worker:
    build: ./worker             # build from ./worker/Dockerfile
```

You can combine them: `build:` plus `image:` builds and tags the result under that image name.

## 10.5 Networking in Compose (automatic and clean)

By default, Compose creates **one user-defined bridge network** for the project and attaches every service to it. This gives you, for free, exactly the recommended setup from section 09:

- Services reach each other **by service name** (`web` connects to `db:5432`, `cache:6379`).
- No need to publish ports between services — only publish what the *outside world* needs.

You can define additional networks for segmentation (e.g. a `frontend` and a `backend` network so the DB isn't on the same network as public-facing services).

## 10.6 Volumes in Compose

- **Named volumes** are declared under the top-level `volumes:` key and referenced by services — persistent and managed by Docker.
- **Bind mounts** use a host path (`./src:/app/src`) — typically for dev.

```yaml
services:
  db:
    volumes:
      - db-data:/var/lib/postgresql/data   # named
      - ./init.sql:/docker-entrypoint-initdb.d/init.sql:ro   # bind, read-only
volumes:
  db-data:
```

`docker compose down` keeps named volumes by default; add `-v` to delete them.

## 10.7 Dependencies and startup order

`depends_on` controls **start order**, but plain `depends_on` only waits for the container to *start*, not for the app inside to be *ready*. A database container can be "started" but not yet accepting connections. Combine `depends_on` with **healthchecks** and `condition: service_healthy` so dependents wait until the dependency is truly ready:

```yaml
depends_on:
  db:
    condition: service_healthy
```

Even with this, well-written apps should still **retry their connections** on startup — orchestration order is best-effort, not a guarantee for the app's whole lifetime.

## 10.8 Environment variables and `.env`

Three layers to keep straight:

1. **`.env` file in the project dir** — Compose reads it to **interpolate `${VARS}`** *in the YAML itself* (e.g. `image: myapp:${TAG}`).
2. **`environment:`** — variables set **inside the service's container**.
3. **`env_file:`** — a file whose variables are injected **into the container**.

```yaml
services:
  web:
    image: myapp:${TAG:-latest}     # from project .env, default "latest"
    environment:
      LOG_LEVEL: ${LOG_LEVEL:-info}
    env_file:
      - ./config/web.env
```

Don't commit secret-bearing `.env` files; add them to `.gitignore` and `.dockerignore`.

## 10.9 Profiles (optional services)

Profiles let you keep optional services (debug tools, seed jobs, extra dependencies) in the same file but only start them when asked:

```yaml
services:
  pgadmin:
    image: dpage/pgadmin4
    profiles: ["debug"]
```
```bash
docker compose --profile debug up   # includes pgadmin
docker compose up                   # excludes it
```

## 10.10 Scaling services

For stateless services on a single host you can run several replicas:

```bash
docker compose up -d --scale worker=4
```

Or declaratively with `deploy.replicas`. Note: scaling a service means you generally **shouldn't publish a fixed host port** for it (port conflicts); put a reverse proxy/load balancer in front, or rely on the internal network. True multi-host scaling needs an orchestrator (section 12).

## 10.11 Multiple Compose files and overrides

Compose merges files, enabling environment-specific configuration:

- `compose.yaml` — base, shared config.
- `compose.override.yaml` — automatically merged on top (commonly dev-only settings like bind mounts).
- Explicit overrides for other environments:

```bash
docker compose -f compose.yaml -f compose.prod.yaml up -d
```

Later files override/extend earlier ones. A common pattern: base file + `override` (dev) used by default; base + `prod` used in production.

## 10.12 The `deploy:` key and `docker stack`

The `deploy:` section (replicas, resource limits, restart/update policies, placement) is **honored by Docker Swarm** via `docker stack deploy -c compose.yaml mystack`, and largely **ignored by `docker compose up`** on a single host (with a few exceptions like resource limits). So a Compose file can do double duty: local dev with `docker compose`, and cluster deploy with `docker stack` (section 12).

## 10.13 Healthchecks in Compose

Defining healthchecks (per service) gives you: visible `healthy`/`unhealthy` status, the ability to gate `depends_on`, and (in Swarm) automatic replacement of unhealthy tasks.

```yaml
healthcheck:
  test: ["CMD", "curl", "-f", "http://localhost:3000/health"]
  interval: 30s
  timeout: 3s
  retries: 3
  start_period: 20s     # grace period during slow startup before failures count
```

## 10.14 When Compose is enough (and when it isn't)

**Compose is ideal for:**
- Local development of multi-service apps.
- CI test environments.
- Simple single-host deployments.

**Compose is *not* enough when you need:**
- Multiple hosts / high availability.
- Automatic rescheduling of failed containers across nodes.
- Rolling updates, autoscaling, advanced service discovery and load balancing at scale.

For those, you move to an **orchestrator** — Swarm (which reuses your Compose file) or Kubernetes (section 12).

---
**Previous:** [09 — Networking](09-networking.md) | **Next:** [11 — Registries & Distribution](11-registries-and-distribution.md)
