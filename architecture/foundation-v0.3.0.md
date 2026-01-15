# Glia Backend Architecture — Foundation v0.3.0

**Status**: Frozen  
**Date**: 2026-01-15  
**Related code version**: `glia-core@v0.3.0`

---

## What Changed Since v0.2.0
Foundation v0.3.0 introduces **Story** and **Artifact** as new topic-scoped, derived layers.
Stories are stored as immutable snapshots and reference artifacts by stable ID.
Card extraction adds deterministic de-duplication to preserve topic-level correctness.

---

## Story as Topic Timeline (Snapshot Semantics)
Story is a readable timeline for a topic.

- Story is stored as **versioned snapshots**.
- Snapshots are immutable and append-only.
- New messages produce a new snapshot version.
- Story extraction is idempotent by `(topic_id, user_id, source_hash)`.
- Story and Card jobs run in parallel but remain independent, using the **same** topic message `source_hash`.

---

## Artifacts and Story Linking
Artifacts are addressable, stable objects scoped to `(user_id, topic_id)`.

- Stories reference artifacts by stable ID.
- Artifact references use `glia://artifact/{id}`.
- Story snapshots never duplicate artifact content.

This separation preserves narrative clarity while enabling durable content references.

---

## Deterministic Card De-duplication
Card extraction enforces deterministic de-duplication:

- Cards are written with a required `dedup_key`.
- Cards are deduplicated by `dedup_key` before write.
- The story card for a topic is a single card with:
  - `dedup_key = "story:{topic_id}"`

Topic isolation remains strict and enforced at all layers.

---

## What This Enables Next
Foundation v0.3.0 enables:

- Stable, replayable story timelines per topic
- Durable artifact references inside stories without content duplication
- Deterministic card state under retries and concurrent extraction

---

## Related Documents
- `DECISIONS/0011-story-as-topic-timeline-snapshots.md`
- `DECISIONS/0012-artifacts-as-addressable-objects.md`
- `DECISIONS/0010-topic-scoped-cards-with-atomic-replace.md`
- `architecture/foundation-v0.2.0.md`
