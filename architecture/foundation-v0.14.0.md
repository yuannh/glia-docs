 # Glia Backend Architecture — Foundation v0.14.0
 
 **Status**: Frozen  
 **Date**: 2026-02-01  
 **Related code version**: `glia-core@v0.14.0`
 
 ---
 
 ## What Changed Since v0.10.1
 Foundation v0.14.0 introduces a **first-class Notes subsystem** with
 server-derived card fields, presigned uploads, and asset binding to support
 editable user notes with images.

This version range also consolidates link span behavior for story and timeline
rendering, ensuring spans remain short, semantic, and non-overlapping.
 
 Key additions:
 - Notes are persisted as a first-class domain object with user isolation.
 - List responses are card-focused; full content is read via detail.
 - Server derives title, excerpt, and cover image fields.
 - Uploads use presigned PUT to avoid routing media through core services.
 - Assets can be bound/unbound to notes based on content references.
 - Request correlation and audit-friendly logging are enforced.
 
 ---
 
 ## Notes Subsystem (First-Class User Notes)
 Notes are distinct from story/cards and are stored as their own durable objects.
 This establishes a clean separation between system-derived narratives and
 user-authored note content.
 
Summary: v0.14.0 introduces a first-class Notes subsystem with user-scoped
storage and stable list/detail semantics to support client editing workflows.

 Constraints:
 - Each note is owned by a single user and must be isolated by user identity.
 - List views are derived, lightweight, and stable.
 - Detail views expose full content for editing.
 
 ---
 
 ## Server-Derived Card Fields
 The server derives and persists presentation fields to keep rendering stable
 and consistent across clients.
 
 - Title is derived from content when not explicitly provided.
 - Excerpt is derived from content with markdown stripped and whitespace normalized.
 - Cover image is derived from the first markdown image reference when present.
 
 This makes list rendering deterministic and client-agnostic.
 
 ---
 
 ## Presigned Uploads and Asset Binding
 Media uploads are handled via presigned URLs in compatible object storage.
 This preserves API performance and prevents large media from transiting core.
 
 Assets are bound to notes by parsing content references and matching on
 storage keys or asset IDs when present. Assets may be unbound when removed
 from content, enabling future garbage collection.
 
 ---
 
## Nav V2 Link Span Rules
Story and timeline spans are constrained to preserve readability and stable
navigation behavior.

Constraints:
- Story/timeline spans must be short, semantic phrases (20–60 characters).
- Spans must not overlap entity spans or other story spans.
- Story titles are not used as body anchors; only explicit spans are allowed.

---

 ## Observability and Correlation
 Request correlation is mandatory for auditing and supportability.
 
 - Request IDs are generated or propagated for each request.
 - Logs include correlation identifiers and high-signal fields for notes and uploads.
 
 ---
 
 ## Explicit Non-Goals
 Foundation v0.14.0 does not introduce:
 
 - Full-text search over notes
 - Automatic image transformations or thumbnails
 - Automatic cleanup of unbound assets (future work)
 
 ---
 
 ## What This Enables Next
 
 - Thumbnail generation for note cards
 - Search and filtering on derived fields
 - Orphan asset garbage collection
 - Multi-device conflict resolution enhancements
 
 ---
 
 ## Related Documents
 - `DECISIONS/0031-core-notes-as-first-class-user-notes.md`
 - `architecture/phase-2.md`
 - `architecture/foundation-v0.10.1.md`
