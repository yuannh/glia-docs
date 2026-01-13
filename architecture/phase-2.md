# Architecture — Phase 2

## Document Scope

This document describes the architecture of Glia as of Phase 2 (early 2026).

Phase 2 evolves the system from an asynchronous prototype into a
durable, multi-user, and operationally accountable system.

The architecture described here reflects a specific set of accepted
architectural decisions recorded in ADRs 0005–0008.
It intentionally captures system semantics, not implementation detail.

This document is time-scoped and will not be retroactively rewritten
as the system evolves further.


## Phase Definition

Phase 2: Durable, Multi-User, and Observable System

Primary characteristics:
	•Durable persistence of core and derived state
	•Explicit user identity and isolation as system invariants
	•Background processing that is retry-safe, observable, and idempotent
	•AI components treated as fallible dependencies, not sources of truth

Explicitly out of scope for Phase 2:
	•Cross-organization or enterprise tenancy
	•Hard real-time guarantees or strict ordering semantics
	•Advanced scheduling, prioritization, or workflow orchestration
	•Model governance, evaluation pipelines, or prompt management systems

Phase 2 focuses on correctness, accountability, and operability
rather than feature breadth.


## Design Goals and Accepted Constraints

The Phase 2 architecture accepts the following constraints:
	•Durability over convenience
All system state that affects user experience must survive restarts,
crashes, and redeployments.
	•User identity as a first-class invariant
All reads and writes are scoped to an authenticated user identity.
Identity is enforced at API, store, and persistence layers.
	•Derived data must be reproducible
Cards and other secondary artifacts are never treated as canonical truth
and must be safe to recompute or replace.
	•Failure is a normal operating condition
External dependencies (including LLMs) are expected to fail partially or
completely without compromising system integrity.
	•Observability over opacity
System behavior must be explainable after the fact using logs and
persisted execution records.

These constraints deliberately favor operational clarity and system trust
over short-term development speed.


## Explicit Non-goals

During Phase 2, the architecture intentionally does not aim to:
	•Provide end-user guarantees around latency or availability SLOs
	•Optimize for high-throughput or large-scale multi-tenant workloads
	•Expose internal execution details as part of the public API
	•Guarantee deterministic AI outputs or semantic stability of cards

These non-goals preserve flexibility for later phases.


## System Overview

At a high level, Glia Phase 2 is structured as a durable asynchronous system
with explicit execution boundaries:

Client / Interface
↓
API Layer (Authenticated, Streaming)
↓
Durable System of Record (PostgreSQL)
↓
Background Workers (Retry-safe, Idempotent)

Unlike Phase 1, durable storage is now the system backbone, not an
auxiliary concern.


## System Components

API Layer

The API layer acts as a user-facing coordination boundary.

Responsibilities
	•Authentication and user identity enforcement
	•Streaming interaction via a fixed SSE protocol
	•Persisting conversation state
	•Enqueueing background extraction tasks
	•Exposing derived artifacts via query APIs

Non-responsibilities
	•Long-running or retry-prone computation
	•AI extraction or synthesis
	•Recovery or compensation logic

The API layer must remain responsive and side-effect minimal.


## Durable System of Record

PostgreSQL serves as the authoritative system of record.

Stored entities
	•Conversations
	•Messages
	•Cards (derived artifacts)
	•Card extraction runs (execution metadata)

Design principles
	•Conversations and messages are canonical user state
	•Cards are explicitly marked as derived data
	•Execution metadata enables replay, debugging, and auditability

Durable storage replaces in-memory coordination from Phase 1.


## Background Processing

Background workers perform non-interactive, failure-tolerant computation.

Responsibilities
	•Extracting cards from conversation state
	•Applying deterministic fallback logic when AI extraction fails
	•Writing derived artifacts atomically
	•Recording execution outcomes and metadata

Execution properties
	•At-least-once delivery semantics
	•Idempotent execution via source hashing
	•Explicit retry and failure boundaries
	•Safe replacement of derived state

Workers are designed to be disposable and restartable without data loss.


## Data & State Model (High-Level)

Phase 2 introduces a clear distinction between canonical state and
derived state:
	•Canonical
	•Conversations
	•Messages
	•User identity
	•Derived
	•Cards
	•Extraction summaries
	•Model-dependent interpretations

Key properties:
	•Derived state can be fully replaced
	•Duplicate or concurrent extraction attempts are deduplicated
	•All state transitions are safe to replay

Consistency is eventual and scoped to a conversation.


## Request & Execution Flow

A typical interaction follows this sequence:
	1.An authenticated client submits a chat request
	2.The API streams assistant output using explicit completion semantics
	3.Conversation messages are persisted durably
	4.A background extraction task is enqueued
	5.Workers extract cards asynchronously
	6.Derived cards are atomically replaced
	7.Execution metadata is recorded for observability
	8.Clients query derived state independently of chat completion

No user-facing request depends synchronously on background extraction.


## Reliability, Failure, and Fallback Semantics

Phase 2 explicitly models failure as part of normal operation:
	•AI extraction may fail due to auth, quota, network, or schema errors
	•Failures do not block user interaction
	•Deterministic rule-based fallback ensures continuity
	•Fallback execution is recorded, not hidden

A successful outcome is defined as:
	•Cards are produced (via AI or fallback)
	•Execution is recorded with explicit metadata
	•No canonical state is corrupted


## Observability & Accountability

Phase 2 introduces execution-level observability:
	•Each extraction run is recorded with:
	•provider
	•model
	•latency
	•fallback usage
	•failure classification
	•Logs are correlated via request and conversation identifiers
	•Operators can trace:
	•What ran
	•Why it ran
	•Whether it succeeded
	•How it failed

This enables debugging, tuning, and future system evolution.


## Security & Privacy Notes

Phase 2 enforces:
	•Mandatory authentication for all user-scoped operations
	•User-level isolation across API, storage, and execution layers
	•No cross-user visibility of conversations or derived artifacts

Details related to:
	•Model prompts
	•Data retention policies
	•Infrastructure secrets

are intentionally excluded from this document.


## Related Architecture Decisions

The Phase 2 architecture reflects the following accepted ADRs:
	•ADR 0005 — Persist conversations and cards in a shared system of record
	•ADR 0006 — Enforce user identity and isolation as a system invariant
	•ADR 0007 — Idempotent, replay-safe background extraction with atomic replacement
	•ADR 0008 — LLM-first extraction with deterministic fallback and observability

Earlier ADRs (0001–0004) remain in effect unless explicitly superseded.


## Evolution Beyond Phase 2

Subsequent phases are expected to introduce:
	•Multi-tenant and organization-aware access control
	•Stronger lifecycle and retention management
	•Expanded metrics, alerting, and operational dashboards
	•Model evaluation, governance, and versioning strategies

When such changes occur, a new phase document will be introduced rather than
retroactively redefining Phase 2.


## Final Note

Phase 2 establishes Glia as a system that can explain its own behavior.

This phase prioritizes architectural memory, operational trust, and
future decision flexibility over short-term feature velocity.
