# FAQ

This document answers common questions about Glia, its current status,
and the scope of this repository.

It is intended for engineers, researchers, and technically curious readers,
not end users.

---

## What is Glia?

Glia is a private AI system conceived as a **long-horizon companion for reflection,
context continuity, and personal meaning-making**.

Rather than optimizing for short, transactional interactions, Glia explores
how an AI system can participate—carefully and non-intrusively—in an individual’s
ongoing process of growth, decision-making, and self-understanding.

---

## What does this repository contain?

This repository contains **public-facing documentation only**, including:

- High-level architectural snapshots
- Architecture Decision Records (ADRs)
- Design principles and system boundaries
- Security posture statements

It does **not** contain:
- Source code
- Models or prompts
- Infrastructure configuration
- Production credentials or internal tooling

---

## Is Glia open source?

No.

This repository is **public documentation**, not an open-source implementation.
The core system implementation remains private.

---

## Is Glia production-ready?

Glia is under active development.

A **legacy implementation** currently supports live usage and testing.
This repository documents the **architecture and design decisions of a
next-generation backend** being developed in parallel.

The architecture described here should not be interpreted as a description
of the legacy production system.

---

## Why maintain public documentation for a private system?

This repository exists to:

- Clarify architectural intent and system boundaries
- Preserve architectural reasoning as the system evolves
- Enable thoughtful technical or research discussion
- Provide transparency **without exposing proprietary implementation details**

It is intentionally minimal and selective.

---

## Why is so much detail intentionally omitted?

Details related to:
- Models
- Prompts
- Data handling
- Storage strategies
- Authentication
- Infrastructure

are omitted by design.

The goal of this repository is to document **architectural decisions and system
semantics**, not implementation specifics that may change or expose sensitive details.

---

## What are “Phases” in the architecture documentation?

Phases represent **time-scoped architectural snapshots**, not promises or roadmaps.

For example:
- *Phase 1* describes the architecture as it existed during early 2026
- Later phases will introduce new constraints and capabilities

When the architecture meaningfully changes, a **new phase document** is added.
Earlier phase documents are not rewritten retroactively.

---

## What are Architecture Decision Records (ADRs)?

ADRs document **why specific architectural decisions were made**.

They capture:
- Context and constraints
- The decision itself
- Trade-offs and consequences
- Alternatives that were considered and rejected

ADRs are **append-only**.
If a decision changes, it is superseded by a new ADR rather than edited in place.

---

## Why does the system emphasize asynchronous behavior?

Glia is designed around the assumption that:
- Meaningful analysis takes time
- Partial results are valuable
- Failures are inevitable and should be observable

Asynchronous design allows the system to remain responsive, explicit,
and failure-tolerant without hiding complexity behind synchronous abstractions.

---

## Why are “cards” treated as derived data?

Cards are defined as **derived system facts**, not immediate UI artifacts.

This separation:
- Prevents chat interaction from being coupled to extraction latency
- Makes derived knowledge replayable and queryable
- Allows extraction strategies to evolve independently

This design choice is documented in the ADRs.

---

## Does Glia store personal data?

Details about data handling, retention, and storage are intentionally omitted
from public documentation.

See `SECURITY.md` for high-level security and privacy principles.

---

## Can I integrate with Glia or build on top of it?

At this stage, Glia is **not a public platform** and does not expose
stable integration contracts.

This repository should not be treated as an API specification or SDK reference.

---

## Is this documentation stable?

The documentation reflects the system’s **current architectural understanding**.

- ADRs are immutable once accepted
- Architecture snapshots are time-scoped
- High-level documents may evolve as the system matures

Changes are made deliberately and with clear intent.

---

## Who is this documentation for?

This repository is intended for:
- Engineers
- System designers
- Researchers
- Technically curious collaborators

It is **not** intended as:
- End-user documentation
- Marketing material
- A feature roadmap

---

## Where should I start reading?

Recommended order:
1. `README.md` — system intent and scope
2. `architecture/phase-1.md` — architectural snapshot
3. `DECISIONS/` — reasoning behind key decisions
4. `SECURITY.md` — security posture

---

## Final note

Glia is an exploration of long-horizon, reflective AI systems.

This repository exists to document *how such a system is thought about*,
not to present a finished product.
