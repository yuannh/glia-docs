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

## Foundation Snapshots

Foundation documents are time-scoped baselines that freeze accepted
constraints and invariants for a specific backend version.

- [foundation-v0.3.0](./foundation-v0.3.0.md)
- [foundation-v0.4.0](./foundation-v0.4.0.md)
- [foundation-v0.5.0](./foundation-v0.5.0.md)
- [foundation-v0.6.0](./foundation-v0.6.0.md)
- [foundation-v0.7.0](./foundation-v0.7.0.md)
- [foundation-v0.8.0](./foundation-v0.8.0.md)
- [foundation-v0.9.0](./foundation-v0.9.0.md)
- [foundation-v0.10.0](./foundation-v0.10.0.md)