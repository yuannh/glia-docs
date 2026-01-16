# Glia Backend Architecture — Foundation v0.6.0

**Status**: Frozen  
**Date**: 2026-01-16  
**Related code version**: `glia-core@v0.6.0`

---

## What Changed Since v0.5.0
Foundation v0.6.0 introduces **narrative threads**, **entity identity**, and
**entity card timeline evolution** with evidence-driven constraints.

Key additions:
- Threads as soft-accepted, explainable cross-story views.
- Entities as stable identity for people cards.
- Timeline evolution that appends events without replacing cards.
- Evidence-driven constraints for timeline events.

---

## Narrative Threads (Soft Accepted)
Threads surface cross-story continuity as a read-only connection view.

- Threads do not modify stories or artifacts.
- Threads are visible by default unless rejected.
- Users only reject; there is no accept flow.
- Every connection includes explicit `reason_json`.

Threads remain topic-scoped and non-destructive.

---

## Entity Layer
Entities provide stable identity for repeated mentions.

- Entities are deduplicated by `(user_id, kind, canonical_key)`.
- Entity identity is stable across mentions.
- Entities remain topic-scoped and user-isolated.

---

## Entity Card Timeline Evolution
Entity cards (v0: people) evolve by appending timeline events.

- One entity card per `(user_id, topic_id, type, entity_id)`.
- Timeline events append; cards are not replaced.
- Non-entity cards retain replace semantics.

---

## Evidence-Driven Timeline Events
Timeline events are strictly evidence-driven.

- Events are created only from structured extractor output.
- Assistant raw replies are never timeline events.
- Each event includes evidence references for traceability.

---

## Explicit Non-Goals
Foundation v0.6.0 does not introduce:

- Cross-topic threads
- Thread-driven content edits
- Entity evolution beyond people (v0 scope)
- Timeline events derived from raw chat text

---

## What This Enables Next
Foundation v0.6.0 enables:

- Stable identity in long-running narratives
- Explainable cross-story continuity
- Controlled AI behavior contracts

---

## Related Documents
- `DECISIONS/0016-narrative-threads-soft-accepted.md`
- `DECISIONS/0017-entity-layer-and-timeline-evolution.md`
- `DECISIONS/0018-person-timeline-events-extractor-only.md`
- `architecture/foundation-v0.5.0.md`
