# adr 0024: story link objects and narrative tokens are frozen

Status: Accepted  
Date: 2026-01-19

## Context
Story rendering for iOS requires a stable, non-ambiguous link structure.
Previously, link representations varied and required inference or UI parsing.

Phase 13 standardizes link objects and narrative tokens so that links are
machine-resolvable without guessing.

## Decision
Freeze **story link objects** and **narrative token format**.

- Links are resolved into a unified links object.
- Narrative text uses explicit link tokens (e.g. `<link kind="people" id="...">`).
- Tokens and links must align one-to-one.
- The frontend must not infer links from raw text.

## Consequences

### Positive
- Stable, deterministic rendering for iOS.
- Link provenance is explicit and auditable.
- No UI heuristics required to resolve links.

### Trade-offs
- Story output must adhere to a strict token format.
- Link mapping must be validated at generation time.

## Alternatives Considered
- Infer links from free text  
  Rejected: unstable and non-deterministic.
- Store link positions without tokens  
  Rejected: brittle for rendering.

## Validation / Acceptance Criteria
- Links object is always returned with story responses.
- Tokens and links align without mismatch.
- E2E tests validate schema shape and alignment.

## Links
- Related ADRs:
  - `DECISIONS/0015-story-artifact-explicit-links.md`
  - `DECISIONS/0020-structured-links-knowledge-graph-v0.md`
- Related Architecture:
  - `architecture/foundation-v0.9.0.md`
