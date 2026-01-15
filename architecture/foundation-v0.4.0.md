# Glia Backend Architecture — Foundation v0.4.0

**Status**: Frozen  
**Date**: 2026-01-15  
**Related code version**: `glia-core@v0.4.0`

---

## What Changed Since v0.3.0
Foundation v0.4.0 introduces a user-owned narrative layer and writable artifacts
while preserving Phase 5 story snapshot semantics and idempotent extraction.

Key additions:
- User Story as a topic-scoped editable layer.
- Artifact creation and editing as user-owned assets.
- Explicit rendering and write-protection rules to preserve auditability.

---

## User Story (Editable Layer)
User Story is a topic-scoped, user-owned narrative that sits above system snapshots.

- Stored separately from `story_versions`.
- References a base system snapshot via `base_story_version`.
- Exactly one User Story per `(user_id, topic_id)`.
- User Story content is authoritative for user-visible narrative when present.

System-generated story extraction never writes to User Story.

---

## Story Rendering Rules
Story presentation follows a strict precedence rule:

- If a User Story exists, render `user_story.content_md`.
- Otherwise, render the latest system `story_version`.

This rule makes provenance explicit without modifying system snapshots.

---

## Artifact Editing
Artifacts become user-owned, editable assets.

- Users can create artifacts.
- Users can update existing artifacts.
- Artifacts remain scoped to `(user_id, topic_id)` and retain stable identity.

No artifact version history, collaboration, or merge semantics are introduced.

---

## Story ↔ Artifact Linking
Story references to artifacts are explicit and non-duplicative.

- Story content may only reference artifacts by link.
- The canonical reference format remains `glia://artifact/{id}`.
- Story content does not embed or copy artifact payloads.

Rendering resolves artifact references at read time.

---

## Write-Protection Rules
Phase 6 enforces hard boundaries to preserve auditability and user ownership:

- `extract_story_job` never writes to User Story.
- LLM outputs never overwrite user-authored content.
- New system `story_versions` never mutate existing User Stories.

System history remains immutable and independently auditable.

---

## Explicit Non-Goals
Phase 6 does not introduce:

- Real-time collaborative editing
- Patch or diff-based merge systems
- Automated overwrite of user-authored narrative
- Mutation of historical `story_versions`

---

## What This Enables Next
Foundation v0.4.0 enables:

- Clear provenance between system snapshots and user narrative
- Durable, user-owned story editing without breaking idempotent extraction
- Stable artifact references that remain consistent across story evolution

---

## Related Documents
- `DECISIONS/0011-story-as-topic-timeline-snapshots.md`
- `DECISIONS/0012-artifacts-as-addressable-objects.md`
- `DECISIONS/0013-user-story-as-editable-layer.md`
- `architecture/foundation-v0.3.0.md`

