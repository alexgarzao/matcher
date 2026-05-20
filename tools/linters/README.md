# Matcher Custom Linters

This directory contains custom Go linters specific to Matcher's architectural patterns.

## Available Linters

### 1. Entity Constructor Linter (`entityconstructor`)

Enforces domain entity constructor patterns:

- Constructor functions must be named `New<EntityName>`
- First parameter must be `context.Context`
- Return type must be `(*EntityName, error)`

**Why?** DDD best practice - entities maintain invariants through validated constructors.

### 2. Observability Linter (`observability`)

Enforces tracing patterns in service methods:

- Service `Execute`/`Run`/`Handle` methods must call `NewTrackingFromContext(ctx)`
- Must create a span with `tracer.Start(ctx, "operation.name")`
- Must defer `span.End()` for proper cleanup

**Why?** Production debugging requires comprehensive tracing of all service operations.

### 3. Repository Transaction Linter (`repositorytx`)

Enforces transaction safety patterns:

- Write methods (`Create`, `Update`, `Delete`, etc.) must have `*WithTx` variants
- Non-WithTx methods should use `common.WithTenantTx` wrapper internally

**Why?** Financial data requires strict transaction safety with tenant isolation.

### 4. Goroutine Leak Linter (`goroutineleak`)

Flags packages that spawn goroutines without a `TestMain` using `goleak.VerifyTestMain`.

### 5. Determinism Linter (`determinism`)

Flags `time.Now()` and `uuid.New()` in test functions that construct entities via `New{Entity}` constructors. Runs as an advisory check under `make lint-custom`.

## Usage

### Run All Custom Linters (Warning Mode)

```bash
make lint-custom
```

This runs `entityconstructor`, `observability`, and `repositorytx`, then runs `determinism` as an advisory non-blocking check.

### Run All Custom Linters (Strict Mode)

```bash
make lint-custom-strict
```

This runs the strict analyzer set with `goroutineleak` enabled. `determinism` is not strict yet.

### Run Standalone

```bash
mkdir -p bin
cd tools && go build -o ../bin/matcherlint ./linters/matcherlint/...
cd ..

go vet -vettool=bin/matcherlint ./internal/.../domain/entities/...
go vet -vettool=bin/matcherlint ./internal/.../services/...
go vet -vettool=bin/matcherlint ./internal/.../adapters/postgres/...
```

## Adding New Linters

1. Create a new package under `tools/linters/`
2. Implement the `*analysis.Analyzer` interface
3. Add test data in `testdata/src/`
4. Register in `matcherlint/main.go`
5. Update this README

## Integration with golangci-lint

These linters can be integrated with golangci-lint using custom plugins:

```bash
# Build plugin
go build -buildmode=plugin -o matcherlint.so ./tools/linters/matcherlint

# Use with golangci-lint (requires golangci-lint with plugin support)
golangci-lint run --custom-linters=matcherlint.so
```

Note: Plugin support requires building golangci-lint from source with CGO enabled.

## Pattern Reference

See `tools/linters/*/analyzer.go` for the current pattern definitions.
