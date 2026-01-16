# adr 0017: entity layer and timeline evolution for entity cards

Status: Accepted  
Date: 2026-01-16

## Context
Repeated mentions of the same person create duplicate cards and lose continuity.
Cards are currently replace-based and do not support stable evolution.

## Decision
Introduce an **entity layer** and **timeline evolution** for entity cards.

- Entities provide stable identity per `(user_id, kind, canonical_key)`.
- Cards gain `entity_id` and `evidence_json`.
- Entity cards (v0: people) evolve by appending timeline events.
- One entity card per `(user_id, topic_id, type, entity_id)`.
- Non-entity cards keep replace semantics.

## Consequences

### Positive
- Entity mentions are deduplicated.
- People cards evolve over time instead of replacing.
- Evidence is explicitly attached to timeline events.

### Trade-offs
- New entity resolution logic is required.
- Timeline growth needs deterministic deduplication rules.

## Alternatives Considered
- Keep replace semantics for people cards  
  Rejected: loses temporal continuity.
- Use only dedup_key without entity ids  
  Rejected: less explicit and harder to reason about.

## Validation / Acceptance Criteria
- Variants of a person resolve to a single entity.
- People cards remain a single card per entity per topic.
- Timeline appends new events without replacing old ones.
- Non-entity cards remain replace-based.

## Links
- Related ADRs:
  - `DECISIONS/0010-topic-scoped-cards-with-atomic-replace.md`
- Related Architecture:
  - `architecture/foundation-v0.6.0.md`
