# Usage Overview

Cloud Connect works seamlessly with standard document attachment workflows in Microsoft Dynamics 365 Business Central. Users attach and access files in the usual way, while the app handles routing and external storage in the background.

---

## Attaching Documents

Attach files to business records the same way you would without the extension installed — by dragging and dropping or using the attach button in the document factbox.

When you attach a file, a dialog appears asking you to select a **document type** for the attachment. Document types determine which storage provider(s) the file is sent to. If you dismiss the dialog or no document types are configured, the file is sent to the configured **default provider**.

After a successful upload the attachment is visible in the list with a document type label in the **Document Type** column.

---

## Document Types

Document types are the core of Cloud Connect's routing system. Each document type is linked to one or more storage providers. When a file is uploaded and assigned a document type, it is automatically stored in all linked providers.

**Examples:**
- A document type *Contract* linked to Azure → all contracts are uploaded to Azure.
- A document type *Invoice* linked to both Azure and Business Central → invoices are stored in both locations simultaneously.

### Managing Document Types

Open the **Document Types** page from the Cloud Connect Setup page or by searching for it in Business Central.

| Action | Description |
|---|---|
| Create a document type | Enter a **Code** (short identifier, e.g. `INVOICE`) and a **Description**. A default provider record is automatically created. |
| Add a storage provider | In the **Storage Providers** factbox (on the right side of the page), use **Add Provider** to link an additional provider to the document type. |
| Remove a storage provider | Select a provider in the factbox and delete it. The last provider on a document type cannot be removed. |
| Apply Routing | Use the **Apply Routing** action to move all existing attachments of this document type to their correct provider(s). Files already in the correct location are left as is. |

### Changing an Attachment's Document Type

To change the document type of an existing attachment, click the **Document Type** column value in the attachment list. A selection menu appears. Selecting a new document type automatically moves the file to the correct provider(s) for that type.

---

## Default Provider

The **Default Provider** setting determines where files go when:
- No document type is selected during upload.
- No document types are configured.

Configure the default provider on the **Cloud Connect Setup** page or during the **Assisted Setup** wizard. If no default provider is configured, files are stored in Business Central's internal storage.

---

## The Document Type Column

The attachment list includes a **Document Type** column. It shows the document type of the attachment, or a status indicator for exceptional states.

| Display value | Meaning |
|---|---|
| *Invoice*, *Contract*, etc. | The attachment has been assigned this document type and is stored in the correct provider(s). |
| *Invoice \[Needs Review\]* | The routing rules for this document type changed after the file was uploaded. The file may not be in the correct location. See [Needs Review](#needs-review) below. |
| *file inaccessible* | The file cannot be found in expected storage location. See [File Inaccessible](#file-inaccessible) below. |

Click on any value in this column to change the document type or, if the file is inaccessible, to check whether it has become available again.

---

## The Location Field

The **Location** field in the attachment details panel shows which storage provider(s) currently hold the file. When a file is stored in multiple providers, all are listed (e.g., `Azure, Business Central`).

---

## Needs Review

The **Needs Review** status appears when the routing rules for a document type have changed since the file was last stored — for example, a new provider was added or one was removed.

**What it means:** The file is still accessible, but may not be in the correct location according to the current configuration.

**How to resolve it:**
1. Open the **Document Types** page.
2. Select the document type in question.
3. Use the **Apply Routing** action.

Cloud Connect will compare where each file is currently stored against where it should be, upload to any missing providers, and remove it from providers it no longer belongs to. Files already in the correct location are left as is.

Alternatively, click the **Document Type** value on the individual attachment to change its type or re-apply routing manually per attachment.

---

## File Inaccessible

The **file inaccessible** status means that Cloud Connect has a metadata record indicating the file should be in external storage, but the file cannot be retrieved from that location.

See [Common Errors — File Inaccessible](../troubleshooting/common-errors.md#file-inaccessible) for causes and troubleshooting steps.

**Quick check:** Click the *file inaccessible* text in the Document Type column. Cloud Connect will silently test whether the file is reachable. If it is, the status is automatically cleared and the file becomes available again.

---

## Downloading Attachments

Click any attachment name to download it. If the file is stored externally, Cloud Connect retrieves it automatically. The experience is identical to downloading a file stored inside Business Central.

If a file is stored in both Business Central and an external provider, Cloud Connect prioritises the Business Central copy to avoid unnecessary external calls.

**Automatic fallback when a provider is unavailable**

If an external provider cannot be reached during a download, Cloud Connect automatically attempts to download the file from any other provider that holds a copy. The failed provider is marked as inaccessible. If the download from the fallback succeeds and a Business Central copy of the file is available, you will be asked whether you want to re-upload the file to the original provider — restoring full redundancy without any manual steps.

**Force-downloading from a specific provider**

The attachment details panel includes **Download from BC** and **Download from Azure** actions. These bypass routing metadata and always attempt to fetch from the named provider directly — useful for diagnosing whether a copy actually exists at a specific location. See [Force-Downloading from a Specific Provider](../troubleshooting/common-errors.md#force-downloading-from-a-specific-provider) in the troubleshooting guide.

---

## Restoring All Files to Business Central

To restore all externally stored files back into Business Central — for example before uninstalling the extension:

1. Open the **Cloud Connect Setup** page.
2. Open the **Prepare for Uninstall** wizard from the Cloud Connect Setup page.
3. Cloud Connect downloads each file from its external provider, restores it into Business Central's internal storage, and updates the metadata accordingly.

Files marked as **inaccessible** are skipped during the restore. After the restore completes, a summary message indicates whether all files were restored or some were skipped.

---

## Assisted Setup

When setting up Cloud Connect for the first time, use the **Assisted Setup** wizard to configure your external storage provider and default routing. The wizard guides you through entering credentials, selecting a default provider, and verifying the connection.

Open the wizard from the Cloud Connect Setup page or by searching for **Cloud Connect** in Business Central's Assisted Setup list.

---

## Common Scenarios

### Merging two document types into one

If you have two document types whose files should all be managed under a single type, you can merge them using a rename-swap technique. No manual reassignment of individual attachments is required.

**Example:** You want all files currently classified as *WRONGTYPE* to become *RIGHTTYPE*.

1. On the **Document Types** page, delete *WRONGTYPE*.
   - The attachments that carry the *WRONGTYPE* code are not affected — they retain their code, now temporarily pointing to a deleted type.
2. Rename the code of *RIGHTTYPE* to `WRONGTYPE`.
   - All attachments that had *WRONGTYPE* now resolve to this record, which carries *RIGHTTYPE*'s storage providers.
3. Rename the code back to `RIGHTTYPE`.
   - All attachments — both the original *RIGHTTYPE* files and the migrated *WRONGTYPE* files — are now classified as *RIGHTTYPE*.
4. If *WRONGTYPE* and *RIGHTTYPE* originally had different storage providers, use **Apply Routing** to move the migrated files to the correct location.


