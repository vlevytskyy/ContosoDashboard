# Contract: Document Management Service

## Purpose
This document defines the domain contract for document management operations in ContosoDashboard.
The contract is intended for service-layer implementation and any Razor page interactions.

## Domain Models

### DocumentMetadata

- `DocumentId` (int)
- `Title` (string)
- `Description` (string)
- `Category` (string)
- `Tags` (string)
- `AssociatedProjectId` (int?)
- `UploadedByUserId` (int)
- `UploadedByUserName` (string)
- `UploadDateTime` (DateTime)
- `FileSize` (long)
- `ContentType` (string)
- `OriginalFileName` (string)
- `FilePath` (string)
- `CanEdit` (bool)
- `CanDelete` (bool)
- `CanShare` (bool)

### DocumentQueryOptions

- `SearchText` (string?)
- `Category` (string?)
- `ProjectId` (int?)
- `SharedWithMe` (bool)
- `SortBy` (string?)
- `SortDescending` (bool)
- `PageIndex` (int)
- `PageSize` (int)

### ShareRequest

- `DocumentId` (int)
- `SharedWithUserId` (int?)
- `SharedWithTeamName` (string?)
- `Permissions` (string)

## Service Operations

### UploadDocumentAsync

Input:
- `title` (string)
- `description` (string?)
- `category` (string)
- `tags` (string?)
- `associatedProjectId` (int?)
- `uploaderUserId` (int)
- `fileStream` (Stream)
- `originalFileName` (string)
- `contentType` (string)
- `fileSize` (long)

Output:
- `DocumentId` (int)
- `success` (bool)
- `errors` (list of strings)

Behavior:
- Reject unsupported file types.
- Enforce 25 MB file size limit.
- Scan file for malware through `IFileScanService`.
- Save file through `IFileStorageService`.
- Persist document metadata only after storage succeeds.
- Log upload activity.

### GetDocumentsAsync

Input:
- `requestingUserId` (int)
- `queryOptions` (`DocumentQueryOptions`)

Output:
- `List<DocumentMetadata>`
- `TotalCount` (int)

Behavior:
- Return only documents owned by the user, shared with the user, or accessible through project membership/administration.
- Support search by title, description, tags, uploader name, and project.

### GetDocumentDetailsAsync

Input:
- `documentId` (int)
- `requestingUserId` (int)

Output:
- `DocumentMetadata` or null

Behavior:
- Return metadata only if the user is authorized.

### DownloadDocumentAsync

Input:
- `documentId` (int)
- `requestingUserId` (int)

Output:
- `Stream` or null
- `contentType` (string)
- Authorization failure if unauthorized

Behavior:
- Authorize access before opening the file stream.
- Log download action.

### ShareDocumentAsync

Input:
- `shareRequest` (`ShareRequest`)
- `requestingUserId` (int)

Output:
- `success` (bool)
- `errors` (list of strings)

Behavior:
- Allow owners to share documents with users or team names.
- Create `DocumentShare` records.
- Notify recipients through `NotificationService`.
- Log share action.

### UpdateDocumentMetadataAsync

Input:
- `documentId` (int)
- `title` (string)
- `description` (string?)
- `category` (string)
- `tags` (string?)
- `requestingUserId` (int)

Output:
- `success` (bool)
- `errors` (list of strings)

Behavior:
- Only the uploader may edit metadata.
- Log edit activity.

### ReplaceDocumentFileAsync

Input:
- `documentId` (int)
- `requestingUserId` (int)
- `fileStream` (Stream)
- `originalFileName` (string)
- `contentType` (string)
- `fileSize` (long)

Output:
- `success` (bool)
- `errors` (list of strings)

Behavior:
- Only the uploader may replace the document.
- Replace the stored file atomically.
- Preserve metadata and log replacement.

### DeleteDocumentAsync

Input:
- `documentId` (int)
- `requestingUserId` (int)

Output:
- `success` (bool)
- `errors` (list of strings)

Behavior:
- Allow uploader or project manager (for project documents) to delete.
- Delete file from storage and remove the metadata record.
- Log deletion.
