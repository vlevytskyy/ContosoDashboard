# Data Model: Document Upload and Management

## Entities

### Document

- `DocumentId` (int, PK)
- `Title` (string, required)
- `Description` (string, optional)
- `Category` (string, required)
- `Tags` (string, optional) — comma-separated tokens for search
- `AssociatedProjectId` (int?, FK to `Project.ProjectId`)
- `UploadedByUserId` (int, FK to `User.UserId`)
- `UploadDateTime` (DateTime, required)
- `FileSize` (long, required)
- `ContentType` (string, required, max 255)
- `FilePath` (string, required) — relative secured storage path
- `OriginalFileName` (string, required)
- `FileName` (string, required) — GUID-based storage name
- `IsDeleted` (bool, required, default false)
- `DeletedDateTime` (DateTime?, optional)

#### Relationships
- One `Document` belongs to one uploader (`User`).
- One `Document` optionally belongs to one project (`Project`).
- One `Document` can have many `DocumentShare` entries.
- One `Document` can have many `DocumentAudit` entries.

### DocumentShare

- `DocumentShareId` (int, PK)
- `DocumentId` (int, FK to `Document.DocumentId`)
- `SharedByUserId` (int, FK to `User.UserId`)
- `SharedWithUserId` (int?, FK to `User.UserId`)
- `SharedWithTeamName` (string?, optional)
- `ShareDateTime` (DateTime, required)
- `Permissions` (string, required) — e.g. `View`, `Edit`, `Download`

#### Purpose
- `SharedWithUserId` is used for individual user shares.
- `SharedWithTeamName` is used for teams/departments where explicit team entities do not currently exist.

### DocumentAudit

- `DocumentAuditId` (int, PK)
- `DocumentId` (int, FK to `Document.DocumentId`)
- `ActionType` (string, required) — e.g. `Upload`, `Download`, `Delete`, `Share`, `Edit`, `Replace`
- `PerformedByUserId` (int, FK to `User.UserId`)
- `PerformedAt` (DateTime, required)
- `Details` (string, optional)

## Indexes and Search Support

- Index `Document.Title`
- Index `Document.Category`
- Index `Document.UploadDateTime`
- Index `Document.AssociatedProjectId`
- Index `Document.UploadedByUserId`
- Index `Document.FilePath`
- Implement query-time search tokenization on `Document.Tags`, `Document.Title`, `Document.Description`, and uploader name.

## Storage and Security

- Files are stored outside `wwwroot` using a secure relative path, such as `uploads/{userId}/{projectId or personal}/{guid}.{ext}`.
- The stored `FilePath` is a relative path or storage key, not a user-controlled filename.
- The uploaded record is created only after the file write succeeds.

## Authorization Model

- Upload: authenticated users, optionally restricted by project membership when `AssociatedProjectId` is set.
- Download/preview: uploader, shared recipients, project managers of the associated project, and administrators.
- Edit metadata: uploader only.
- Replace/delete: uploader or project manager for project documents; administrators can manage all documents.
- Shared document visibility: recipients see documents in `SharedWithMe`.

## Integration Points

- `NotificationService` will send a notification when a document is shared.
- `ProjectService` may be used to validate membership when uploading documents to a project.
- `DocumentService` orchestrates file storage, DB persistence, scan validation, and audit logging.
