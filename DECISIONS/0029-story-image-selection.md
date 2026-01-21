# adr 0029: story image selection with explainable image refs

Status: Accepted  
Date: 2026-01-20

## Context
Story output currently lacks a defined image selection strategy.
Phase 14 requires an explainable, bounded image set for narrative blocks.

## Decision
Introduce **image selection** with explainable `image_refs`.

- Story blocks may include image references.
- Selection targets 2–4 images where available.
- Priority favors opening and climax moments.
- Every image ref includes a non-empty reason.

## Consequences

### Positive
- Story visuals become consistent and explainable.
- iOS can render images with clear rationale.
- Avoids arbitrary or excessive image usage.

### Trade-offs
- Additional selection logic and validation.
- Fewer images when evidence is sparse.

## Alternatives Considered
- No image selection rules  
  Rejected: inconsistent and non-explainable.
- Full auto-selection without reasons  
  Rejected: reduces auditability.

## Validation / Acceptance Criteria
- 2–4 images selected when available.
- Each image ref includes a reason.
- E2E tests validate counts and placement rules.

## Links
- Related ADRs:
  - `DECISIONS/0025-story-structured-blocks.md`
- Related Architecture:
  - `architecture/foundation-v0.10.0.md`
