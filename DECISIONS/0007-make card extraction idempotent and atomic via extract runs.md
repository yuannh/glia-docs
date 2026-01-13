ADR 0007: Make Card Extraction Idempotent and Atomic via Extract Runs

Status: Accepted
Date: 2026-01-12


## Context

Card extraction is a derived operation that may be retried, delayed, or executed concurrently due to background processing and failures.

Without explicit idempotency guarantees:
	•	Retried jobs could duplicate or corrupt cards.
	•	Partial failures could leave conversations with empty or inconsistent card sets.
	•	System correctness would depend on timing rather than intent.

To be production-safe, card extraction must be repeatable, deterministic, and atomic.


## Decision

Card extraction is modeled as an idempotent extract run with atomic writes:
	•	Each extraction run is uniquely identified by (conversation_id, user_id, source_hash).
	•	A source hash represents the logical input state of the conversation.
	•	At most one successful extract run exists per unique source hash.
	•	Card writes replace the entire card set for a conversation atomically.
	•	Re-running extraction for the same source hash is a no-op.

Extraction results are treated as derived facts, not incremental mutations.


## Consequences

### Positive
	•	Background jobs are safe to retry without risk of duplication or corruption.
	•	Card state is always internally consistent.
	•	Failures are observable and recoverable.
	•	The system cleanly separates primary facts (messages) from derived facts (cards).

### Trade-offs
	•	Extraction requires explicit bookkeeping and metadata.
	•	Cards are rewritten wholesale rather than patched incrementally.
	•	The extraction pipeline is more complex than a naive implementation.


## Alternatives Considered
	•Incremental card updates
	Rejected: prone to partial failure and difficult to reason about.
	•Best-effort deduplication
	Rejected: unreliable under retries and concurrency.
	•In-memory idempotency guards
	Rejected: incompatible with multi-instance deployment.


## Validation / Acceptance Criteria
	•	Re-running extraction for the same input does not change card state.
	•	Concurrent extraction attempts do not produce duplicate cards.
	•	Worker crashes during extraction do not leave empty or partial card sets.
	•	Card state always corresponds to a complete extraction run.


## Links

- **Related ADRs**
  - `DECISIONS/0003-cards-as-derived-facts.md`
  - `DECISIONS/0004-background-card-extraction.md`

- **Related Architecture**
  This decision is reflected as a core system constraint in  
  `architecture/phase-2.md`.
