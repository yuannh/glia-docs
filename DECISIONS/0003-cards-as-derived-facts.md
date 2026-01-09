# ADR 0003: Cards as derived facts, not immediate chat side effects

- Status: Accepted
- Date: 2026-01-08

## Context

Cards are introduced as structured artifacts derived from conversation
history. A key question was whether cards should be produced as immediate
side effects of chat execution or treated as independently derived state.

Clarity, failure tolerance, and explicit system boundaries are treated
as first-class system constraints.

## Decision

Cards are defined as **derived facts**:

- Cards are generated from persisted conversation state.
- Card extraction may succeed or fail independently of chat execution.
- Cards are not required to appear in the chat stream.
- Card storage represents the latest known derived state only.

Writes to the card store are atomic:
- Failed extraction must not overwrite existing cards.
- Partial or incomplete results are never committed.

## Consequences

### Positive
- Chat interaction remains stable regardless of extraction outcomes.
- Cards become queryable, replayable system facts.
- Extraction logic can evolve independently of interaction semantics.

### Trade-offs
- Cards are not immediately visible by default.
- Additional coordination is required to ensure atomic replacement.

## Alternatives Considered

- Streaming cards inline with chat  
  Rejected: latency coupling between interaction and extraction.

- Treating cards as ephemeral UI artifacts  
  Rejected: loss of history and replayability.

- Incremental append-only writes  
  Rejected: risk of state corruption and partial knowledge.

## Validation

- Chat completes successfully even when card extraction fails.
- Existing cards remain unchanged on extraction failure.
- `/api/cards` reflects only the latest successful extraction.

## Links

- Architecture snapshot: `architecture/phase-1.md`
