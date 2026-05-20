# scripts

Utility scripts for development, CI, and database operations.

## Scripts

| Script | Description |
|--------|-------------|
| `add-license-headers.sh` | Adds or verifies Elastic License 2.0 headers on Go files |
| `check-migrations.sh` | Verifies migration up/down pairs and sequential numbering |
| `check-tests.sh` | Verifies every `.go` file has a corresponding `_test.go` file |
| `check-tests_test.sh` | Self-test fixtures for `check-tests.sh` |
| `check-test-tags.sh` | Validates required test build tags and checks co-located tagged tests use approved tags (`unit`, `integration`, `chaos`, `e2e`) |
| `generate-instrumentation-map.go` | Generates streaming instrumentation map data |
| `inject_swagger_tags.py` | Post-processes generated Swagger docs to inject custom tag definitions |
| `manual-indexes.sql` | Additional database indexes for performance tuning |
| `post-deploy-audit-logs-append-only.sql` | Post-deployment script enforcing append-only audit log constraints |

### postgres/

PostgreSQL-specific initialization scripts for primary/replica setup:

| Script | Description |
|--------|-------------|
| `postgres/init-primary.sh` | Initializes the primary PostgreSQL instance with WAL replication settings |
| `postgres/init-replica.sh` | Initializes the PostgreSQL read replica |

## Usage

Most scripts are invoked through Makefile targets:

```bash
make check-tests      # Runs check-tests.sh
make check-tests-self # Runs check-tests.sh self-test fixtures
make check-test-tags  # Runs check-test-tags.sh
make check-migrations # Runs check-migrations.sh
make check-license    # Runs add-license-headers.sh --check
make generate-docs    # Uses inject_swagger_tags.py
```
