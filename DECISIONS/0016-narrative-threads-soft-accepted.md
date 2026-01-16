# adr 0016: narrative threads soft accepted and explainable

Status: Accepted  
Date: 2026-01-16

## Context
After explicit story artifact links, the system needs a safe way to surface
cross-story continuity without modifying stories or artifacts.
Any such capability must be explainable, reversible by the user, and non-destructive.

## Decision
Introduce **narrative threads** as a read-only, explainable connection view.

- Threads are not content and do not modify existing data.
- Threads are soft-accepted by default and shown unless rejected.
- Users can reject only; there is no accept action.
- Every thread member requires `reason_json`.
- Cross-topic links are forbidden.

Thread visibility is governed by suggestions with status `pending` or `rejected`.

## Consequences

### Positive
- Cross-story continuity becomes visible without rewriting content.
- Every connection is explainable and auditable.
- Users can remove threads without side effects.

### Trade-offs
- New background job and storage are required.
- Threads may not reflect user intent until rejected.

## Alternatives Considered
- Auto-merge stories  
  Rejected: violates non-destructive constraints.
- Require explicit acceptance before showing  
  Rejected: contradicts soft-accept rule.
- LLM-inferred threads  
  Rejected: reduces explainability.

## Validation / Acceptance Criteria
- Threads never modify stories or artifacts.
- `GET /api/threads` returns only non-rejected threads.
- Rejected threads do not reappear.
- Each member has non-empty `reason_json`.

## Links
- Related ADRs:
  - `DECISIONS/0015-story-artifact-explicit-links.md`
- Related Architecture:
  - `architecture/foundation-v0.6.0.md`
