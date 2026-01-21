# adr 0030: entity profile hard validation and fail closed writes

Status: Accepted  
Date: 2026-01-21

## Context
Entity profiles are user-facing, render-ready cards.
Constraints have been defined in contracts, but enforcement was not guaranteed at runtime.
This allowed invalid profiles to be written without explicit failure signals or negative tests,
weakening data quality and auditability.

## Decision
Entity profile outputs are **hard validated** and **fail closed** before persistence.

- Validation is centralized in the contract validator.
- Required fields must exist: `name`, `body_md`, and `subtitle` for `people`.
- Word count and paragraph count are enforced per kind.
- Banned phrases are rejected across subtitle and body.
- Invalid outputs are rejected and **not** written.
- Jobs surface explicit failure counters (`rejected`, `invalid_payload`, `empty_body`) and return `ok=false`.
- Negative E2E tests are required to verify rejection behavior.

## Consequences

### Positive
- Invalid profiles cannot enter storage.
- Data quality becomes enforceable and observable.
- Failures are explicit, auditable, and testable.

### Trade-offs
- Higher drop rate for non-conforming outputs.
- Validators require ongoing tuning as contracts evolve.

## Alternatives Considered
- Soft warnings with permissive writes  
  Rejected: allows invalid data into storage.
- Auto-repair invalid profiles  
  Rejected: hides failure and weakens constraints.

## Validation / Acceptance Criteria
- Invalid payloads are rejected and do not persist.
- Validators enforce per-kind word and paragraph constraints.
- Banned phrases cause rejection.
- Jobs return `ok=false` when any rejection occurs.
- Negative E2E test confirms rejection path.

## Links
- Related ADRs:
  - `DECISIONS/0028-entity-artifact-detail-viewmodels.md`
- Related Architecture:
  - `architecture/foundation-v0.10.1.md`
