# Decision log

## D-001: Private bucket with presigned URLs
- status: active
- owner: storage#object-storage
- date: 2026-10-01
- basis: user
- decision: Files live in one private S3 bucket and are accessed only through presigned URLs.
- why: Keeps files private without proxying large transfers through the API servers.

## D-002: Upload limit of 10 MB
- status: superseded
- owner: file-uploads#validation
- date: 2026-10-01
- basis: default
- decision: A single upload may be at most 10 MB.
- why: Recommended default; the user skipped the question.
- superseded_by: D-003

## D-003: Upload limit of 25 MB
- status: active
- owner: file-uploads#validation
- date: 2026-10-05
- basis: user
- decision: A single upload may be at most 25 MB.
- why: Customers upload design files that exceed 10 MB.
- supersedes: D-002

## D-004: Deleted uploads kept 30 days
- status: active
- owner: storage#retention
- date: 2026-10-02
- basis: delegated
- decision: Deleted uploads are purged from the bucket 30 days after deletion.
- why: User said "you decide"; 30 days allows recovery from accidental deletes without keeping data indefinitely.
