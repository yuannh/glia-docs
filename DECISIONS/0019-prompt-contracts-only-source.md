# adr 0019: prompt contracts as the only source of ai behavior

Status: Accepted  
Date: 2026-01-16

## Context
AI outputs drive chat responses, person events, and story summaries.
Inline prompts are not auditable or versioned, and behavior is inconsistent.
Phase 11 requires enforceable boundaries without schema changes.

## Decision
Introduce **prompt contracts** as the only allowed source of AI behavior.

- All AI calls reference a named, versioned contract.
- Contracts live in `prompt_contracts/*.md` and are loaded at runtime.
- Inline prompts in code are forbidden.
- All outputs require deterministic fallback behavior.
- Contract name + version must be logged or embedded in existing JSON fields.
- No schema changes are introduced.

## Consequences

### Positive
- AI behavior is auditable, versioned, and consistent.
- Output constraints are explicit and enforceable.
- Failures are deterministic and recoverable.

### Trade-offs
- All AI call sites must be refactored to use contracts.
- Contract loading becomes a runtime dependency.

## Alternatives Considered
- Keep inline prompts  
  Rejected: not auditable.
- Store contracts in DB  
  Rejected: violates no-schema-change rule.
- Allow mixed inline + contracts  
  Rejected: breaks single source of truth.

## Validation / Acceptance Criteria
- No inline prompts remain in code paths.
- All AI calls log `contract_name` and `version`.
- Chat outputs are plain text only and never written into facts.
- Person events and story summaries follow strict schemas.
- Fallbacks are deterministic and used on failure.

## Links
- Related ADRs:
  - `DECISIONS/0018-person-timeline-events-extractor-only.md`
- Related Architecture:
  - `architecture/foundation-v0.7.0.md`
