# Usage Overview

Cloud Connect works seamlessly with standard document attachment workflows in Microsoft Dynamics 365 Business Central. Users can attach, view, and manage documents as usual, while the app handles external storage in the background.

---

Attaching Documents
---

Attach files to business records using the standard Business Central interface — the same way you would without the app installed. When you attach a file, Cloud Connect automatically uploads it to the configured external cloud provider. No extra steps are required.

After a successful upload, the attachment is visible in the document list with the location column showing the name of the provider (e.g., "Azure"). The original file is no longer stored inside Business Central.

---

Downloading Attachments
---

Click on any attachment in the document list to download it. If the file is stored externally, Cloud Connect retrieves it from the external provider and starts the download automatically. The process is identical to downloading an internally stored file.

If a file cannot be retrieved (for example, because it was deleted outside of Business Central), the attachment will be marked as **Lost** and an error message will appear. Contact your administrator if this happens.

---

The Location Column
---

The attachment list includes a **Location** column that shows where each file is stored:

| Value | Meaning |
|-|-|
| *Azure* | File is stored in Azure Blob Storage. |
| *Default* | File is stored in Business Central's internal storage. |
| *lost* | File was stored externally but is no longer accessible. |
| *pending* | Upload has started but has not yet completed. |
| *failed* | Upload failed. The original file has been restored in Business Central. |

---

Restoring All Files to Business Central
---

If you need to uninstall Cloud Connect or migrate away from external storage, you can restore all externally stored files back into Business Central using the restore function.

1. Open the **Cloud Connect Setup** page in Business Central.
2. Use the **Restore All Documents** action.
3. The app will download each externally stored file and place it back into Business Central's internal storage.

After the restore is complete, all files are stored internally again and the app can be safely uninstalled without loss of data.

---

Assisted Setup
---

When setting up Cloud Connect for the first time, use the **Assisted Setup** wizard to configure the connection to your external storage provider. The wizard guides you through:

1. Selecting a storage provider (currently Azure Blob Storage).
2. Entering the required credentials: container name and SAS token.
3. Specifying the required permissions for the SAS token.
4. Testing the connection.

To open the wizard, search for **Set Up External File Accounts** in Business Central, or navigate to the Cloud Connect Assisted Setup directly from the setup page.
