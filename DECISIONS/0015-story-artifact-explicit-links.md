# adr 0015: story artifact explicit links

Status: Accepted  
Date: 2026-01-16

## Context
Stories are free text; artifacts are stable, topic-scoped objects.
Users need a way to click from a story mention to an artifact without rewriting story text.
Implicit inference or text parsing is not explainable or stable.

## Decision
Introduce **explicit story artifact links** as a first-class relation.

- Links are stored independently of story text.
- Links reference a story source (`system` or `user`) and a story ref id.
- Links reference artifacts by stable id.
- Links store anchor text and optional span positions.
- Links are strictly topic-scoped.

Stories remain unstructured; structure lives in links.

## Consequences

### Positive
- Stories become clickable without modifying narrative text.
- Link provenance is explicit and explainable.
- Topic isolation is preserved at the link layer.

### Trade-offs
- Additional storage and APIs are required.
- Links must be authored explicitly; no auto-linking.

## Alternatives Considered
- Embed artifact content into story text  
  Rejected: duplicates content and drifts.
- Infer links via LLM or regex  
  Rejected: not explainable or stable.
- Restructure story text into schema  
  Rejected: violates free-text boundary.

## Validation / Acceptance Criteria
- Links can be created between story and artifact within the same topic.
- Story text is never modified by link creation.
- Cross-topic links are rejected.
- Story read returns links aligned with the returned story source.

## Links
- Related ADRs:
  - `DECISIONS/0011-story-as-topic-timeline-snapshots.md`
  - `DECISIONS/0012-artifacts-as-addressable-objects.md`
- Related Architecture:
  - `architecture/foundation-v0.5.0.md`
