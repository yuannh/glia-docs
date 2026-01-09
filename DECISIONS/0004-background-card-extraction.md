# ADR 0004: Background card extraction via dedicated workers

- Status: Accepted
- Date: 2026-01-10

## Context

Executing card extraction within the API process introduced resource
contention and coupled background cognition to user-facing interaction.

Phase 1 requires clear separation between interaction and long-running
processing.

## Decision

Card extraction is executed asynchronously by dedicated worker processes.

System roles are defined as:
- API: handles HTTP, streaming, and conversation persistence
- Worker: performs card extraction and post-processing
- Shared coordination layer: mediates task execution

Extraction is enqueued after chat completion and never blocks streaming.

## Consequences

### Positive
- Chat latency is unaffected by extraction cost.
- Extraction failures are isolated and observable.
- API and background workloads scale independently.

### Trade-offs
- Additional operational components are introduced.
- Tasks must be idempotent and retry-safe.

## Alternatives Considered

- API-bound background tasks (rejected: shared resource contention).
- Synchronous extraction before completion (rejected: UX impact).
- Heavy orchestration frameworks (rejected for Phase 1 complexity).

## Validation

- Chat streams complete immediately.
- Cards appear asynchronously via query.
- Extraction success and failure are observable independently.

## Links

- Architecture snapshot: `architecture/phase-1.md`
