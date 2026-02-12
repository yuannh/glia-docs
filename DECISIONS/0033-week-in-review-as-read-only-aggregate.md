# adr 0033: week in review as read-only aggregate card

Status: Accepted  
Date: 2026-02-05

## Context
Users need a time-bounded summary of stories per conversation per week.
System already has story_versions and topic-scoped narratives; a read-only
aggregate avoids duplicating content and keeps a single source of truth.

## Decision
Introduce **Week in Review** as a read-only, time-bounded aggregate.

- A job builds a weekly aggregate from story_versions for a given conversation and week_id.
- Output is written as a card (type=week_review) with stable schema (week_id, story refs, summary).
- The job is idempotent per (user_id, conversation_id, week_id); repeated runs do not duplicate.
- API exposes GET /api/week_in_review?conversation_id=...&week_id=YYYY-Www.
- No direct editing of week-in-review content; it is derived only from story_versions.

## Consequences

### Positive
- Clients get a stable, time-scoped summary without recomputing on every request.
- Single source of truth remains story_versions; week_review is derived.
- Idempotent writes keep storage predictable.

### Trade-offs
- Requires a defined week_id format and time-bounds logic.
- Scope is conversation + week; cross-conversation rollups are out of scope.

## Alternatives Considered
- Compute on every API request  
  Rejected: latency and load.
- Store as a separate non-card entity  
  Rejected: cards already provide list/detail and filtering; consistency with feed is useful.

## Validation / Acceptance Criteria
- Job is idempotent per (user_id, conversation_id, week_id).
- API returns week_id, summary, and story references.
- E2E verifies card presence and API response shape.

## Links
- Related ADRs:
  - `DECISIONS/0011-story-as-topic-timeline-snapshots.md`
- Related Architecture:
  - `architecture/foundation-v0.10.0.md`
