# adr 0026: line patterns for production grade outputs

Status: Accepted  
Date: 2026-01-20

## Context
LINE outputs are currently limited to hypotheses.
Phase 14 requires LINE to generate production-grade outputs that can feed
story composition and entity updates.

## Decision
Introduce **LINE patterns** with explicit output types.

- Outputs include `thread_ready`, `entity_update`, and `thread_status_update`.
- Each output must include traceable evidence.
- Output schema is contract-validated before persistence.
- LINE outputs are stored as first-class records.

## Consequences

### Positive
- LINE becomes a structured input layer for story and entity.
- Evidence-backed outputs remain auditable.
- Output types align with downstream consumers.

### Trade-offs
- Additional schema and storage requirements.
- Increased validator complexity.

## Alternatives Considered
- Keep LINE as free-form hypothesis  
  Rejected: insufficient for production workflows.
- Derive outputs directly from story logic  
  Rejected: hides reasoning and weakens evidence chain.

## Validation / Acceptance Criteria
- Each output includes evidence with user message references.
- Schema validation rejects invalid types or missing fields.
- E2E tests confirm at least one `thread_ready` and `entity_update`.

## Links
- Related ADRs:
  - `DECISIONS/0023-dot-line-story-hard-gates.md`
- Related Architecture:
  - `architecture/foundation-v0.10.0.md`
