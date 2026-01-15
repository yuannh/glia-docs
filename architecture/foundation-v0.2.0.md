# Glia Backend Architecture — Foundation v0.2.0

**Status**: Frozen  
**Date**: 2026-01-14  
**Related code version**: `glia-core@v0.2.0`

---

## What Changed Since v0.1.0

Foundation v0.2.0 introduces **Topic as a first-class isolation unit** and upgrades
the card extraction system to be **topic-scoped, idempotent, and atomic**.

This version builds on v0.1.0 without modifying its assumptions, and extends
the system to support:

- Single conversation, multiple concurrent topics
- Clean isolation of derived knowledge (cards, stories) by topic
- Deterministic, observable extraction runs per topic

---

## Key Architectural Additions

### 1. Topic as First-Class Entity

A `Topic` represents a coherent thread of thought inside a single conversation.

- Each user has one active conversation (`conv_{user}`)
- Each conversation can contain multiple topics
- Every message is assigned exactly one topic at write time

Topics are now the **isolation boundary** for:
- Card extraction
- Story generation
- Future entity and artifact evolution

---

### 2. Topic Routing (v0)

Incoming messages are routed to topics using deterministic rules:

- Default: append to active topic
- Strong switch signals (e.g. "switch topic", "another thing") create a new topic
- Explicit `topic_id` from client is always respected

All routing decisions are recorded in `topic_router_runs` for observability.

---

### 3. Topic-Scoped Cards

Cards are no longer conversation-scoped.

- Cards belong to `(user_id, topic_id)`
- Each extraction replaces all cards for a topic atomically
- No cards are shared or overwritten across topics

This prevents cross-topic contamination as conversations grow long-lived.

---

### 4. Topic-Scoped Extraction Runs

Card extraction jobs are now keyed by:

(topic_id, user_id, source_hash)

Guarantees:
- Same input → exactly one successful run
- Concurrent jobs deduplicate safely
- Worker retries never corrupt card state

---

## Backward Compatibility

- `conversation_id` remains in schema for traceability
- Legacy APIs are supported but internally map to active topics
- No existing client behavior is broken

---

## What This Enables Next

Foundation v0.2.0 unlocks future work without architectural rewrites:

- Entity de-duplication within a topic
- Story versioning and evolution
- Artifact extraction and hyperlinking
- Cross-topic narrative threads

---

## Related Documents

- `foundation-v0.1.0.md`
- ADR-0009: Topic as First-Class Isolation Unit
- ADR-0010: Topic-Scoped Cards with Atomic Replace

---

## Phase 4 Closure — Topic-Scoped Cards

This section formally closes **Phase 4** and certifies **foundation v0.2.0** as a stable and frozen architectural baseline.

Phase 4 focused on making *topics* a first-class isolation boundary for card extraction, storage, and lifecycle management. The goal was to eliminate cross-topic interference, ensure idempotent extraction, and establish clear operational invariants suitable for long-lived user data.

---

### Scope of Phase 4

Phase 4 covered the following responsibilities:

- Introduce **topic-scoped card extraction** as the canonical path
- Enforce **hard isolation boundaries** between topics
- Guarantee **idempotent and atomic** card replacement
- Improve **observability and auditability** of extract runs
- Ensure safe behavior for **edge cases** (e.g. empty topics)

Phase 4 explicitly **did not** introduce new product features or UX changes. All work was infrastructure and correctness focused.

---

### What Shipped

The following capabilities are fully implemented and verified:

#### Topic-Scoped Cards
- All cards are scoped by `topic_id`
- `replace_for_topic()` performs atomic delete + insert per `(user_id, topic_id)`
- Cross-topic overwrites are structurally impossible

#### Topic-Scoped Extract Runs
- `card_extract_runs` is uniquely constrained by  
  `(topic_id, user_id, source_hash)`
- Extract jobs are idempotent per topic
- Legacy `(conversation_id, user_id, source_hash)` constraint is preserved for compatibility

#### Message Range Guardrails
Each extract run records:
- `messages_range_start_id`
- `messages_range_end_id`
- `message_count`

This enables:
- Precise debugging of extraction inputs
- Auditable replay boundaries
- Detection of partial or unexpected runs

#### Empty Topic No-Op Semantics
- Topics with zero messages result in **no extract run**
- No cards, no hashes, no side effects
- Job returns `{ ok: true, reason: "no_messages" }`

This prevents:
- Incorrect conversation_id fallbacks
- Pollution of extract run history
- False positives in observability

#### End-to-End Verification
The following E2E scripts are authoritative for Phase 4 correctness:

- `scripts/e2e_cards_topic_scope.sh`
- `scripts/e2e_cards_empty_topic_noop.sh`

These verify:
- Topic isolation
- Idempotency
- Absence of cross-topic collisions
- Correct empty-topic behavior

---

### Invariants Guaranteed

As of foundation v0.2.0, the system guarantees:

- **Isolation**  
  Cards and extract runs are isolated by `topic_id`

- **Idempotency**  
  Re-running extraction with the same topic + messages produces no duplication

- **Atomicity**  
  Card replacement is atomic at the topic boundary

- **Auditability**  
  Every extract run is traceable to a concrete message range

- **Safety on Edge Cases**  
  Empty topics produce no persistent side effects

These invariants are relied upon by all subsequent phases.

---

### Risks Discovered & Mitigations

#### Risk: Card ID Collisions Across Topics
- **Issue**: Extractor-generated card IDs were not globally unique
- **Mitigation**: Card primary keys are now namespaced by scope  
  (e.g. `t:{topic_id}:{card_id}`)

#### Risk: Incorrect `conversation_id` Fallback on Empty Topics
- **Issue**: Extract job could fall back to active conversation when messages were empty
- **Mitigation**: Empty-topic no-op behavior eliminates this path entirely

#### Risk: Silent Idempotency Violations
- **Issue**: Missing topic-scoped unique constraint could allow duplicate runs
- **Mitigation**: Database-level unique constraint enforced and verified

All identified Phase 4 risks are now mitigated at the **schema + code** level.

---

### Deferred Decisions (Intentional)

The following decisions were consciously deferred:

- Enforcing `messages.topic_id NOT NULL` at the database level  
  (requires backfill or `CHECK NOT VALID` strategy)

- Historical backfill of pre-topic cards and runs

These are postponed to avoid destabilizing early user data and will be reconsidered in a future foundation version.

---

### Compatibility & Migration Notes

- Conversation-scoped APIs remain available for backward compatibility
- Topic-scoped paths are canonical for all new functionality
- No historical data migration is required to adopt foundation v0.2.0

---

### Status

**Phase 4 is complete.**

Foundation **v0.2.0** is considered:
- **Stable**
- **Frozen**
- **Safe for long-term iteration**

No further changes should be made to Phase 4 semantics.  
All future work must target **Phase 5 / foundation v0.3.0**.

Certified against `glia-core` tag **v0.2.3**.
