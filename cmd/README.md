# cmd

Application entry points for the Matcher service.

## Binaries

### matcher

The primary service binary. Initializes infrastructure, wires dependencies, and starts the HTTP server with the reconciliation engine.

```bash
go run ./cmd/matcher
# or
make dev    # with live reload
make build  # produces bin/matcher
```

### health-probe

A lightweight Kubernetes health probe binary for liveness and readiness checks. Used in container orchestration environments where `curl`/`wget` are unavailable.

```bash
go run ./cmd/health-probe
```

### generate-casdoor

Generates Casdoor RBAC seed data from Matcher's auth catalog.

```bash
go run ./cmd/generate-casdoor --output config/casdoor/init_data.json
# or
make generate-casdoor
```

### migration-preflight

Validates migration safety before the `migrate` CLI applies `up`, `down`, or `goto` actions.

```bash
DATABASE_URL="$DATABASE_URL" go run ./cmd/migration-preflight --action up
DATABASE_URL="$DATABASE_URL" go run ./cmd/migration-preflight --action down
DATABASE_URL="$DATABASE_URL" go run ./cmd/migration-preflight --action goto --target 32
```
