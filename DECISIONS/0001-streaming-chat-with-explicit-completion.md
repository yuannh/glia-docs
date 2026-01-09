# ADR 0001: Streaming chat with explicit completion semantics

- Status: Accepted
- Date: 2026-01-06

## Context

Glia’s primary interaction surface is conversational.
Synchronous request/response patterns or implicitly terminated streams
were found to produce ambiguous client behavior, particularly when
upstream model execution fails mid-generation.

Phase 1 prioritizes non-blocking interaction and explicit system behavior
over synchronous completion guarantees.

## Decision

The `/api/chat` endpoint uses **Server-Sent Events (SSE)** with an explicit
completion signal.

A chat stream must:
- deliver output incrementally
- model errors as data rather than transport failures
- always terminate with an explicit completion event

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

- Synchronous HTTP responses (rejected: blocks interaction).
- Implicit termination via connection close (rejected: ambiguous on failure).
- WebSockets (rejected for Phase 1 due to operational complexity).

## Validation

- Streaming output is observable via `curl -N`.
- Failure cases terminate with completion rather than broken connections.

## Links

- Architecture snapshot: `architecture/phase-1.md`
