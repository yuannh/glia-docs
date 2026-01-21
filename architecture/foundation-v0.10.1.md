# Glia Backend Architecture — Foundation v0.10.1

**Status**: Frozen  
**Date**: 2026-01-21  
**Related code version**: `glia-core@v0.10.1`

---

## What Changed Since v0.10.0
Foundation v0.10.1 hardens **entity profile** outputs with fail-closed validation.

Key additions:
- Entity profile outputs are validated before persistence.
- Invalid outputs are rejected and never written.
- Failure signals and counters are surfaced for observability.

---

## Entity Profile Hard Validation
Entity profile generation now enforces hard constraints.

- Required fields must exist (name/body; subtitle for people).
- Word and paragraph ranges are enforced per kind.
- Banned phrases are rejected.
- Validation failures result in drop with explicit failure signals.

This closes the loop between contracts and runtime enforcement.

---

## Explicit Non-Goals
Foundation v0.10.1 does not introduce:

- Auto-repair of invalid profiles
- Relaxed validation or soft warnings
- Changes to profile schema shape

---

## What This Enables Next
Foundation v0.10.1 enables:

- Reliable, render-ready entity profiles
- Auditable rejection of invalid outputs
- Safer iteration on profile contracts

---

## Related Documents
- `DECISIONS/0030-entity-profile-hard-validation.md`
- `architecture/foundation-v0.10.0.md`
