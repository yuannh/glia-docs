# adr 0021: thread hypothesis suggestions

Status: Accepted  
Date: 2026-01-16

## Context
Narrative threads are currently derived from explicit, explainable signals.
Phase 12 introduces a controlled AI hypothesis layer for thread suggestions,
but must not create threads, change story content, or alter existing data.

This requires a strict prompt contract, a validated JSON schema, and an
idempotent write path that only populates `thread_suggestions`.

## Decision
Introduce **thread hypothesis suggestions** as an AI-generated, read-only layer.

- A new prompt contract `thread_hypothesis` produces JSON-only output.
- Output is validated against a strict schema:
  - `hypotheses[]` is allowed to be empty.
  - Each hypothesis includes evidence with `message_id`, `topic_id`, and `snippet`.
  - Evidence must include at least two user messages.
  - `confidence` is constrained to `{low, medium, high}`.
- The build job reads only user messages and writes only to `thread_suggestions`.
- Suggestions are idempotent via `dedup_key` and are stored with status `pending`.
- No code in this flow may write to `threads` or `thread_members`.

## Consequences

### Positive
- AI can propose narrative hypotheses without mutating core data.
- Suggestions are explainable and tied to user evidence.
- The system remains safe and auditable through schema validation.

### Trade-offs
- Suggestions are non-binding and may be noisy.
- Strict validation may reject otherwise useful hypotheses.
- Additional schema and job logic are required.

## Alternatives Considered
- Auto-create threads from hypotheses  
  Rejected: violates non-destructive constraints.
- Use unvalidated free-form text  
  Rejected: not auditable or explainable.
- Allow assistant messages as evidence  
  Rejected: breaks evidence integrity.

## Validation / Acceptance Criteria
- Hypotheses output is JSON-only and schema-valid.
- Each hypothesis has evidence from at least two user messages.
- `thread_suggestions` is the only table written by the job.
- `threads` and `thread_members` remain unchanged.
- E2E scripts confirm pending suggestions and zero thread writes.

## Links
- Related ADRs:
  - `DECISIONS/0016-narrative-threads-soft-accepted.md`
  - `DECISIONS/0019-prompt-contracts-only-source.md`
- Related Architecture:
  - `architecture/foundation-v0.8.0.md`
