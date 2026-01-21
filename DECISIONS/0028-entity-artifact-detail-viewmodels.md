# adr 0028: entity and artifact detail viewmodels are frozen

Status: Accepted  
Date: 2026-01-20

## Context
Entity and artifact detail views are currently too thin for direct rendering.
Phase 14 requires stable, render-ready viewmodels for iOS.

## Decision
Freeze **entity** and **artifact** detail viewmodels.

- Entity detail includes `name/subtitle/body/footer` and linked references.
- Artifact detail includes `title/content_md/image_ids/source_links`.
- Schemas are stable and required for direct rendering.

## Consequences

### Positive
- iOS can render detail pages without inference.
- Detail views become consistent and user-facing.
- Linked references are explicit.

### Trade-offs
- New viewmodel schemas must be maintained.
- Content generation must meet minimum completeness.

## Alternatives Considered
- Keep index-level entity or artifact views  
  Rejected: insufficient for user-facing detail pages.
- Allow frontend to infer missing fields  
  Rejected: unstable and inconsistent.

## Validation / Acceptance Criteria
- Detail APIs return required fields with correct types.
- E2E tests verify presence of body, footer, and source fields.

## Links
- Related ADRs:
  - `DECISIONS/0012-artifacts-as-addressable-objects.md`
  - `DECISIONS/0017-entity-layer-and-timeline-evolution.md`
- Related Architecture:
  - `architecture/foundation-v0.10.0.md`
