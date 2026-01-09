# Security Policy

## Scope and Intent

Glia is an **early-stage, actively evolving system**.
This repository contains **public-facing documentation and architectural
decisions**, not the full production implementation.

This document describes how security issues should be reported and
clarifies the **current security boundaries and limitations** of the system.

It is not a statement of full security maturity or compliance.

---

## Current Security Posture

At the current stage of development:

- Glia is **not production-hardened**
- Formal threat modeling is **incomplete**
- Authentication, authorization, and user isolation are **not finalized**
- Data persistence, retention, and deletion guarantees are **not yet defined**

As such, Glia **should not be used to store or process highly sensitive,
regulated, or personal data**.

---

## Data Handling and Privacy

The public documentation intentionally omits implementation details related to:

- Data storage mechanisms
- Encryption at rest
- Data retention policies
- Backup and recovery strategies

Any data handling behavior described in public documents reflects
**architectural intent**, not operational guarantees.

---

## Authentication and Access Control

At this stage:

- Authentication mechanisms may be incomplete or placeholder
- User isolation may not be fully enforced
- Authorization boundaries may evolve significantly

No assumptions should be made about access control robustness until
explicitly documented in later phases.

---

## Reporting a Security Issue

If you believe you have discovered a **security vulnerability**, please report it responsibly.

### How to Report

- Do **not** open a public issue
- Do **not** disclose details publicly

Instead, contact the maintainers privately at:

**security@gliahq.com**  
*(or the preferred private contact channel if otherwise communicated)*

Please include:
- A clear description of the issue
- Steps to reproduce (if applicable)
- Potential impact assessment
- Any supporting logs or evidence

---

## What Qualifies as a Security Issue

Examples include:
- Unauthorized access to data or functionality
- Privilege escalation
- Remote code execution
- Authentication or authorization bypass
- Data leakage across user or system boundaries

---

## What Does *Not* Qualify as a Security Issue

The following are generally **out of scope** for security reporting:

- Feature requests
- Performance issues
- Expected behavior of experimental or incomplete features
- Bugs that do not impact confidentiality, integrity, or availability

---

## Disclosure Policy

Valid security issues will be:
- Acknowledged promptly
- Investigated privately
- Addressed according to severity and development priorities

Public disclosure will occur **only after a fix or mitigation is available**,
or by mutual agreement.

---

## Evolution

Security posture, controls, and guarantees are expected to evolve
significantly as Glia progresses through later architectural phases.

This document will be updated as meaningful security milestones are reached.

---

## Guiding Principle

> Security documentation exists to set **clear expectations**, not to
> create false confidence.

If a security property is not explicitly documented, it should be
assumed **not guaranteed**.
