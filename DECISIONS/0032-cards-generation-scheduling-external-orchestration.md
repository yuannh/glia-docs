# ADR 0032: Cards generation scheduling via external orchestration

**Status:** Accepted  
**Date:** 2026-02-04  
**Decision Makers:** Glia Core

## Context
We need a reliable daily cards-generation workflow in production while allowing
developers to trigger the same logic immediately for tuning and iteration.
Running AI extraction synchronously inside API requests is too slow and unstable.
The system already has an idempotent, queued execution path for cards extraction.

## Decision
- **Production:** use external orchestration (e.g., cron/scheduler) to trigger
  the cards-generation workflow asynchronously.
- **Development/Tuning:** provide a controlled manual trigger to run the same
  workflow on demand.
- **Scheduler vs executor separation:** scheduling selects scope and enqueues;
  execution runs asynchronously in background workers.
- **API layer only enqueues**; it never performs synchronous AI extraction.

## Alternatives Considered
1. **Built-in scheduler (e.g., background beat service)**  
   Rejected for v1 due to operational complexity.
2. **Synchronous extraction in API requests**  
   Rejected due to latency and reliability concerns.
3. **Reuse debugging-only paths in production**  
   Rejected due to security and operational risk.

## Consequences
**Pros**
- Clear separation of orchestration and execution
- Faster iteration for tuning without waiting for daily runs
- Safer API performance characteristics
- Easier operational control and rollback

**Cons / Trade-offs**
- Requires external scheduling configuration
- Requires careful guardrails on scope and load
- Observability relies on logs unless batch runs are persisted later

## Notes
- Default scope targets recently active topics to avoid full scans.
- Manual triggering is controlled and not exposed as a public interface.
- Future enhancements may include user-local scheduling, run persistence,
  and more granular controls.
