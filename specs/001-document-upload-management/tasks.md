# Tasks: Document Upload and Management

**Input**: Design documents from `specs/001-document-upload-management/`
**Prerequisites**: `plan.md`, `spec.md`, `data-model.md`, `contracts/`, `quickstart.md`

## Phase 1: Setup (Shared Infrastructure)

**Purpose**: Create the secure local storage and core service contracts for document upload.

- [ ] T001 Create secure local uploads folder at `ContosoDashboard/ContosoDashboard/App_Data/uploads`
- [ ] T002 Add `ContosoDashboard/ContosoDashboard/Services/IFileStorageService.cs`
- [ ] T003 Add `ContosoDashboard/ContosoDashboard/Services/LocalFileStorageService.cs`
- [ ] T004 Add `ContosoDashboard/ContosoDashboard/Services/IFileScanService.cs`
- [ ] T005 Add `ContosoDashboard/ContosoDashboard/Services/LocalFileScanService.cs`
- [ ] T006 Register `IFileStorageService`, `IFileScanService`, and `DocumentService` in `ContosoDashboard/ContosoDashboard/Program.cs`

---

## Phase 2: Foundational (Blocking Prerequisites)

**Purpose**: Add domain entities, persistence, and core document service behavior before user story work begins.

- [ ] T007 Add `ContosoDashboard/ContosoDashboard/Models/Document.cs`
- [ ] T008 Add `ContosoDashboard/ContosoDashboard/Models/DocumentShare.cs`
- [ ] T009 Add `ContosoDashboard/ContosoDashboard/Models/DocumentAudit.cs`
- [ ] T010 Update `ContosoDashboard/ContosoDashboard/Data/ApplicationDbContext.cs` with `DbSet<Document>`, `DbSet<DocumentShare>`, and `DbSet<DocumentAudit>` plus search-related indexes
- [ ] T011 Add `ContosoDashboard/ContosoDashboard/Services/DocumentService.cs`
- [ ] T012 Extend `ContosoDashboard/ContosoDashboard/Services/NotificationService.cs` to publish document share notifications and audit events
- [ ] T013 Create test project `ContosoDashboard/ContosoDashboard.Tests/ContosoDashboard.Tests.csproj` and reference `ContosoDashboard/ContosoDashboard/ContosoDashboard.csproj`
- [ ] T014 Add core unit tests in `ContosoDashboard/ContosoDashboard.Tests/DocumentServiceTests.cs` for document upload validation, persistence, and storage rollback behavior

---

## Phase 3: User Story 1 - Upload Document (Priority: P1) 🎯 MVP

**Goal**: Enable authenticated employees to upload documents with metadata, validate uploads, and store documents securely.

**Independent Test**: Upload a valid file <=25MB with title/category, verify the file is stored in `App_Data/uploads`, metadata is persisted, and a success message is displayed.

### Implementation

- [ ] T015 Add `ContosoDashboard/ContosoDashboard/Pages/Documents.razor`
- [ ] T016 Add `ContosoDashboard/ContosoDashboard/Shared/DocumentUploadModal.razor`
- [ ] T017 Add upload form and client-side metadata validation in `ContosoDashboard/ContosoDashboard/Shared/DocumentUploadModal.razor`
- [ ] T018 Add upload handling, progress indicator, and UI feedback in `ContosoDashboard/ContosoDashboard/Pages/Documents.razor`
- [ ] T019 Implement `UploadDocumentAsync` end-to-end in `ContosoDashboard/ContosoDashboard/Services/DocumentService.cs`
- [ ] T020 Add unsupported file type and 25MB size rejection logic in `ContosoDashboard/ContosoDashboard/Services/DocumentService.cs`
- [ ] T021 Add upload audit logging in `ContosoDashboard/ContosoDashboard/Services/DocumentService.cs`
- [ ] T022 Add document upload tests in `ContosoDashboard/ContosoDashboard.Tests/DocumentServiceTests.cs`

---

## Phase 4: User Story 2 - Browse & Search Documents (Priority: P1)

**Goal**: Provide users with searchable, filterable document views for owned, project, and shared documents.

**Independent Test**: Upload multiple documents with different categories, tags, and projects; verify search/filter returns correct permitted documents within performance targets.

### Implementation

- [ ] T023 Add `DocumentQueryOptions` search/filter support in `ContosoDashboard/ContosoDashboard/Services/DocumentService.cs`
- [ ] T024 Add `ContosoDashboard/ContosoDashboard/Pages/ProjectDocuments.razor`
- [ ] T025 Add `ContosoDashboard/ContosoDashboard/Pages/SharedWithMe.razor`
- [ ] T026 Add search box, category filter, and project filter UI in `ContosoDashboard/ContosoDashboard/Pages/Documents.razor`
- [ ] T027 Implement `GetDocumentsAsync` with permission-filtered search, category/project filtering, and sort options in `ContosoDashboard/ContosoDashboard/Services/DocumentService.cs`
- [ ] T028 Add integration tests in `ContosoDashboard/ContosoDashboard.Tests/DocumentServiceSearchTests.cs` for browse and search behavior

---

## Phase 5: User Story 3 - Share & Manage Documents (Priority: P2)

**Goal**: Allow owners to share documents, edit metadata, replace files, and delete documents with audit logging.

**Independent Test**: Share a document with another user, verify notification and access; replace a file and confirm content update; delete and confirm removal from storage and metadata.

### Implementation

- [ ] T029 Add `ContosoDashboard/ContosoDashboard/Pages/DocumentDetails.razor`
- [ ] T030 Add document sharing UI to `ContosoDashboard/ContosoDashboard/Pages/DocumentDetails.razor`
- [ ] T031 Add metadata edit, replace file, and delete controls to `ContosoDashboard/ContosoDashboard/Pages/DocumentDetails.razor`
- [ ] T032 Implement `ShareDocumentAsync`, `UpdateDocumentMetadataAsync`, `ReplaceDocumentFileAsync`, and `DeleteDocumentAsync` in `ContosoDashboard/ContosoDashboard/Services/DocumentService.cs`
- [ ] T033 Add lifecycle tests in `ContosoDashboard/ContosoDashboard.Tests/DocumentServiceLifecycleTests.cs` for sharing, editing, replacement, and deletion

---

## Phase 6: Polish & Cross-Cutting Concerns

**Purpose**: Finish documentation, security review, and training-safe feature readiness.

- [ ] T034 Update `specs/001-document-upload-management/quickstart.md` with implementation verification steps
- [ ] T035 Update `README.md` or feature documentation with the local secure upload folder and document management overview
- [ ] T036 [P] Review `ContosoDashboard/ContosoDashboard/Services/DocumentService.cs` and `ContosoDashboard/ContosoDashboard/Pages/DocumentDetails.razor` for authorization and error handling
- [ ] T037 [P] Update `ContosoDashboard/ContosoDashboard/Pages/SharedWithMe.razor` to show shared document status and notifications

---

## Dependencies & Execution Order

### Phase Dependencies

- **Phase 1**: Setup tasks can start immediately
- **Phase 2**: Foundational tasks depend on Phase 1 completion
- **Phase 3+**: Each user story depends on Phase 2 completion
- **Final Phase**: Depends on all user story phases

### User Story Dependencies

- **User Story 1 (US1)**: Can start after foundational services and models exist
- **User Story 2 (US2)**: Can start after foundational services exist; it depends on the document query and permission model but not on US1-specific UI
- **User Story 3 (US3)**: Can start after foundational services exist; it relies on US1 upload and US2 permission/search components for a complete lifecycle experience

### Parallel Opportunities

- `[P]` tasks in Phase 1 and Phase 6 can run in parallel
- Phase 3, Phase 4, and Phase 5 can be staffed in parallel after Phase 2 completion
- Test project creation and core service tests in Phase 2 can be done while model files are reviewed

## Parallel Execution Examples

- Run `T002`, `T003`, `T004`, and `T005` in parallel because they create independent service abstractions
- While `T010` and `T011` are in progress, another developer can implement `T016` and `T017` UI scaffolding for upload
- After Phase 2, US1, US2, and US3 story work can proceed concurrently if capacity allows

## Implementation Strategy

### MVP First

1. Complete Phase 1: Setup
2. Complete Phase 2: Foundational
3. Complete Phase 3: User Story 1 upload flow
4. Validate upload success and storage behavior independently
5. Stop and demo if a working upload flow is available

### Incremental Delivery

1. Finish foundation
2. Deliver US1 as the MVP increment
3. Deliver US2 search/browse next
4. Deliver US3 share/manage last
5. Polish security, docs, and notifications after all stories are functional
