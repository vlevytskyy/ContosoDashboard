# Quickstart: Implementing Document Upload and Management

## Overview
This quickstart describes the implementation path for the feature within the existing ContosoDashboard training application.

## Setup

1. Open the repository root in your editor.
2. Ensure the feature branch is checked out: `002-document-upload-management`.
3. Confirm that the app runs from the application folder:

```bash
cd ContosoDashboard/ContosoDashboard
dotnet run
```

4. Verify the app starts successfully and redirects to `/login`.

## Implementation Steps

1. Add document domain models:
   - `ContosoDashboard/ContosoDashboard/Models/Document.cs`
   - `ContosoDashboard/ContosoDashboard/Models/DocumentShare.cs`
   - `ContosoDashboard/ContosoDashboard/Models/DocumentAudit.cs`

2. Extend `ApplicationDbContext` in `ContosoDashboard/ContosoDashboard/Data/ApplicationDbContext.cs`:
   - Add `DbSet<Document> Documents`, `DbSet<DocumentShare> DocumentShares`, and `DbSet<DocumentAudit> DocumentAudits`.
   - Configure relationships and indexes for search performance.

3. Create a file storage abstraction:
   - `ContosoDashboard/ContosoDashboard/Services/IFileStorageService.cs`
   - `ContosoDashboard/ContosoDashboard/Services/LocalFileStorageService.cs`

4. Implement the document workflow service:
   - `ContosoDashboard/ContosoDashboard/Services/DocumentService.cs`
   - Validate file size and file type.
   - Generate a GUID-based storage filename.
   - Save the file to the local uploads folder.
   - Persist document metadata after storage succeeds.
   - Log audit events for uploads, downloads, replaces, deletes, and shares.
   - Enforce authorization rules.

5. Extend or integrate with `NotificationService`:
   - Send in-app notifications for document sharing events.

6. Add UI pages and components:
   - `Documents.razor` — My Documents list with filtering and search.
   - `ProjectDocuments.razor` — documents associated with a project.
   - `SharedWithMe.razor` — documents shared with the current user.
   - `DocumentDetails.razor` — preview, download, edit metadata, and replace/delete controls.
   - `DocumentUploadModal.razor` — upload form and file validation.

7. Wire the new services into dependency injection in `ContosoDashboard/ContosoDashboard/Program.cs`.

## Local Storage Setup

Create a secure local uploads folder outside `wwwroot`, for example:

```bash
mkdir -p ContosoDashboard/ContosoDashboard/App_Data/uploads
```

Ensure the app can write to this folder.

## Verification

1. Run the app and log in via `/login`.
2. Upload a supported file (`.pdf`, `.docx`, `.xlsx`, `.pptx`, `.txt`, `.jpeg`, `.png`).
3. Confirm the document appears in `My Documents` with title, category, project, and upload date.
4. Search by title, description, tags, uploader name, and project.
5. Share a document with another user and verify the share appears in `Shared with Me`.
6. Preview or download a document and verify the file is served through authorized endpoints.
7. Edit metadata, replace the file, and delete the document to confirm lifecycle behavior.

## Notes

- Use the existing training mock auth and authorization patterns.
- Preserve offline behavior; do not add external cloud dependencies in the feature branch.
- Keep the new feature explicit and easy to follow for learners.
