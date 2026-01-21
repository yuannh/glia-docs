# Glia Backend Architecture — Foundation v0.10.0

**Status**: Frozen  
**Date**: 2026-01-20  
**Related code version**: `glia-core@v0.10.0`

---

## What Changed Since v0.9.0
Foundation v0.10.0 upgrades narrative delivery from summary to structured
experience and hardens LINE as a production-grade input layer.

Key additions:
- Story outputs are structured into blocks with strict voice and length rules.
- LINE outputs produce production-grade patterns with evidence.
- LINE is restricted to DOT evidence (ids-only enforcement).
- Entity and artifact detail viewmodels are frozen for direct rendering.
- Story image selection is bounded and explainable.

---

## Story Structured Blocks
Story outputs are no longer a single summary.

- Outputs include opening, moment cards, bridges, and optional closing.
- Word counts and banned phrases are enforced by validators.
- Blocks remain evidence-backed and traceable.

---

## LINE Patterns as Production Outputs
LINE produces structured outputs for downstream use.

- Output types include thread_ready, entity_update, and thread_status_update.
- Evidence is required for every output.
- Validators hard gate all outputs.

---

## LINE Evidence Lock (Ids-Only)
LINE can only consume DOT-approved evidence.

- Allowed message ids come from DOT evidence.
- Any non-approved ids are rejected.
- This enforces a non-bypassable evidence chain.

---

## Entity and Artifact Detail Viewmodels
Detail APIs become render-ready contracts.

- Entity details include name, subtitle, body, footer, and linked references.
- Artifact details include title, content, image ids, and source links.
- Schemas are stable for direct iOS rendering.

---

## Story Image Selection
Story blocks include bounded image selection.

- 2–4 images when available.
- Priority for opening and climax cards.
- Each image ref includes a reason.

---

## Explicit Non-Goals
Foundation v0.10.0 does not introduce:

- Automatic inference outside evidence constraints
- Free-form narrative without structural validation
- UI-specific layout logic in backend

---

## What This Enables Next
Foundation v0.10.0 enables:

- Erica-level readable narratives
- iOS-ready detail rendering without heuristics
- Production-grade LINE outputs feeding story and entity layers

---

## Related Documents
- `DECISIONS/0025-story-structured-blocks.md`
- `DECISIONS/0026-line-patterns-production-outputs.md`
- `DECISIONS/0027-line-ids-only-enforcement.md`
- `DECISIONS/0028-entity-artifact-detail-viewmodels.md`
- `DECISIONS/0029-story-image-selection.md`
- `architecture/foundation-v0.9.0.md`
