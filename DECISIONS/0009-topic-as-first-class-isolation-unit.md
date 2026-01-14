# ADR 0009: Treat Topic as the First-Class Isolation Unit for Long-Running Conversations

Status: Accepted  
Date: 2026-01-14

## Context

Glia is designed for long-running use where a user can continue chatting in a single conversation UI over days or weeks.

In this setting, a single “conversation” often contains multiple independent themes (e.g., family, work, travel). If all derived artifacts (cards, stories, future entities) are scoped only to the conversation:

- Derived outputs accumulate noise and become harder to interpret.
- Unrelated themes contaminate each other (semantic drift).
- The system cannot reliably summarize or evolve knowledge per theme.
- Future capabilities (story versioning, entity evolution, cross-time threads) lack a stable unit of separation.

The system therefore requires an explicit isolation boundary that is:
- Stable over time
- Addressable by API
- Compatible with a single conversation UI
- Suitable as the scope for derived-data production and retrieval


## Decision

Introduce **Topic** as a first-class isolation unit.

- A conversation may contain multiple topics.
- Each message is associated with exactly one topic (initially nullable for compatibility, later required).
- Topic routing is deterministic and explainable:
  - Default to the current active topic for the user within a conversation.
  - Create a new topic when strong “switch topic” hints are detected.
  - Respect an explicit topic_id provided by the client when present.
- Topics are queryable via a dedicated Topics API and carry minimal lifecycle state
  (e.g., active/closed/archived, last_active_at).

Topic becomes the intended scope boundary for all derived outputs (cards, stories, future entities), even if legacy compatibility temporarily remains.


## Consequences

### Positive
- Unrelated themes stop contaminating each other inside a long-running chat.
- Derived artifacts become interpretable and maintainable over time.
- The system gains a stable semantic unit for future evolution:
  story versioning, entity deduplication, and cross-time threading can all build on topic boundaries.
- Topic routing becomes observable and debuggable via explicit run records.

### Trade-offs
- Adds a new concept (topic) that clients and operators must understand.
- Requires routing logic and lifecycle rules (even if minimal at first).
- Introduces a compatibility period where topic association may be partially populated (nullable) and requires careful migration.


## Alternatives Considered

- Conversation-scoped derived data only  
  Rejected: causes cross-theme contamination and growing noise over time.

- Multiple conversations instead of topics  
  Rejected: conflicts with the product goal of “one ongoing chat UI,” and shifts complexity to the user.

- LLM-based topic segmentation for all routing  
  Rejected (for v0): non-deterministic, harder to debug, increases operational risk. Deterministic routing provides a stable foundation.

- Post-hoc clustering of messages into themes  
  Rejected: makes derived data ambiguous and complicates idempotency and explainability.


## Validation / Acceptance Criteria

- Messages can be written with an associated topic_id.
- Within a single conversation, multiple topics can be created and listed.
- Topic routing decisions are recorded with a decision and reasons sufficient to explain “why this topic.”
- Messages across different topics remain separable by query (topic-scoped retrieval).


## Links

- **Related ADRs**
  - `DECISIONS/0003-cards-as-derived-facts.md`
  - `DECISIONS/0007-make-card-extraction-idempotent-and-atomic.md`

- **Related Architecture**
  This decision is reflected as a core system constraint in  
  `architecture/foundation-v0.2.0.md`.
