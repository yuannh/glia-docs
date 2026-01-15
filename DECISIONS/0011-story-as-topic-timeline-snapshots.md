# ADR 0011: Story as Topic Timeline via Versioned Snapshots

Status: Accepted  
Date: 2026-01-15

## Context
Glia already treats topics as the strict isolation boundary for long-running conversations.
Cards are derived facts and extraction is idempotent and atomic.
Phase 5 introduces Story as a readable narrative layer over topic messages.

Without explicit snapshot semantics, stories risk partial updates, non-deterministic merges,
and unclear lifecycle under asynchronous extraction and retries.
A stable, replay-safe story representation is required.

## Decision
Story is a readable timeline for a Topic, stored as **immutable versioned snapshots**.

- A story snapshot represents the full readable narrative for a topic at a specific message state.
- Snapshots are **append-only**; there are no patch or diff updates in Phase 5.
- New messages produce a **new snapshot version**.
- Story extraction is **idempotent** by `(topic_id, user_id, source_hash)`.
- The Story job runs **in parallel but independent** of the Card job and uses the **same** topic message `source_hash`.

## Consequences

### Positive
- Story generation is replay-safe and deterministic under retries.
- A complete narrative view is always available for any snapshot version.
- Asynchronous extraction does not risk partial story state.
- Topic isolation is preserved by construction.

### Trade-offs
- Storage cost increases due to full snapshot storage.
- Snapshot replacement is not possible without creating a new version.
- Incremental updates and fine-grained edits are deferred beyond Phase 5.

## Alternatives Considered
- Patch-based story updates  
  Rejected: complex to reason about under async retries and leads to partial state.
- Inline story generation within chat requests  
  Rejected: couples narrative generation to interaction latency and failure.
- Single mutable story document  
  Rejected: lacks replayability and versioned auditability.

## Validation / Acceptance Criteria
- Multiple story snapshots can exist for the same topic.
- Re-running story extraction for the same `(topic_id, user_id, source_hash)` creates **no** new snapshot.
- New messages produce a new snapshot version with a distinct `source_hash`.
- Story extraction and card extraction can run independently without cross-coupling or failure dependency.

## Links
- Related ADRs:
  - `DECISIONS/0009-topic-as-first-class-isolation-unit.md`
  - `DECISIONS/0010-topic-scoped-cards-with-atomic-replace.md`
- Related Architecture:
  - `architecture/foundation-v0.3.0.md`
