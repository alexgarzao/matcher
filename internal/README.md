# internal

Core application code organized as bounded contexts plus supporting packages following Domain-Driven Design (DDD) with Hexagonal Architecture.

## Bounded Contexts

| Context | Description |
|---------|-------------|
| [configuration](configuration/) | Reconciliation contexts, sources, field maps, match rules, fee schedules/rules, scheduling |
| [discovery](discovery/) | External data source discovery, schema detection, and extraction management |
| [ingestion](ingestion/) | File parsing (CSV/JSON/XML), normalization, deduplication, and transaction import |
| [matching](matching/) | Match orchestration, rule execution, fee verification, confidence scoring, adjustments |
| [exception](exception/) | Exception lifecycle, disputes, evidence tracking, resolution workflows, bulk operations |
| [governance](governance/) | Immutable audit logs, hash chain verification, actor mapping, archival |
| [reporting](reporting/) | Dashboard analytics, export jobs (CSV/PDF), streaming reports, caching |

## Supporting Packages

| Package | Description |
|---------|-------------|
| [auth](auth/) | Authentication, authorization, and multi-tenancy middleware |
| [bootstrap](bootstrap/) | Service initialization, dependency wiring, systemplane, and lifecycle management |
| [shared](shared/) | Shared kernel with cross-context domain objects, ports, bridge adapters, fee engine, and infrastructure helpers |
| [streaming](streaming/) | lib-streaming catalog, producer bootstrap, outbox relay wiring, and manifest support |
| [testutil](testutil/) | Shared test utilities and helpers |

Outbox is a cross-cutting infrastructure concern wired through `internal/bootstrap/outbox_wiring.go` and `lib-commons/v5/commons/outbox`; it is not a bounded context.

## Architecture

Each bounded context follows the hexagonal structure:

```
{context}/
├── adapters/        # Infrastructure implementations (HTTP, PostgreSQL, Redis, RabbitMQ)
├── domain/          # Entities, value objects, domain services, and business rules
├── ports/           # Interfaces for external dependencies
└── services/        # Use cases split into command/ (writes), query/ (reads), and worker/ (background)
```

Contexts communicate through well-defined ports and shared domain objects in `internal/shared/`. Cross-context bridge adapters live in `shared/adapters/cross/`. Direct imports between bounded contexts are blocked by `depguard` linter rules.
