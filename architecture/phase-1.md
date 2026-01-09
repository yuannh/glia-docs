# Architecture — Phase 1

> **Document Scope**
>
> This document describes the architecture of Glia as of **Phase 1 (early 2026)**.
>
> Phase 1 focuses on establishing a reliable asynchronous foundation for a
> single-user, reflection-oriented system. Architectural decisions documented here
> are made under that constraint and are expected to evolve in later phases.
>
> This document is updated intentionally and does not attempt to retroactively
> rewrite architectural history.

---

## Phase Definition

**Phase 1: Asynchronous Single-User System**

Primary characteristics:
- Single-user or tightly scoped usage
- Asynchronous processing as the default execution model
- Emphasis on clarity, failure tolerance, and operational simplicity

Explicitly out of scope for Phase 1:
- Multi-user isolation and tenancy
- Long-term durable storage guarantees
- Complex orchestration or scheduling pipelines

---

## Design Goals

The Phase 1 architecture is shaped by the following constraints:

- **Non-blocking user interaction**  
  User-facing requests must remain responsive regardless of downstream processing time.

- **Clear separation of concerns**  
  API coordination, background execution, and shared state must remain loosely coupled.

- **Shared, visible state across processes**  
  Intermediate results and task progress must be observable across system boundaries.

- **Operational simplicity over extensibility**  
  The system favors explicit, understandable flows over future-proof abstractions.

---

## Explicit Non-goals

During Phase 1, the architecture intentionally does **not** aim to:

- Provide a stable or public API contract
- Expose model, prompt, or data-handling internals
- Optimize for multi-tenant scalability
- Serve as a production SLO or compliance reference

These omissions are deliberate and protect early learning velocity.

---

## System Overview

At a high level, Glia Phase 1 is structured as an asynchronous pipeline:

Client / Interface
↓
API Layer (FastAPI)
↓
Shared State & Queue (Redis)
↓
Background Workers (Celery)

Each layer has a narrow, explicitly defined responsibility.

---

## System Components

### API Layer (FastAPI)

**Responsibilities**
- Request validation and boundary enforcement
- User-facing orchestration (including streaming / SSE)
- Enqueueing background work and presenting observable results

**Non-responsibilities**
- Long-running extraction or synthesis
- Blocking or compute-heavy workflows

The API layer is treated as a coordination boundary, not a computation surface.

---

### Shared State & Coordination (Redis)

Redis serves as a **coordination and visibility layer**, not a source of truth.

**Responsibilities**
- Task queue / broker for background jobs
- Short-to-medium-lived shared state across processes
- Visibility into task progress and partial results

**Rationale**
- Low latency and simple operational model
- Strong cross-process visibility
- Predictable failure and recovery behavior

Redis is intentionally used to avoid coupling request handling to durable storage semantics.

---

### Background Processing (Celery Workers)

**Responsibilities**
- Long-running extraction, analysis, and post-processing
- Isolated execution of background tasks

**Execution properties**
- At-least-once execution semantics
- Explicit retry and failure boundaries
- Horizontally scalable independent of API traffic

Workers are treated as disposable and failure-tolerant by design.

---

## Data & State Model (High-Level)

- State is scoped primarily at the **conversation level**
- Background tasks are expected to be **idempotent**
- Partial, delayed, or incremental results are considered normal

The system assumes:
- Failures will occur
- Work may be retried
- State transitions must be safe to replay

Consistency is eventual and contextual rather than immediate or global.

---

## Request & Execution Flow

A typical request follows this sequence:

1. Client submits a request to the API
2. API validates input and enqueues background work
3. Worker processes the task asynchronously
4. Shared state is updated as progress or results emerge
5. Client observes updates via polling or streaming

The system does not assume a single synchronous completion point.

---

## Reliability & Observability

Observability is scoped to system boundaries:

- Logging is separated between API and worker contexts
- Correlation identifiers may be propagated across enqueue and execution boundaries
- Retries are explicit, bounded, and observable

The goal is to surface failure modes clearly rather than conceal them.

---

## Security & Privacy Notes

This document intentionally omits details related to:
- Models and prompts
- Data retention and storage
- Access control and authentication

Phase 1 guiding principles:
- Least privilege between components
- Minimal exposure of sensitive context
- Clear trust boundaries between system layers

---

## Evolution Beyond Phase 1

Subsequent phases are expected to introduce changes such as:
- Stronger durability guarantees
- More explicit context lifecycle management
- Multi-user isolation and access control

When such shifts occur, new documents will be introduced rather than
retroactively redefining Phase 1 decisions.
