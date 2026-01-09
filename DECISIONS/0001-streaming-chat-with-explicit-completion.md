# ADR 0001: Streaming chat with explicit completion semantics

- Status: Accepted
- Date: 2026-01-06

## Context

Glia’s primary interaction surface is conversational.
Early exploration showed that synchronous request/response patterns or
implicitly terminated streams lead to ambiguous client behavior,
particularly when upstream model execution fails mid-generation.

When completion is inferred from connection closure, clients cannot
reliably distinguish between:
- successful completion,
- partial output due to failure,
- or transport-level interruption.

Non-blocking interaction and explicit completion semantics are treated
as first-class system constraints.

## Decision

The `/api/chat` endpoint uses **Server-Sent Events (SSE)** with an explicit
completion signal.

A chat stream must:
- deliver output incrementally,
- model errors as data rather than transport failures,
- always terminate with an explicit completion event.

Completion is never inferred from connection closure.

## Consequences

### Positive
- Clients observe a deterministic lifecycle for every request.
- Partial results remain visible even when generation fails.
- Transport stability is decoupled from upstream model reliability.

### Trade-offs
- The server must manage explicit stream termination.
- Clients must implement a small, explicit state machine.

## Alternatives Considered

- Synchronous HTTP responses  
  Rejected: blocks interaction and obscures latency.

- Implicit termination via connection close  
  Rejected: ambiguous in the presence of partial failures.

- WebSockets  
  Rejected: increased operational complexity and connection lifecycle management
  relative to the system’s needs at this stage.

## Validation

- Streaming output is observable via `curl -N`.
- Failure cases terminate with an explicit completion event rather than
  a broken connection.

## Links

- Architecture snapshot: `architecture/phase-1.md`
