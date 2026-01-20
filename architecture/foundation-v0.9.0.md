# Glia Backend Architecture — Foundation v0.9.0

**Status**: Frozen  
**Date**: 2026-01-19  
**Related code version**: `glia-core@v0.9.0`

---

## What Changed Since v0.8.0
Foundation v0.9.0 hardens the DOT / LINE / STORY pipeline with **contract gating**
and makes story evidence **mandatory and auditable**.

Key additions:
- Story evidence must be persisted and validated before write.
- DOT/LINE/STORY outputs are hard gated by validators.
- Story link objects and narrative tokens are frozen for iOS rendering.

---

## Story Evidence as a Hard Requirement
Story outputs are accepted only when evidence is valid and traceable.

- Evidence must reference valid user messages.
- Evidence must match topic and conversation scope.
- Invalid evidence causes retry once, then drop.
- Evidence is persisted and returned by the API.

This makes story a verifiable artifact, not a best-effort summary.

---

## Contract Validators as Hard Gates
DOT, LINE, and STORY are now enforced by runtime validators.

- Validators are required for every output.
- Invalid outputs never reach persistence.
- Retry-then-drop is the default failure policy.
- Contracts are the only valid source of output rules.

---

## Story Link Objects and Narrative Tokens
Story links are standardized for deterministic rendering.

- Links are resolved into a unified links object.
- Narrative text uses explicit link tokens.
- Tokens and links must align one-to-one.
- The frontend must not infer links from raw text.

---

## Explicit Non-Goals
Foundation v0.9.0 does not introduce:

- UI/experience-layer story composition
- Automatic semantic inference of links
- Relaxed validation or “best-effort” persistence

---

## What This Enables Next
Foundation v0.9.0 enables:

- iOS-ready, stable story rendering
- Auditable, evidence-backed narratives
- Safer evolution of DOT/LINE/STORY without drift

---

## Related Documents
- `DECISIONS/0022-story-evidence-mandatory.md`
- `DECISIONS/0023-dot-line-story-hard-gates.md`
- `DECISIONS/0024-story-link-objects-frozen.md`
- `architecture/foundation-v0.8.0.md`
