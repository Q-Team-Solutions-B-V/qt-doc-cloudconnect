# Common Errors

This section describes common issues and troubleshooting steps for Cloud Connect.

---

## File Inaccessible

The **file inaccessible** status means that Cloud Connect has a metadata record showing the file should be in external storage, but the file cannot be retrieved from that location.
**Automatic fallback:** If the file is stored in more than one provider (for example Azure and Business Central), Cloud Connect automatically tries the other provider before marking the file as inaccessible. If the fallback download succeeds and a Business Central copy exists, you will be prompted to re-upload the file to the failed provider to restore redundancy.

### Common causes

| Cause | Details |
|---|---|
| File deleted outside Business Central | The file was manually deleted from the Azure container directly, without going through Cloud Connect. |
| Container or account renamed/deleted | The Azure storage account or container configured in Cloud Connect no longer exists or its name was changed. |
| SAS token expired | The Shared Access Signature token used by Business Central has expired and access to the container is denied. |
| Permissions changed | The SAS token no longer has the required Read permission for the file or container. |
| File moved to a different path | The file was renamed or moved in Azure outside of Business Central. |

### Troubleshooting steps

1. **Force-download from a specific provider** — Open the attachment details panel. The actions **Download from BC** and **Download from Azure** are available regardless of where the file is currently marked as stored. Use both of these to verify whether a copy exists in any of these locations without relying on the routing metadata. See [Force-Downloading from a Specific Provider](#force-downloading-from-a-specific-provider).
2. **Check the connection** — Open the Cloud Connect Setup page and verify the Azure account name, container name, and SAS token are correct and the token has not expired.
3. **Check the file in Azure** — Log into the Azure Portal and navigate to the configured container. Search for the file using the path shown in the attachment's **Location** field (visible in the attachment details panel).
4. **Quick availability check** — Click the *file inaccessible* text in the Document Type column of the attachment list. Cloud Connect will silently test whether the file is reachable. If it succeeds, the status is cleared automatically.
5. **Restore from Azure** — If you can locate the file in Azure but the check still fails, verify the SAS token permissions include **Read** and **List**.
6. **Accept the loss** — If the file has been permanently deleted and cannot be recovered, the metadata record can be left as-is or cleaned up. The attachment placeholder in Business Central can be deleted manually if it is no longer needed.

---

## Force-Downloading from a Specific Provider

The attachment details panel (opened by selecting an attachment in the list) exposes two actions for diagnostic purposes:

| Action | What it does |
|---|---|
| **Download from BC** | Downloads the attachment directly from Business Central’s internal storage, regardless of where the routing metadata says the file is stored. |
| **Download from Azure** | Downloads the attachment directly from Azure Blob Storage, regardless of routing metadata. |

These actions bypass all routing logic and metadata. They are useful when:

- You suspect the routing metadata is out of sync and want to verify whether a copy exists in a specific location.
- A file is marked as inaccessible in one provider but you believe a copy may still exist there.
- You want to confirm that a file was correctly uploaded to or removed from a specific provider after a routing change.

If **Download from BC** succeeds but returns the placeholder text ("This file has been moved to..."), the real file content is no longer in Business Central — only the external copy remains. If **Download from Azure** fails with an error, the file is not accessible in Azure at the stored path.

---

## Needs Review

This is not an error but a status indicating that the document type's routing configuration changed after the file was uploaded. The file may not be in the correct storage location.

**Resolution:** Open the **Document Types** page, select the relevant document type, and use the **Apply Routing** action. Cloud Connect will reconcile the file's current location with the current routing rules.

See also: [Usage Overview — Needs Review](../usage/usage-overview.md#needs-review).

---

## Unable to Attach Documents

- Ensure the external storage provider is configured correctly in the Cloud Connect Setup page.
- Verify that the user has the required permission set assigned (`QTEAM Cloud Connect`).
- If you see an error about metadata creation failing, the SAS token may not have **Create** permission on the container.

---

## Storage Provider Connection Fails

- Check the Azure Storage Account Name, Container Name, and SAS Token in the Cloud Connect Setup page.
- Verify the SAS token has not expired and includes at minimum: **Read**, **Create**, **Delete**, and **List** permissions.
- Test connectivity by opening the Assisted Setup wizard and stepping through the connection test.
- Check the storage account and container exist in the Azure Portal.

---

## Upload Failed / File Restored to Business Central

If an upload fails, Cloud Connect automatically rolls back the change: the original file content is restored in Business Central and no partial external record is left behind. The attachment remains accessible inside Business Central.

**If uploads consistently fail:**
- Verify SAS token permissions (**Create** permission required).
- Check whether the Azure container exists and is accessible.
- Ensure the file is not empty and has content before attaching.

---

## General Troubleshooting Steps

- After changing credentials or connection settings, re-test via the Assisted Setup wizard.
- If a user reports issues, verify they have the correct permission set assigned.
- Contact Q-Team Solutions support if issues persist after following the steps above.

