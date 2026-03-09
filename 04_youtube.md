# 4. Design a Video Streaming Service (YouTube)

## Functional Requirements
- Upload videos
- Stream videos on-demand
- Search for videos
- Like, comment, share

## Non-Functional Requirements
- Smooth streaming (low buffering)
- High availability
- Support for various network conditions and device types
- Global distribution

## Video Upload & Processing Pipeline
```
[User Uploads Video] → [Raw Storage S3]
                               ↓
                     [Message Queue (SQS/Kafka)]
                               ↓
                     [Transcoding Workers]
                       ↓       ↓      ↓
                    [360p]  [720p]  [1080p] → S3
                               ↓
                     [Update DB: video ready]
                     [Notify user: processing complete]
```

## Transcoding
- Convert uploaded video into multiple formats and resolutions
- **Adaptive Bitrate Streaming (ABR):** Client automatically switches quality based on bandwidth
  - Formats: **HLS** (Apple), **DASH** (industry standard)
  - Segments video into 2-10 second chunks
  - Manifest file lists all available quality levels and chunk URLs

## Video Delivery
```
[Client] → [CDN (CloudFront/Akamai)] → [S3 (Video segments)]
```
- Video chunks cached at CDN edge nodes
- Client fetches from nearest CDN node
- **< 50ms** first byte for most users globally

## Metadata Storage
```sql
videos:     video_id, user_id, title, description, status, duration, created_at
video_files: video_id, resolution, format, s3_url, size
tags:       tag_id, tag_name
video_tags: video_id, tag_id
```

## Search
- Index video metadata in **Elasticsearch**
- Full-text search on title, description, tags
- Autocomplete using Elasticsearch suggestions

## View Count — Distributed Counter Problem
Naive approach (UPDATE SET views = views + 1) won't scale.
- Use **Redis counter** (INCR command — atomic)
- Batch flush to DB every N seconds/minutes
- Approximate counts acceptable for display

---
