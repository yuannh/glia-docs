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
