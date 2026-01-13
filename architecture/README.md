# Architecture

This directory documents the architectural evolution of Glia over time.

Each document represents a **time-scoped architectural snapshot** that reflects
the system’s accepted constraints, boundaries, and trade-offs at a specific
stage of development. These documents are not retroactively rewritten as the
system evolves.

## Architecture Phases

- [Phase 1 — Asynchronous Single-User System](./phase-1.md)  
  Establishes an asynchronous, decoupled foundation focused on clarity,
  failure tolerance, and non-blocking user interaction.

- [Phase 2 — Durable, Multi-User, Observable System](./phase-2.md)  
  Introduces durable persistence, enforced user identity and isolation,
  idempotent background processing, and system-level observability as
  first-class concerns.

Each phase builds on prior decisions while explicitly documenting newly
accepted constraints and invariants.

Future phases will be added as separate documents rather than modifying
existing ones, preserving architectural intent and decision history.
