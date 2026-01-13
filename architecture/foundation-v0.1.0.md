#  Glia Backend Engineering Memo

> Version: foundation-v0.1.0
> Scope: Step 1 → Step 12 (Phase 1–2)
> Status: Locked as Foundation


## 1. Purpose

> This memo documents the completed backend foundation of Glia from Step 1 through the current Phase 2 milestone.
> The system described here is considered stable, validated, and locked.
> All future development must preserve the invariants defined in this memo.


## 2. What This Version Is

foundation-v0.1.0 establishes the minimum correct backend required to support:
- Continuous user conversations
- Deterministic persistence
- Asynchronous + synchronous card extraction
- Debug and production parity
- Observability and replayability

> This version is not an experiment and not a prototype.
> It is the reference foundation for all subsequent work.

## 3. Core Architectural Decisions (Locked)

### 3.1 Conversation Model (Phase 2 Invariant)
	•	One active conversation per user
	•	The active conversation ID is deterministic:
  conv_{user_id}

  Examples:
  - demo → conv_demo
  - a → conv_a
  - b → conv_b

Rules:
- If conversation_id is not provided, the request is written to conv_{user_id}
- The literal "default" conversation does not exist
- Explicit conversation_id is allowed but must be owned by the user
- Conversations are user-isolated and non-shareable

This is a product decision, not a temporary workaround.

### 3.2 Message Persistence
	•	All messages are persisted before any LLM streaming begins
	•	Assistant messages are persisted only after stream completion
	•	Message order is strictly append-only per conversation
	•	No message is ever written without a resolved conversation_id

### 3.3 Cards Are Derived State (Step 3 → Step 11)
	•	Cards are derived facts, not primary data
	•	Cards are always scoped by:
  (user_id, conversation_id)
  - Cards may be re-derived at any time from messages
  - Cards are never incrementally mutated in place

### 3.4 Card Extraction Semantics (Critical Invariant)

> All card extraction — debug or background — obeys the same rule:

> Extraction replaces the entire card set for a conversation atomically

Implementation rule:
- replace_for_conversation(...) is the only valid write path

This prevents:
- Duplication
- Drift
- Partial state
- Debug/production divergence

### 3.5 Debug vs Production Behavior

| Mode                | Behavior                                      |
|---------------------|-----------------------------------------------|
| EMIT_CARD_EVENTS=1  | Synchronous extraction + SSE card events      |
| EMIT_CARD_EVENTS=0  | Background job extraction                     |

Invariant:
> Both modes produce the same final card state.

> Debug mode exists only for observability and validation.

### 4. Observability & Idempotency (Step 11–12)
	•	Every extraction run is recorded in card_extract_runs
	•	Runs are idempotent per (conversation_id, user_id, source_hash)
	•	Provider, model, latency, fallback, and errors are recorded
	•	The system can answer:
	•	What was extracted?
	•	When?
	•	Why?
	•	With which model?

> This enables replay, audit, and regression analysis.

### 5. Explicit Non-Goals of This Version

The following are intentionally out of scope for foundation-v1:
- Multiple active conversations per user
- Conversation switching UI semantics
- Topic/thread modeling
- Card deduplication across time
- Semantic memory, summarization, or pruning
- Cross-conversation reasoning

These belong to Phase 3+.

### 6. Product Implication (Important)

> Users may stay in one continuous conversation UI forever.

Multiple topics, memories, and storylines are expected to be handled via:
- Card lifecycle
- Topic grouping
- Deduplication
- Evolution logic

> Not via new conversation IDs.

### 7. Validation Status

This version has been validated via:
- Manual E2E curl flows
- Multi-user isolation tests
- Database inspection
- Debug vs background extraction comparison
- Truncate-and-replay verification

> No unresolved correctness issues remain within scope.

## 8. Version Lock

foundation-v0.1。0 is now considered locked.

Any future change must:
- Be additive, or
- Be implemented above this layer, or
- Explicitly declare which invariant it breaks (and why)

## End of Memo
Status: Accepted as Glia Backend Foundation

