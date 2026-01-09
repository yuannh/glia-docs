# ADR 0004: Background card extraction via dedicated workers

- Status: Accepted
- Date: 2026-01-09

## Context

Executing card extraction within the API process introduced resource
contention and coupled background cognition to user-facing interaction.

Clear separation between user-facing interaction and long-running
background processing is treated as a first-class system constraint.

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

- API-bound background tasks  
  Rejected: shared resource contention and unclear failure isolation.

- Synchronous extraction before completion  
  Rejected: direct impact on interaction latency and user experience.

- Heavy orchestration frameworks  
  Rejected: operational and conceptual overhead disproportionate to system needs.

## Validation

- Chat streams complete without waiting for extraction.
- Cards appear asynchronously via query.
- Extraction success and failure are observable independently.

## Links

- Architecture snapshot: `architecture/phase-1.md`
