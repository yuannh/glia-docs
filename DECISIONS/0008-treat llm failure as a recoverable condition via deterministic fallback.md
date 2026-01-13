ADR 0008: Treat LLM Failure as a Recoverable Condition via Deterministic Fallback

Status: Accepted
Date: 2026-01-12

⸻

Context

Large language model (LLM) calls are inherently unreliable due to network issues, rate limits, quota exhaustion, and provider errors.

If LLM failure is treated as system failure:
	•	Card extraction becomes brittle and unpredictable.
	•	System availability is tied to third-party uptime.
	•	Failures propagate unnecessarily to users and operators.

However, card extraction is a derived enhancement, not a core transactional requirement.

⸻

Decision

The system adopts an LLM-first with deterministic fallback strategy:
	•	LLM-based extraction is attempted when configured.
	•	If the LLM fails, a deterministic rule-based extractor is used.
	•	A successful fallback extraction is considered a successful run.
	•	Each extract run records whether fallback was used and why.
	•	System correctness does not depend on LLM availability.

LLM failure is treated as a recoverable condition, not a fatal error.

⸻

Consequences

Positive
	•	Card extraction remains reliable even when LLM providers fail.
	•	System availability is decoupled from third-party services.
	•	Operators gain visibility into LLM reliability and fallback frequency.
	•	Future extractor strategies can evolve without changing system semantics.

Trade-offs
	•	Extracted cards may be less semantically rich during fallback.
	•	Observability and metrics become essential to monitor quality degradation.
	•	“Success” no longer implies “LLM succeeded,” only that extraction completed.

⸻

Alternatives Considered
	•	Fail extraction on LLM error
Rejected: causes unnecessary system degradation.
	•	Disable extraction entirely when LLM fails
Rejected: violates derived-data guarantees.
	•	Retry indefinitely until LLM succeeds
Rejected: leads to queue backlogs and unpredictable latency.

⸻

Validation / Acceptance Criteria
	•	Card extraction succeeds even when LLM credentials are missing or invalid.
	•	Extract runs record whether fallback was used and the failure reason.
	•	Cards are always produced for extractable conversations.
	•	Operators can distinguish LLM success from fallback success.

⸻

Links
	•	ADR 0003: Cards as Derived Facts
	•	ADR 0007: Make Card Extraction Idempotent and Atomic
	•   Architecture — Phase 2  
        This decision is reflected as a core system constraint in
        `architecture/phase-2.md`.
