# adr 0022: story evidence is mandatory and drop on validation failure

Status: Accepted  
Date: 2026-01-19

## Context
Story summaries are a system-level artifact and must be auditable.
Previously, a story could exist without a traceable evidence chain, which makes
output unverifiable and potentially untrustworthy.

Phase 13 introduces `story_compose` as a strict contract and requires story
evidence to be persisted and validated before write.

## Decision
Story outputs must include a **traceable evidence chain** and fail closed.

- Story generation uses a strict contract with JSON-only output.
- Evidence must include valid user message references.
- Validation is enforced before persistence.
- On validation failure: retry once, then drop (no write).

Stories are no longer accepted without evidence.

## Consequences

### Positive
- Story outputs become auditable and verifiable.
- LLM output can no longer bypass evidence requirements.
- Failure modes are explicit and safe.

### Trade-offs
- Some story generations will be dropped.
- Validator logic becomes a hard dependency of story creation.

## Alternatives Considered
- Allow partial evidence or soft warnings  
  Rejected: allows unverifiable stories.
- Persist stories and annotate with “unknown evidence”  
  Rejected: undermines auditability.

## Validation / Acceptance Criteria
- Evidence is persisted with each story.
- Evidence references only user messages and valid topic/conversation scope.
- Invalid evidence never results in a persisted story.
- API returns evidence alongside story content.

## Links
- Related ADRs:
  - `DECISIONS/0019-prompt-contracts-only-source.md`
  - `DECISIONS/0020-structured-links-knowledge-graph-v0.md`
- Related Architecture:
  - `architecture/foundation-v0.9.0.md`
