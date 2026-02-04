# ADR 0025: story structured blocks with strict voice and length constraints

Status: Accepted  
Date: 2026-01-20

## Context
Story is currently a summary output. Phase 14 requires a readable narrative
experience with explicit structure and consistent tone.

Without enforced blocks and voice constraints, story output drifts and becomes
inconsistent for iOS rendering.

## Decision
Introduce **structured story blocks** with hard constraints.

- Story output must include `opening`, `moment_cards`, `bridges`, and optional `closing`.
- Blocks enforce word-count limits and banned phrases.
- Validators reject outputs that violate structure or constraints.
- Narrative remains evidence-backed and traceable.

## Consequences

### Positive
- Story becomes readable and consistent across topics.
- iOS can render a predictable structure.
- Output drift is controlled by hard validators.

### Trade-offs
- Higher drop rate for non-conforming outputs.
- Validators must be maintained as rules evolve.

## Alternatives Considered
- Keep narrative as a single summary  
  Rejected: not a structured experience.
- Soft checks for structure and voice  
  Rejected: allows drift into production.

## Validation / Acceptance Criteria
- Blocks are present and structurally complete.
- Word-count and banned-phrase rules are enforced.
- E2E tests validate block structure and constraints.

## Links
- Related ADRs:
  - `DECISIONS/0022-story-evidence-mandatory.md`
  - `DECISIONS/0024-story-link-objects-frozen.md`
- Related Architecture:
  - `architecture/foundation-v0.10.0.md`
