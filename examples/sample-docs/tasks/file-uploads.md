# File uploads tasks

- [x] T-001 Create the private bucket and presigned URL helper
  implements: storage#object-storage
  done when: helper returns a URL that accepts a PUT and rejects reads without a signature

- [ ] T-002 Add the upload-slot endpoint with size validation
  implements: file-uploads#upload-flow, file-uploads#validation
  done when: a 26 MB request is rejected with 413 and a 1 MB request returns a presigned URL

- [ ] T-003 Add the 30-day purge job
  implements: storage#retention
  done when: an upload deleted 31 days ago is gone from the bucket in a test run
