# adr 0023: dot line story outputs are contract validated and hard gated

Status: Accepted  
Date: 2026-01-19

## Context
DOT, LINE, and STORY are critical output layers.
If these layers accept unvalidated outputs, system semantics can drift and
forbidden content can reach storage or user interfaces.

Phase 13 requires a unified validator that enforces contracts at runtime.

## Decision
DOT, LINE, and STORY outputs are **hard gated** by contract validators.

- Each layer must pass its validator before persistence.
- Validators are centralized and consistent.
- On validation failure: retry once, then drop.
- Outputs must never bypass contracts.

This applies regardless of provider or model.

## Consequences

### Positive
- Prevents drift and forbidden output from entering the system.
- Makes AI outputs consistent and audit-ready.
- Creates a single enforcement point for all layers.

### Trade-offs
- Increased drop rates for invalid outputs.
- Requires maintaining strict validators and schemas.

## Alternatives Considered
- Soft validation with warnings  
  Rejected: allows forbidden outputs to persist.
- Per-call inline validation  
  Rejected: fragmented and hard to audit.

## Validation / Acceptance Criteria
- Validator failures prevent persistence.
- Validators enforce all required fields and banned content.
- DOT/LINE/STORY jobs cannot bypass validators.
- E2E tests include negative cases that must fail closed.

## Links
- Related ADRs:
  - `DECISIONS/0019-prompt-contracts-only-source.md`
- Related Architecture:
  - `architecture/foundation-v0.9.0.md`
