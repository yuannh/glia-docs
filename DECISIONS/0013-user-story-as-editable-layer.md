# ADR 0013: User Story as an Editable Layer

Status: Accepted  
Date: 2026-01-15

## Context
Phase 5 introduced system-generated story snapshots (`story_versions`) as
immutable, topic-scoped narratives derived from message state.
Phase 6 requires a user-owned narrative layer that is editable and referenceable
without breaking Phase 5 idempotency, determinism, or auditability.

Key constraints:
- System-generated history remains immutable and auditable.
- LLM and extraction jobs must never overwrite user-authored content.
- Story references to artifacts remain explicit and non-duplicative.

## Decision
Introduce **User Story** as a topic-scoped, user-owned editable layer.

- User Story is stored separately from `story_versions`.
- Each topic has at most one User Story per user.
- User Story references a base `story_version` via `base_story_version`.
- User Story content is user-authored and stored as `content_md`.
- Story rendering uses User Story when present; otherwise falls back to latest system story.

System-generated story extraction never writes to User Story.

## Consequences

### Positive
- Users can author and evolve their own narrative without losing system history.
- Phase 5 idempotent extraction semantics remain unchanged.
- Story provenance is explicit: system snapshot vs user-edited layer.
- Artifact references remain explicit and resolvable at render time.

### Trade-offs
- Two-story layers must be managed and rendered consistently.
- User Story may diverge from newly generated system snapshots.
- Additional write paths and access control are required for user edits.

## Alternatives Considered
- Allow system extraction to overwrite user story  
  Rejected: violates user ownership and breaks provenance.
- Edit `story_versions` directly  
  Rejected: undermines auditability and idempotent snapshot semantics.
- Multiple user story branches per topic  
  Rejected for Phase 6: increases complexity and diverges from minimal constraints.

## Validation / Acceptance Criteria
- User Story is persisted separately from `story_versions`.
- `story_versions` remain immutable and append-only.
- `extract_story_job` never writes to User Story.
- Rendering prefers User Story when present; otherwise uses latest system story.
- User Story creation and update do not modify existing story versions.

## Links
- Related ADRs:
  - `DECISIONS/0011-story-as-topic-timeline-snapshots.md`
  - `DECISIONS/0012-artifacts-as-addressable-objects.md`
- Related Architecture:
  - `architecture/foundation-v0.3.0.md`
