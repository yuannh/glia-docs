# ADR 0005: Persist conversations and cards in a shared system-of-record

- Status: Accepted
- Date: 2026-01-12

## Context

Earlier decisions established Glia’s asynchronous interaction and execution
model, including streaming semantics, a fixed SSE protocol, derived cards,
and background extraction via workers (ADR 0001–0004).

At that stage, conversations and cards were stored in memory. This enabled
rapid validation of interaction flow and failure behavior, but imposed
fundamental limitations:

- All state was lost on process restart.
- Multiple API or worker instances could not share a consistent view of system facts.
- Background retries risked duplication or corruption without a durable source of truth.
- Upcoming work (authentication, user isolation, idempotency, observability)
  requires replayable, authoritative system state.

At this point, lack of persistence became a **structural constraint** rather
than an implementation detail.

Introducing a shared system-of-record represents a qualitative shift in the
system’s capabilities, enabling durability, multi-instance correctness, and
future access control.

## Decision

Glia will persist **conversation history** and **derived cards** in a shared,
durable **system-of-record**.

Specifically:

- **PostgreSQL** is introduced as the authoritative store for:
  - `conversations`
  - `messages`
  - `cards` (derived artifacts)

- **Redis** remains in use strictly for coordination concerns
  (e.g. background job brokering) and is **not** treated as a durable source of truth.

- API and Worker processes are treated as **stateless** with respect to durable data:
  - both read from and write to the same database
  - restarts must not lose system facts
  - horizontal scaling must not introduce divergent state

- The public store interfaces remain stable:
  - `ConversationStore` and `CardStore` interfaces do not change
  - persistence is an implementation change, not an API semantic change

This decision establishes durability as a first-class architectural property
and enables the transition beyond an ephemeral, single-instance-oriented system.

## Consequences

### Positive
- Conversations and cards survive API and worker restarts.
- Multiple API and worker instances observe a single, consistent view of system facts.
- Background retries become safer because writes reconcile against durable state.
- Authentication and user-level isolation are unblocked by a reliable ownership substrate.
- The system moves closer to production-grade semantics without altering
  existing interaction contracts.

### Trade-offs
- Operational complexity increases (database dependency, migrations, backups).
- Data modeling decisions become harder to reverse than in-memory structures.
- Database availability becomes a hard dependency that must fail clearly and explicitly.
- Latency and throughput are now influenced by database performance and
  connection management.

## Alternatives Considered

- Continue using in-memory stores  
  Rejected: cannot support restarts or multi-instance correctness.

- Persist only cards while keeping conversation history ephemeral  
  Rejected: card derivation depends on replayable, authoritative message history.

- Use Redis as the system-of-record  
  Rejected: Redis durability and data modeling semantics are not aligned with
  long-lived authoritative facts.

- Event sourcing or append-only logs as the primary source of truth  
  Rejected at this stage: complexity outweighs learning and operational value.

## Validation

This decision is considered accepted when:

- Restarting the API does not lose conversations, messages, or cards.
- Restarting workers does not duplicate, wipe, or corrupt existing cards.
- API and Worker processes operate on the same persisted facts.
- The system fails fast and clearly if the database is unavailable,
  with no silent fallback to ephemeral state.

## Links

- Related ADRs:
  - `DECISIONS/0003-cards-as-derived-facts.md`
  - `DECISIONS/0004-background-card-extraction.md`
  - Architecture — Phase 2  
  This decision manifests as a system-wide constraint in `architecture/phase-2.md`.
