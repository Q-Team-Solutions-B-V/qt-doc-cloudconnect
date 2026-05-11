# Integration Overview

Cloud Connect is designed with extensibility in mind. The app provides an interface for integrating with additional external storage providers beyond Azure Blob Storage.

## Extensible Provider Interface

Developers can implement custom providers to support other cloud storage solutions, such as OneDrive. The interface ensures that new providers can be added without changing the core app functionality.

## API and Events

Cloud Connect exposes AL events and integration points for advanced scenarios, such as custom workflows or automated document processing.

## Authentication

Authentication with external providers is managed through secure credential storage and configuration in Business Central.
