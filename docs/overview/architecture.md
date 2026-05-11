# Architecture

Cloud Connect integrates with Microsoft Dynamics 365 Business Central as an AL extension. It intercepts document attachment operations and redirects file storage to external cloud providers, such as Azure Blob Storage. The app uses an extensible provider interface, allowing for future support of additional storage solutions like OneDrive.

The architecture ensures that users interact with attachments in the same way as native Business Central functionality, while the app manages the storage and retrieval of files from the configured external provider. This approach optimizes database size and performance, and supports compliance with organizational storage policies.
