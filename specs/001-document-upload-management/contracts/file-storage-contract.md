# Contract: File Storage Interface

## Purpose
Define the storage contract for uploaded documents so the ContosoDashboard implementation remains cloud-migration-ready and training-safe.

## Interface

### `IFileStorageService`

```csharp
public interface IFileStorageService
{
    Task<string> UploadAsync(Stream fileStream, string relativePath, string contentType);
    Task DeleteAsync(string relativePath);
    Task<Stream> DownloadAsync(string relativePath);
    Task<bool> ExistsAsync(string relativePath);
}
```

## Semantics

- `relativePath` is a storage key or relative file path that does not include user-input directories.
- The implementation must sanitize and normalize the path before writing or reading.
- `UploadAsync` returns the normalized storage path/key saved in the document metadata.
- `DeleteAsync` removes the file from storage.
- `DownloadAsync` returns a readable stream for authorized downloads.
- `ExistsAsync` can be used for safe cleanup and validation.

## Training Implementation

### `LocalFileStorageService`

- Writes files to a local folder outside `wwwroot`, for example `App_Data/uploads/`.
- Uses a path pattern such as:
  - `uploads/{userId}/{projectId or personal}/{guid}.{ext}`
- Uses `FileStream` with `FileMode.CreateNew` to avoid overwriting existing storage paths.
- Ensures the upload folder exists before writing.
- Does not expose files directly via static file middleware.

## Migration Notes

- A production implementation may replace `LocalFileStorageService` with `AzureBlobStorageService`.
- The service contract remains unchanged.
- Only DI registration and configuration should differ between local training and cloud deployments.

## Error Handling

- `UploadAsync` should throw a well-defined exception or return a failure when storage is unavailable.
- Consumers should catch storage errors and rollback document metadata persistence.
- `DeleteAsync` should be idempotent: if the file is already absent, it should succeed silently or return `false`.

## Security Contract

- The storage implementation must never write user-controlled paths directly.
- The application must build the full storage key from validated elements such as `userId`, `projectId`, and a GUID.
- The file name stored in metadata must not be reused by user input.
