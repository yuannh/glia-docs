# Architecture Decision Records (ADR)

This directory contains **Architecture Decision Records (ADRs)** for Glia.

ADRs document **irreversible or high-leverage architectural decisions** made
during the evolution of the system.  
They capture *why* a decision was made, not how it was implemented.

---

## What an ADR Is (and Is Not)

### An ADR **is**:
- A record of a **significant architectural decision**
- A description of the **context, constraints, and trade-offs**
- A durable explanation that remains useful even as implementation changes
- A reference point for future evolution or superseding decisions

### An ADR **is not**:
- A development log or changelog
- A list of tasks completed in a sprint
- A dump of implementation details
- A replacement for architecture snapshots or code comments

If a change can be fully understood by reading the code or commit diff,
it likely does **not** require an ADR.

---

## When to Write an ADR

An ADR should be written **only when a decision meets at least two of the following criteria**:

- The decision **changes system semantics** (e.g. lifecycle, failure behavior)
- The decision introduces or removes a **system boundary** (process, role, responsibility)
- The decision is **hard to reverse** without significant cost
- Future contributors might reasonably ask:  
  *“Why was it designed this way?”*

Examples of ADR-worthy decisions:
- Defining explicit completion semantics for streaming APIs
- Fixing a protocol contract that clients must depend on
- Defining what constitutes “system state” vs “derived data”
- Moving work across process or execution boundaries

---

## When *Not* to Write an ADR

Do **not** write an ADR for:
- Refactors with unchanged behavior
- Bug fixes
- Performance optimizations that do not alter semantics
- Temporary experiments or prototypes
- Incremental feature additions that follow existing patterns

ADRs should be **rare, deliberate, and high-signal**.

---

## Time-Ordered, Decision-Driven

ADRs in this directory are:
- **Numbered sequentially**
- **Ordered by the time the decision was made**
- **Written at the moment the decision becomes real**, not retroactively rewritten

The ordering reflects **decision points**, not release versions or phases.

---

## Relationship to Other Documentation

- `README.md`  
  Defines system intent and long-term orientation.

- `architecture/phase-*.md`  
  Describes **what the system looks like at a given phase**.

- `DECISIONS/*.md` (this directory)  
  Records **why the system took a particular shape**.

Architecture documents may evolve.  
ADRs are **append-only**.

---

## ADR Lifecycle

Each ADR has a status:

- **Accepted** — the decision is in effect
- **Superseded** — the decision has been replaced by a later ADR

ADRs are **not edited** after acceptance.
If a decision changes, a **new ADR** must be written that explicitly supersedes the old one.

---

## ADR Structure

Each ADR should follow a consistent structure:

- Title, status, and date
- Context (constraints and problem framing)
- Decision (clear, testable statement)
- Consequences (positive and trade-offs)
- Alternatives considered
- Validation / acceptance criteria
- Links to **public, stable documentation only**

Private implementation details must not be referenced directly.

---

## Public vs Private

This repository contains **public ADRs**.

Public ADRs:
- Do not link to private code paths
- Do not expose internal infrastructure or secrets
- Focus on architectural intent and system semantics

Implementation details live in private repositories and are intentionally excluded.

---

## Guiding Principle

> ADRs exist to preserve **architectural memory**.

They allow the system to evolve without losing the reasoning that shaped it,
and they prevent future changes from unknowingly violating earlier constraints.

If an ADR no longer feels relevant, it should be **superseded**, not erased.

---
