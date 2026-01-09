# ADR 0001: SSE event protocol with non-breaking error semantics

- Status: Accepted
- Date: 2026-01-07

## Context

Glia's `/api/chat` is streamed over SSE. Early versions could fail mid-stream when the LLM provider errors,
causing the client to see a broken connection (e.g., curl(18)) and leaving the UI in an undefined state.

We need a minimal, stable event protocol that:
- is easy for the client to implement
- is resilient to upstream provider failures
- supports incremental outputs now, and richer payloads later

## Decision

Standardize SSE events emitted by `/api/chat` to exactly four types:

- `token`
- `card`
- `error`
- `done`

Additionally, the stream must **always terminate with `event: done`**, including error cases.
On any exception, the server emits:

1) `event: error` with a normalized payload  
2) `event: done`  

Payload conventions:
- `card`: `{ "card": { ... } }`
- `error`: `{ "message": "...", "code": "..." }`

The canonical protocol definition lives in `app/core/protocol.md`.

## Consequences

### Positive
- Clients have a simple, stable state machine (`token|card|error|done`).
- Errors become first-class events rather than connection failures.
- Streaming is resilient: LLM failures do not break the transport layer.

### Negative / Trade-offs
- The server must normalize diverse upstream errors into a consistent `{message, code}` shape.
- Clients must treat `done` as the only terminal signal (not connection close).

### Follow-ups
- Define optional `event_id`/cursor semantics if we need robust reconnect in later phases.
- Keep `card` as optional (debug/grey release) without changing protocol types.

## Alternatives Considered

- Rely on connection close as termination: rejected (ambiguous and fragile).
- Use WebSocket instead of SSE: rejected for Phase 1 due to increased operational complexity.
- Emit many event types (e.g., start/progress/debug): rejected to keep the protocol minimal.

## Validation / Acceptance Criteria

- Success path:
  - `curl -N -X POST http://localhost:8000/api/chat ...`
  - Observes `event: token` repeated, followed by `event: done`

- Failure path (intentional fault injection, wrong model/key):
  - Observes `event: error` then `event: done`
  - No curl connection failure caused by abrupt termination

## Links

- Protocol: `app/core/protocol.md`
- Key implementation: `app/services/chat_stream.py`
- Related architecture snapshot: `architecture/phase-1.md`
