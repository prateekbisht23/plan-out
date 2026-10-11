---
module: file-uploads
status: in-progress
summary: How users upload files from the dashboard and what is accepted.
---
# File uploads

## Overview
Users attach files to a workspace from the dashboard. This module owns the user-facing flow and the rules for what is accepted.

## Scope
**Owns:** the upload flow and validation rules.

**Does not own:** where bytes are stored or how long they are kept; see [storage](storage.md#object-storage).

## Upload flow
1. The dashboard asks the server for an upload slot.
2. The server validates the request (see [Validation](#validation)) and returns a presigned URL from [storage](storage.md#object-storage).
3. The browser uploads straight to that URL and then confirms completion to the server.

## Validation
A single file may be at most 25 MB [D-003]. Anything larger is rejected before a URL is issued.

## Open questions
None.

## Related modules
- [storage](storage.md#object-storage): provides the presigned URL
