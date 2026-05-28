# Research: Document Upload and Management

## Decision 1: Local file storage with abstraction

**Decision:** Store uploaded files locally outside `wwwroot` and expose file operations through an `IFileStorageService` interface.

**Rationale:**
- The existing application is a training-focused, offline-first Blazor Server app.
- Local storage is required by the feature spec and existing README guidance.
- Storing files outside `wwwroot` protects them from direct URL access and enables authorization checks.
- An abstraction preserves a future migration path to Azure Blob Storage or another provider without changing business logic.

**Alternatives considered:**
- Storing files in `wwwroot`: rejected for security reasons.
- Saving files as BLOBs in SQLite: rejected due to performance, complexity, and the explicit requirement for file storage outside web-accessible directories.
- Cloud-only storage: rejected because the training branch must remain local and offline-capable.

## Decision 2: Service-layer upload workflow with validation and authorization

**Decision:** Implement a `DocumentService` that performs upload validation, file scanning, authorization, storage, metadata save, and audit logging.

**Rationale:**
- The current repository already uses service-layer abstractions for project, task, and notification operations.
- Centralizing document workflows in a service keeps UI pages thin and consistent with existing architecture.
- Service-level authorization prevents IDOR by enforcing access rules even if page-level checks are bypassed.

**Alternatives considered:**
- Handling upload logic directly in Razor pages: rejected because it would duplicate business rules and reduce testability.
- Using controller-based APIs only: unnecessary for an internal Blazor Server implementation; service endpoints are sufficient.

## Decision 3: Document metadata, sharing, and audit models

**Decision:** Add `Document`, `DocumentShare`, and `DocumentAudit` entities to capture metadata, share relationships, and activity logs.

**Rationale:**
- The feature needs searchable metadata and audit tracking for uploads, downloads, edits, deletes, and shares.
- `DocumentShare` enables sharing with users and team identifiers while keeping the schema simple.
- `DocumentAudit` supports the requirement for document-related activity tracking without overloading the document table.

**Alternatives considered:**
- A flat metadata-only model: rejected because it would not support sharing and audit requirements cleanly.
- A separate tag entity: rejected for training simplicity; tags will be stored in a normalized string field with search parsing.

## Decision 4: Search and browse behavior aligned to current app patterns

**Decision:** Implement document listing and search using service queries over title, description, tags, uploader name, and associated project.

**Rationale:**
- The existing app already uses EF Core queries and service-layer filtering for tasks and projects.
- Search should return only permitted documents and respect project membership, ownership, and administrator roles.
- Reusing the current page/service pattern keeps the feature consistent and easier to maintain.

**Alternatives considered:**
- Full-text search engine: rejected because it is out of scope for the training app and would introduce external dependencies.
- Client-side filtering of all records: rejected because it would not scale to the target dataset and performance goals.

## Decision 5: Training-safe malware scanning abstraction

**Decision:** Add a `IFileScanService` abstraction and implement a local training stub that validates file metadata and optionally performs a simple content check.

**Rationale:**
- The feature spec requires virus/malware scanning.
- The repository has no existing external scanner integration, and training constraints prohibit external cloud services.
- An abstraction lets training use a safe stub while leaving a migration path to a real scanner later.

**Alternatives considered:**
- Skipping scanning entirely: rejected because it violates the requirement.
- Integrating a vendor scanner immediately: rejected because it would introduce external dependencies outside the training scope.
