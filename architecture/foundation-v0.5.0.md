# Glia Backend Architecture — Foundation v0.5.0

**Status**: Frozen  
**Date**: 2026-01-16  
**Related code version**: `glia-core@v0.5.0`

---

## What Changed Since v0.4.0
Foundation v0.5.0 introduces **narrative divergence** and **explicit story artifact links**
as first-class, topic-scoped structures.

Key additions:
- Divergence is explicitly modeled and visible at read time.
- Story artifact links are stored as independent objects, not embedded text.
- Link visibility is aligned with the currently rendered story source.

---

## Narrative Divergence
Divergence is a first-class, auditable state when user narrative differs from system snapshots.

- Divergence is scoped to `(user_id, topic_id, system_version)`.
- Divergence is read-time visible in story responses.
- Resolve records a user decision without modifying story content.

System and user narratives remain immutable and independently auditable.

---

## Explicit Story Artifact Links
Story artifact connections are represented as explicit link objects.

- Links reference a specific story source (`system` or `user`) and a story ref id.
- Links reference artifacts by stable id.
- Links store anchor text and optional span positions.
- Links never modify story text or artifact content.

Links are topic-scoped and strictly isolated.

---

## Story Read Alignment
Story reads return links aligned with the displayed story source:

- `source=system` → system story links
- `source=user` → user story links

This prevents mismatched or mixed provenance.

---

## Explicit Non-Goals
Foundation v0.5.0 does not introduce:

- Automatic or inferred linking
- Cross-topic linking
- Story text rewriting
- Artifact mutation via links

---

## What This Enables Next
Foundation v0.5.0 enables:

- Safe cross-story continuity surfaces
- Explainable link provenance
- A stable foundation for narrative threads

---

## Related Documents
- `DECISIONS/0014-narrative-divergence.md`
- `DECISIONS/0015-story-artifact-explicit-links.md`
- `DECISIONS/0013-user-story-as-editable-layer.md`
- `architecture/foundation-v0.4.0.md`
