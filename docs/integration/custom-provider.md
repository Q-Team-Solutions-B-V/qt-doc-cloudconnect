# Implementing a Custom Cloud Provider

Cloud Connect is designed to be extensible. You can add support for additional cloud storage providers by implementing the `QTEAM IExternal CloudProvider` interface and registering your provider in the relevant places throughout the app.

This page describes everything that needs to be done to implement a new provider from start to finish.

---

## Overview of the Provider System

When a document attachment is inserted in Business Central, the `QTEAM Document Upload Manager` codeunit intercepts the event and asks the `QTEAM Cloud Storage Helper` codeunit to resolve which provider to use. The helper returns an implementation of the `QTEAM IExternal CloudProvider` interface. The upload manager then calls `UploadAttachment` on that provider.

Every provider is responsible for:
- Uploading a file to its storage backend
- Downloading a file back from its storage backend by record ID
- Deleting a file from its storage backend by record ID

---

## Step 1 — Implement the Interface

Create a new codeunit that implements `QTEAM IExternal CloudProvider`. The interface requires exactly three procedures:

### `GetName(): Text`

Return a human-readable name for the provider. This name is used for display purposes, including in the "Location" column visible in document attachment lists.

### `UploadAttachment(DocAttachment: Record "Document Attachment")`

This procedure is called when a new attachment is inserted. It must:

1. Verify the attachment has content.
2. Ensure the filename is unique among existing attachments for the same record, using `QTEAM Cloud Storage Helper`.`EnsureUniqueFileName`.
3. Strip the file extension from the filename using `QTEAM Cloud Storage Helper`.`RemoveExtension`.
4. Resolve the file scenario using `QTEAM Cloud Storage Helper`.`ResolveFileScenario`.
5. Build a file path using `QTEAM Cloud Storage Helper`.`BuildFilePath`. This produces a structured path in the format `tablefolder/recordno/fileid_filename.ext`.
6. Upload the binary content of the attachment to your storage backend.
7. Insert a metadata record in `QTEAM Document Storage Details` using `QTEAM Cloud Storage Helper`.`TryInsertMetadata`, passing the file path, file scenario, and the value from the `QTEAM Storage Provider` enum that corresponds to your provider.
8. Clear the binary content stored in the Business Central attachment record using `QTEAM Cloud Storage Helper`.`TryClearAttachment`. This replaces the file with a small placeholder text file. If this step fails, delete the uploaded file from your backend and remove the metadata record.
9. Mark the sync status as uploaded using `QTEAM Cloud Storage Helper`.`UpdateSyncStatus`.

### `DownloadFileByRecordId(FileRecordID: RecordId)`

This procedure is called when a user opens or downloads an attachment. It must:

1. Look up the metadata record in `QTEAM Document Storage Details` by `File Record ID`.
2. Check whether the file was previously marked as lost, so you can mark it as recovered if it turns out to be accessible again.
3. Retrieve an `InStream` from your storage backend using the stored file URL and file scenario.
4. If the file cannot be retrieved, call `QTEAM Cloud Storage Helper`.`MarkFileAsLost` and raise an error.
5. If the file was previously lost but is now accessible, call `QTEAM Cloud Storage Helper`.`MarkFileAsRecovered`.
6. Reconstruct the filename from the `Document Attachment` record.
7. Trigger a browser download using `DownloadFromStream`.

### `DeleteFileByRecordId(FileRecordID: RecordId)`

This procedure is called before a document attachment is deleted. It must:

1. Look up the metadata in `QTEAM Document Storage Details`.
2. Delete the file from your storage backend.
3. Delete the metadata record.

---

## Step 2 — Register the Provider in the Storage Provider Enum

The `QTEAM Storage Provider` enum is marked as `Extensible = true`. Add a new value to this enum in your extension to represent your provider. The enum value is stored in the `Cloud Provider` field of every `QTEAM Document Storage Details` record, so each file's provider is traceable.

---

## Step 3 — Register a File Scenario

Cloud Connect uses Business Central's `File Scenario` enum to associate file operations with a configured file account. The `QTEAM File Scenarios` enumextension already adds five placeholder scenarios to the `File Scenario` enum.

If your provider relies on Business Central's `External File Storage` system (like the Azure Blob provider does), you need to map your provider to a file scenario. The file scenario determines which configured external file account is used when calling `ExternalFileStorage.Initialize(FileScenario)`.

If your provider communicates directly with its backend using its own SDK or API, and does not go through `External File Storage`, you do not need a file scenario.

---

## Step 4 — Register the Provider in `GetProviderFromStorage`

The `QTEAM Cloud Storage Helper`.`GetProviderFromStorage` procedure is used when restoring files, and in any context where a provider must be resolved from an existing `QTEAM Document Storage Details` record. Add a case branch for your new enum value that returns an instance of your codeunit.

---

## Step 5 — Register the Provider in `ResolveStorageProvider`

The `QTEAM Cloud Storage Helper`.`ResolveStorageProvider` procedure determines which provider to use when a new attachment is inserted. It currently routes based on the `Table ID` of the attachment (the type of record the file is attached to). Add your routing logic here.

This is where storage routing is implemented — for example, routing attachments on customer records to one provider and attachments on vendor records to another.

---

## Step 6 — Add Permissions

Add your new codeunit to the `QTEAM Cloud Connect` permission set so that users with the standard permission set can execute your provider code.

---

## Summary of Required Changes

| What | Where |
|------|-------|
| Provider implementation | New codeunit implementing `QTEAM IExternal CloudProvider` |
| Provider identity | New value in `QTEAM Storage Provider` enum (extensible) |
| File scenario (if using External File Storage) | New value in `QTEAM File Scenarios` enumextension |
| Restore and download routing | `GetProviderFromStorage` in `QTEAM Cloud Storage Helper` |
| Upload routing | `ResolveStorageProvider` in `QTEAM Cloud Storage Helper` |
| Permissions | `QTEAM Cloud Connect` permission set |
