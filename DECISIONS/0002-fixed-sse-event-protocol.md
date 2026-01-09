# ADR 0002: Fixed SSE event protocol for chat streaming

- Status: Accepted
- Date: 2026-01-07

## Context

As streaming behavior stabilized, ad-hoc event types and payload shapes
began to emerge. Without a fixed protocol, client implementations risk
divergence and future-breaking changes.

A minimal and stable event contract is treated as a first-class system
constraint.

## Decision

The SSE protocol for `/api/chat` is fixed to **four event types only**:

- `token` — incremental text output
- `card` — optional derived artifacts
- `error` — normalized error information
- `done` — explicit stream termination

No additional event types are permitted.
All payload conventions are documented in a single canonical protocol.

## Consequences

### Positive
- Client state machines remain simple and stable.
- New features must compose within existing event types.
- Debug and experimental behavior can be gated without protocol changes.

### Trade-offs
- Expressiveness is intentionally constrained.
- Additional semantics must be encoded within payloads.

## Alternatives Considered

- Allowing arbitrary event types  
  Rejected: protocol drift and client divergence.

- Versioned protocols  
  Rejected: increased coordination and compatibility overhead.

- Encoding semantics in transport behavior  
  Rejected: non-observable and ambiguous failure modes.

## Validation

- All chat streams emit only the four defined event types.
- Error cases emit `error` followed by `done`.

## Links

- Architecture snapshot: `architecture/phase-1.md`
