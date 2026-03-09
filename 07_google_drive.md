# 7. Design a File Storage Service (Google Drive)

## Functional Requirements
- Upload, download, delete files
- Organize files in folders
- Share files and folders with others
- Sync across devices
- Search files by name

## Non-Functional Requirements
- Durability (files should never be lost — 99.999999999%)
- High availability
- Sync latency < 1 second for small files
- Support large files (up to 50 GB)

## File Upload Architecture
**Large file upload strategy:** Split into chunks
1. Client splits file into 4 MB chunks
2. Client uploads each chunk (parallel upload)
3. Client sends final "complete" message with all chunk IDs
4. Server assembles chunks in S3

```
Client → [Metadata Service] → DB (creates file record, returns pre-signed S3 URLs)
Client → [S3 directly] (uploads chunks via pre-signed URLs)
Client → [Metadata Service] (marks upload complete)
```

**Benefits:**
- Resume failed uploads (only re-upload failed chunks)
- Parallel uploads → faster for large files
- No file bytes pass through your app servers

## Delta Sync (Like Dropbox)
Only sync the parts of a file that changed:
1. Client chunks file, computes SHA hash for each chunk
2. Compare with server's known chunk hashes
3. Upload only changed/new chunks
4. Server reconstructs file from chunks (some unchanged)

## Metadata Storage
```sql
files:
  file_id       UUID PRIMARY KEY
  owner_id      BIGINT
  name          VARCHAR(255)
  size          BIGINT
  checksum      VARCHAR(64)
  s3_path       TEXT
  parent_folder UUID (nullable)
  created_at    TIMESTAMP
  updated_at    TIMESTAMP
  
file_shares:
  share_id      UUID
  file_id       UUID
  shared_with   BIGINT (user_id)
  permission    ENUM (read, write)
```

## S3 Storage
- Files stored in S3 with **versioning** enabled
- **Replication** across multiple AZs (S3 does this automatically)
- 11 nines durability (99.999999999%)

## Sync Service
- Client maintains local state of all file metadata
- Server sends change events via WebSocket or long polling
- Client applies changes locally

---
