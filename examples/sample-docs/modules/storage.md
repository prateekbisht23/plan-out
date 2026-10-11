---
module: storage
status: in-progress
summary: Where uploaded files are kept and how they are named and retained.
---
# Storage

## Overview
Everything about where file bytes live. Other modules ask this one for a place to put a file and never decide it themselves.

## Scope
**Owns:** bucket layout, object naming, retention.

**Does not own:** who may upload or what is accepted; see [file uploads](file-uploads.md#validation).

## Object storage
Files are kept in a single private S3 bucket and are never served directly; clients receive time-limited presigned URLs [D-001].

Objects are named `<workspace-id>/<upload-id>`, so one workspace can never read another's files by guessing a key.

## Retention
Deleted uploads are removed from the bucket after 30 days [D-004].

## Glossary
- **Presigned URL**: a temporary link, signed by the server, that lets a client upload or download one object.

## Open questions
None.

## Related modules
- [file-uploads](file-uploads.md#upload-flow): the flow that writes into this bucket
