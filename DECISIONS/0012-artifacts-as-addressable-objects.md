# ADR 0012: Artifacts as Addressable Objects with Stable Identity

Status: Accepted  
Date: 2026-01-15

## Context
Phase 5 introduces Story as a topic-scoped narrative layer.
Stories may reference structured outputs and durable knowledge that should remain stable
across story versions.

If story content duplicates artifact content, stories become bloated and inconsistent,
and updates to artifacts cannot be reflected without rewriting story snapshots.

## Decision
Artifacts are first-class, addressable objects with **stable identity**.

- Each artifact has a stable ID.
- Artifacts are scoped to `(user_id, topic_id)`.
- Stories reference artifacts by ID only and **never duplicate artifact content**.
- The canonical artifact reference format is `glia://artifact/{id}`.

Story remains a narrative timeline; artifacts represent stable, addressable content.

## Consequences

### Positive
- Stories remain readable and compact while referencing durable content.
- Artifact content can evolve independently of story snapshots.
- Topic isolation is preserved for artifacts and their references.
- Downstream systems can reliably dereference artifacts by ID.

### Trade-offs
- Additional object type and lifecycle must be managed.
- Story readability depends on correct artifact resolution.
- Artifact identity constraints must be enforced to prevent collisions.

## Alternatives Considered
- Embed full artifact content inside story snapshots  
  Rejected: duplication, drift, and inconsistent updates.
- Use transient or computed artifact identifiers  
  Rejected: breaks stable addressing and reference durability.
- Global artifact namespace without topic scoping  
  Rejected: violates topic isolation guarantees.

## Validation / Acceptance Criteria
- Artifacts are created with stable IDs and scoped by `(user_id, topic_id)`.
- Stories reference artifacts using `glia://artifact/{id}` only.
- Artifact content is not duplicated inside story snapshots.
- Artifact references resolve within the same topic boundary.

## Links
- Related ADRs:
  - `DECISIONS/0011-story-as-topic-timeline-snapshots.md`
  - `DECISIONS/0009-topic-as-first-class-isolation-unit.md`
- Related Architecture:
  - `architecture/foundation-v0.3.0.md`
