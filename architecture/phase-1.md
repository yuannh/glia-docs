# Architecture — Phase 1

> **Document Scope**
>
> This document describes the architecture of Glia as of **Phase 1 (early 2026)**.
>
> Phase 1 establishes a reliable asynchronous foundation for a
> single-user, reflection-oriented system.
> The architecture described here reflects a specific set of accepted
> system constraints and decisions, and is expected to evolve in later phases.
>
> This document is intentionally time-scoped and does not attempt to
> retroactively rewrite architectural history.

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

## Design Goals and Accepted Constraints

The Phase 1 architecture reflects the following accepted constraints:

- **Non-blocking user interaction**  
  User-facing requests remain responsive regardless of downstream
  processing duration.

- **Clear separation of concerns**  
  Interaction coordination, background execution, and shared state are
  explicitly decoupled.

- **Shared, visible state across processes**  
  Intermediate results and task progress are observable across system
  boundaries.

- **Operational simplicity over extensibility**  
  Explicit, understandable flows are favored over future-proof abstractions.

These constraints prioritize correctness, debuggability, and learning
velocity over completeness.

---

## Explicit Non-goals

During Phase 1, the architecture intentionally does **not** aim to:

- Provide a fully stable or public-facing API surface
- Expose model, prompt, or internal data-handling details
- Optimize for multi-tenant scalability
- Serve as a production SLO, compliance, or governance reference

These omissions are deliberate and preserve flexibility for future phases.

---

## System Overview

At a high level, Glia Phase 1 is structured as an asynchronous pipeline:

Client / Interface  
↓  
API Layer  
↓  
Shared Coordination & State  
↓  
Background Workers  

Each layer has a narrow, explicitly defined responsibility.

---

## System Components

### API Layer

The API layer serves as a **coordination boundary**, not a computation surface.

**Responsibilities**
- Request validation and boundary enforcement
- User-facing orchestration, including streaming responses
- Enqueueing background work and exposing observable outcomes

**Non-responsibilities**
- Long-running extraction or synthesis
- Blocking or compute-heavy workflows

---

### Shared Coordination & State

Shared infrastructure provides **coordination and visibility**, not durable truth.

**Responsibilities**
- Task queue / broker for background execution
- Short- to medium-lived shared state across processes
- Visibility into task progress and partial results

This layer intentionally avoids durable storage semantics to prevent
tight coupling between interaction and persistence.

---

### Background Processing

Background workers execute **long-running and failure-tolerant tasks**.

**Responsibilities**
- Extraction, analysis, and post-processing
- Isolated execution independent of API request lifecycles

**Execution properties**
- At-least-once execution semantics
- Explicit retry and failure boundaries
- Horizontal scalability independent of API traffic

Workers are treated as disposable and failure-tolerant by design.

---

## Data & State Model (High-Level)

- State is scoped primarily at the **conversation level**
- Derived artifacts are produced asynchronously from conversation state
- Partial, delayed, or incremental results are considered normal

The system assumes:
- Failures will occur
- Work may be retried
- State transitions must be safe to replay

Consistency is eventual and contextual rather than immediate or global.

---

## Request & Execution Flow

A typical interaction follows this sequence:

1. The client submits a request to the API
2. The API validates input and initiates streaming output
3. Background work is enqueued independently of streaming
4. Workers process tasks asynchronously
5. Shared state is updated as progress or results emerge
6. The client observes outcomes via streaming or subsequent queries

No single synchronous completion point is assumed.

---

## Reliability & Observability

Observability is scoped to system boundaries:

- Logging is separated between API and background execution contexts
- Correlation identifiers may propagate across enqueue and execution boundaries
- Retries are explicit, bounded, and observable

The system favors surfacing failure modes clearly rather than concealing them.

---

## Security & Privacy Notes

This document intentionally omits details related to:
- Models and prompts
- Data retention and storage
- Authentication and access control

Phase 1 guiding principles:
- Least privilege between components
- Minimal exposure of sensitive context
- Clear trust boundaries between system roles

---

## Related Architecture Decisions

The Phase 1 architecture reflects the following accepted
Architecture Decision Records:

- **ADR 0001** — Streaming chat with explicit completion semantics
- **ADR 0002** — Fixed SSE event protocol for chat streaming
- **ADR 0003** — Cards as derived facts, not immediate chat side effects
- **ADR 0004** — Background card extraction via dedicated workers

---

## Evolution Beyond Phase 1

Subsequent phases are expected to introduce changes such as:
- Durable persistence and stronger consistency guarantees
- Explicit context lifecycle management
- Multi-user isolation and access control
- Expanded observability and operational tooling

When such shifts occur, new phase documents will be introduced rather than
retroactively redefining Phase 1.
