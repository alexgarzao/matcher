# migrations

PostgreSQL schema migrations managed with [golang-migrate](https://github.com/golang-migrate/migrate). Currently at **32 migrations** (000001 through 000032).

## Naming Convention

Migrations use sequential versioning with descriptive names:

```
000001_init_schema.up.sql
000001_init_schema.down.sql
000002_fee_schedules.up.sql
000002_fee_schedules.down.sql
```

Every migration must have both `.up.sql` and `.down.sql` files.

## Current Migrations

| # | Name | Description |
|---|------|-------------|
| 000001 | init_schema | Initial database schema |
| 000002 | fee_schedules | Fee schedule tables |
| 000003 | disputes | Dispute management tables |
| 000004 | nullable_match_group_rule_id | Make match group rule ID nullable |
| 000005 | exception_comments | Exception comment tables |
| 000006 | auto_match_and_scheduling | Auto-match trigger and scheduling support |
| 000007 | exception_pending_resolution | Exception pending resolution status |
| 000008 | match_group_status_revoked | Revoked status for match groups |
| 000009 | context_status_draft_archived | Draft and archived context statuses |
| 000010 | unique_context_name | Unique constraint on context names |
| 000011 | audit_log_changes_json | JSON format for audit log changes |
| 000012 | ensure_fee_normalization_column | Fee normalization column |
| 000013 | fetcher_discovery | Discovery context tables (fetcher connections, schemas) |
| 000014 | add_fetcher_source_type | Fetcher source type support |
| 000015 | extraction_requests_fetcher_conn_idx_fk | Extraction request indexes and foreign keys |
| 000016 | fee_rules | Fee rule tables |
| 000017 | add_source_side_to_reconciliation_sources | Source side column |
| 000018 | enforce_source_side_not_null | Enforce non-null source side |
| 000019 | drop_legacy_source_fee_schedule | Remove legacy fee schedule from sources |
| 000020 | systemplane_key_renames | Rename runtime/systemplane keys safely |
| 000021 | external_system_to_varchar | Convert external system fields to varchar |
| 000022 | remove_rate_unify_fee_schedule | Remove legacy rate schema and make fee schedules authoritative |
| 000023 | fetcher_connection_schema_username | Add Fetcher connection schema username support |
| 000024 | fetcher_bridge_indexes | Add Fetcher bridge indexes |
| 000025 | bridge_readiness_dashboard_indexes | Add bridge readiness dashboard indexes |
| 000026 | bridge_failure_semantics | Add bridge failure semantics |
| 000027 | custody_deletion_marker | Add custody deletion marker |
| 000028 | tighten_bridge_eligibility_index | Tighten bridge eligibility index |
| 000029 | reindex_bridge_semantics_concurrently | Reindex bridge semantics concurrently |
| 000030 | drop_reclassified_bootstrap_keys | Drop reclassified bootstrap keys |
| 000031 | audit_logs_tenant_entity_index | Add audit logs tenant/entity index |
| 000032 | fetcher_source_connection_unique | Add unique Fetcher source connection constraint |

## Commands

```bash
make migrate-up                          # Apply all pending migrations
make migrate-down                        # Rollback the last migration
make migrate-to VERSION=15               # Migrate to a specific version
make migrate-create NAME=add_feature     # Create a new migration pair
make migrate-version                     # Show current migration version
make migrate-force VERSION=15            # Force version after manual dirty-state repair; does not run SQL
```

## Guidelines

- Always test the forward migration path before merging.
- If a migration is intentionally irreversible, document it clearly and test the blocked rollback/preflight behavior instead of forcing a fake down/up cycle.
- Add indexes for new foreign keys and filter columns.
- Never modify existing migration files after they have been applied.
- Audit tables are append-only: never add UPDATE or DELETE operations.
- Production rollout and rollback procedures are not documented in this repository yet; add them before referencing a production migration guide.
