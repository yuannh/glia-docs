# ADR 0010: Scope Cards to Topic and Enforce Atomic Replace Semantics

Status: Accepted  
Date: 2026-01-14

## Context

Cards are derived artifacts produced from conversation messages. As users chat over time and across themes, cards must remain:
- Interpretable (not mixed across unrelated themes)
- Consistent (no partial writes or duplicated results)
- Recoverable (safe to retry extraction)

Previously, conversation-scoped card storage causes semantic contamination when multiple topics exist inside one conversation. Additionally, extraction may run asynchronously, meaning:
- Multiple jobs can be triggered for the same logical input.
- Failures and retries can lead to duplicate writes.
- Partial writes can leave the system in an inconsistent state.

To support long-running usage and reliable derived data, cards must:
1) be isolated by topic, and  
2) be written with transactional “replace” semantics, and  
3) be coupled to idempotent extraction runs.


## Decision

Adopt **topic-scoped cards** with **atomic replace** semantics.

- Cards are stored and retrieved primarily by (user_id, topic_id).
- Each extraction produces a complete card set for the topic input and replaces the previous set atomically:
  - delete existing cards for that (user_id, topic_id)
  - insert the new set
  - commit as a single transaction
- Extraction runs are recorded and deduplicated by a stable input hash (source_hash) scoped to topic:
  - uniqueness is enforced for (topic_id, user_id, source_hash)
  - repeated jobs for the same input must deduplicate rather than rewrite
- Compatibility behavior may remain temporarily for clients that query by conversation_id,
  but the canonical semantics are topic-scoped.


## Consequences

### Positive
- Cards for different topics cannot contaminate each other.
- Atomic replace eliminates half-written or mixed-generation states.
- Idempotent runs prevent duplicate writes under retries or multiple job triggers.
- The system remains safe under async execution, crashes, and restarts.
- Cards become a stable foundation for subsequent evolutions:
  entity deduplication, story versioning, and artifact linking.

### Trade-offs
- Replace semantics can overwrite previous cards even if only a subset changed.
- Atomic replace may be less efficient than incremental updates (addressed in later phases with entity-level evolution).
- Requires schema support for topic-scoping and run-level deduplication.


## Alternatives Considered

- Conversation-scoped cards  
  Rejected: violates topic isolation and leads to semantic mixing over time.

- Append-only cards without replace  
  Rejected: accumulates duplicates and requires additional reconciliation logic.

- Incremental update (“patch cards”) instead of replace  
  Rejected for this stage: increases complexity, makes correctness harder to guarantee, and complicates idempotency.
  Incremental evolution is deferred to a later phase once entities exist.

- Best-effort deduplication without a unique constraint  
  Rejected: unsafe under concurrency, retries, and multi-worker execution.


## Validation / Acceptance Criteria

- Two topics within the same conversation produce two isolated card sets.
- Re-running extraction for the same topic input does not create additional runs and does not rewrite cards.
- Under asynchronous extraction, cards never appear in partial states for a topic.
- Querying cards by topic_id returns only the cards derived from that topic’s messages.


## Links

- **Related ADRs**
  - `DECISIONS/0003-cards-as-derived-facts.md`
  - `DECISIONS/0007-make-card-extraction-idempotent-and-atomic.md`
  - `DECISIONS/0009-topic-as-first-class-isolation-unit.md`

- **Related Architecture**
  This decision is reflected as a core system constraint in  
  `architecture/foundation-v0.2.0.md`.
