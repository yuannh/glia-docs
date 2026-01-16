# adr 0018: person timeline events are extractor only and evidence driven

Status: Accepted  
Date: 2026-01-16

## Context
After introducing timeline evolution for people cards, timelines risk being polluted
by assistant replies or raw chat text.
Timeline events must represent facts, not conversation output.

## Decision
People timeline events are **extractor only** and **evidence driven**.

- Timeline append occurs only when extractor emits `person_event`.
- Assistant raw replies are never written as timeline events.
- Every event must include `evidence.message_ids`.
- Timeline append is deduplicated by deterministic evidence-based key.

If no `person_event` is emitted, no timeline event is added.

## Consequences

### Positive
- Timelines remain factual and traceable to user messages.
- Assistant chatter cannot pollute timelines.
- Event deduplication is deterministic.

### Trade-offs
- Mentions may be skipped if extractor fails to emit events.
- Extractor schema becomes a hard dependency.

## Alternatives Considered
- Use message text directly as events  
  Rejected: mixes facts with conversation phrasing.
- Allow assistant text as events  
  Rejected: undermines factual integrity.

## Validation / Acceptance Criteria
- Timeline appends only when `person_event` exists.
- No assistant-originated text appears in timeline events.
- Each event includes `evidence.message_ids`.
- Duplicate evidence does not create duplicate events.

## Links
- Related ADRs:
  - `DECISIONS/0017-entity-layer-and-timeline-evolution.md`
- Related Architecture:
  - `architecture/foundation-v0.6.0.md`
