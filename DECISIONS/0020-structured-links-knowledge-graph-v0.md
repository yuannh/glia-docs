# adr 0020: structured links for navigable knowledge graph v0

Status: Accepted  
Date: 2026-01-16

## Context
Users must navigate between story, timeline, entity, and topic without inference.
Links must be explicit, explainable, and stable without schema changes.

## Decision
Freeze **structured links** as a first-class payload in cards.

- Story cards expose `raw_json.links.entities`.
- People timeline items expose `links` and only link to entities from
  extractor-emitted `entities_mentioned`.
- Story to story links are allowed only with reasons:
  `thread_overlap` or `entity_overlap`.
- Links are topic-scoped; cross-topic linking is forbidden.
- Links fields must exist even when empty.

Links are derived from structured evidence, not from text inference.

## Consequences

### Positive
- Navigation is stable and explainable.
- Frontend can rely on fixed payload shape.
- Topic isolation is preserved at the link layer.

### Trade-offs
- Missing extractor signals result in missing links.
- Additional link assembly logic is required.

## Alternatives Considered
- Infer links from story text  
  Rejected: not explainable or stable.
- Frontend-derived links  
  Rejected: breaks contract stability.
- Cross-topic links  
  Rejected: violates isolation.

## Validation / Acceptance Criteria
- Story cards always include `raw_json.links` (even if empty).
- Timeline links only reference `entities_mentioned`.
- Story to story links include a valid reason.
- Cross-topic links never appear.
- E2E scripts validate navigation and isolation.

## Links
- Related ADRs:
  - `DECISIONS/0017-entity-layer-and-timeline-evolution.md`
  - `DECISIONS/0018-person-timeline-events-extractor-only.md`
  - `DECISIONS/0019-prompt-contracts-only-source.md`
- Related Architecture:
  - `architecture/foundation-v0.7.0.md`
