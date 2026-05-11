# Common Errors

This section describes common issues and troubleshooting steps for Cloud Connect.

## File inaccessible
- This message means that the document in question could not be accessed from external storage.
- Make sure that the external storage account is set up properly within Business Central.
- If the connection to the storage account is valid, it probably means that the file was somehow deleted in the external cloud provider. Confirm the document's existence within the cloud and recover it if deleted.

## Unable to Attach Documents
- Ensure the external storage provider is configured correctly.
- Verify that the user has the required permission set assigned.

## Storage Provider Connection Fails
- Check credentials and storage container information in the configuration.
- Check connection status of storage provider outside the Business Central environment.
- Test the connection using the Assisted Setup wizard.

## General Troubleshooting Steps
- Restart Business Central after installation or configuration changes.
- Review the Assisted Setup for incomplete steps.
- Contact Q-Team Solutions support if issues persist.
