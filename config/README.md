# config

Configuration files for the Matcher service.

## Files

| File | Description |
|------|-------------|
| `.config-map.example` | Bootstrap-only settings that require a restart. Copy to a Kubernetes ConfigMap/Secret for production. Not needed for local development. |
| `seaweedfs-s3.json` | S3-compatible object storage configuration for SeaweedFS (used for export/archival features). |

## How Configuration Works

Matcher uses a **zero-config** approach:

1. **Defaults are in the binary.** All configuration has sensible defaults baked into `defaultConfig()`. Running `make dev` uses binary defaults. Running `make up` also requires `SYSTEMPLANE_SECRET_MASTER_KEY`; export it in your shell or provide it through local `config/.env` before invoking the Makefile.

2. **Env vars override defaults at startup.** Any `env:` tagged field in the `Config` struct can be overridden via environment variables.

3. **Systemplane handles runtime changes.** After startup, configuration is managed through the canonical lib-systemplane admin API (management-plane surface, intentionally excluded from the public OpenAPI spec) — no restart required for most settings:

```
GET  /system/matcher             — list all keys (inline schema metadata)
GET  /system/matcher/:key        — read a single key
PUT  /system/matcher/:key        — write a single key
```

See `github.com/LerianStudio/lib-systemplane/admin` for the full HTTP surface. The previous `/v1/system/configs[...]` paths and the `/schema`, `/history`, `/reload` sub-endpoints are not part of the current lib-systemplane admin surface.

Only bootstrap-only keys (listed in `.config-map.example`) require a restart.
