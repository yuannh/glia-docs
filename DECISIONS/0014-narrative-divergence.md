# adr 0014: narrative divergence

Status: Accepted  
Date: 2026-01-16

## Context
System story snapshots are immutable and idempotent. User stories are editable and never overwritten.
As system stories evolve, user-authored narratives may diverge.
Without explicit divergence, the system cannot answer whether the current narrative is system or user,
and users cannot record a decision without mutation.

## Decision
Introduce **Narrative Divergence** as a first-class, auditable state scoped to
`(user_id, topic_id, system_version)`.

- Divergence is created when a user story exists and the system story advances beyond it.
- Divergence is visible in the story read path.
- Users can resolve divergence by recording a choice; no merge or rewrite occurs.
- Extraction jobs never write to user stories or divergences.

## Consequences

### Positive
- Divergence becomes explicit, queryable, and auditable.
- User ownership is preserved without hidden merges.
- System snapshots remain immutable and replay-safe.

### Trade-offs
- Additional lifecycle state must be stored and maintained.
- Divergence can persist indefinitely until user resolves.

## Alternatives Considered
- Auto-merge system and user narratives  
  Rejected: violates user ownership.
- Overwrite user stories on system update  
  Rejected: breaks auditability.
- UI-only divergence detection  
  Rejected: not auditable or enforceable.

## Validation / Acceptance Criteria
- Divergence exists only when a user story is behind the latest system version.
- Story reads expose divergence status.
- Resolve only updates divergence state.
- System and user stories remain unchanged by divergence operations.

## Links
- Related ADRs:
  - `DECISIONS/0011-story-as-topic-timeline-snapshots.md`
  - `DECISIONS/0013-user-story-as-editable-layer.md`
- Related Architecture:
  - `architecture/foundation-v0.4.0.md`
