# adr 0027: line ids only enforcement via dot evidence

Status: Accepted  
Date: 2026-01-20

## Context
Even with contracts, LINE could still access message text directly and bypass
the DOT evidence chain. This undermines traceability and the non-bypass rule.

## Decision
Enforce **ids-only** for LINE at runtime.

- LINE may only consume message ids and snippets from DOT evidence.
- Any access to message text outside allowed ids is rejected.
- Validators and runtime checks enforce this gate.
- Invalid ids result in drop (no persistence).

## Consequences

### Positive
- Evidence chain becomes non-bypassable.
- LINE outputs are strictly grounded in DOT evidence.
- Reduces risk of silent drift.

### Trade-offs
- Requires additional runtime plumbing.
- May reduce output coverage if DOT evidence is sparse.

## Alternatives Considered
- Rely on code review or rg guardrails only  
  Rejected: not enforceable at runtime.
- Allow fallback to raw message text  
  Rejected: breaks evidence guarantees.

## Validation / Acceptance Criteria
- LINE fails when using ids not in DOT evidence.
- E2E tests inject invalid ids and must fail closed.
- LINE outputs only reference allowed evidence.

## Links
- Related ADRs:
  - `DECISIONS/0023-dot-line-story-hard-gates.md`
- Related Architecture:
  - `architecture/foundation-v0.10.0.md`
