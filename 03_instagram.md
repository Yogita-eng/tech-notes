# 3. Design a Social Media Platform (Instagram)

## Functional Requirements
- Upload and share photos/videos
- Follow/unfollow users
- Like and comment on posts
- News feed showing posts from followed users

## Non-Functional Requirements
- High availability (99.9%)
- News feed loads in < 500ms
- Highly scalable (500M+ users)
- Eventual consistency acceptable for feeds

## News Feed Generation — The Core Problem

### Pull Model (Fan-out on Read)
- When user opens app → query all followed users' recent posts → merge and sort
- ✅ No write overhead
- ❌ Very slow for users following thousands of accounts (N database queries)

### Push Model (Fan-out on Write / Pre-computed Feed)
- When user posts → immediately write to every follower's feed cache (Redis list)
- ✅ Fast reads — just read your pre-computed feed list
- ❌ Massive write amplification for celebrities (Kylie Jenner has 400M followers!)

### Hybrid Model (Used by Instagram, Twitter)
- **Regular users** → Push model (pre-compute feeds)
- **Celebrities / accounts with millions of followers** → Pull model (compute at read time)
- Combine feeds at read time

## Feed Service Architecture
```
[Post Created] → Kafka
                    ↓
            [Feed Fanout Service]
                    ↓
        [Check: is user celebrity?]
         No ↓              Yes → skip
    [Write post ID to
     each follower's
     Redis feed list]
         ↓
[At read time: fetch post details from Posts DB for each ID in list]
```

## Database Schema
```sql
users:      user_id, username, profile_image_url
posts:      post_id, user_id, image_url, caption, created_at
follows:    follower_id, followee_id, created_at
likes:      post_id, user_id, created_at
comments:   comment_id, post_id, user_id, content, created_at
```

## Photo Storage
- Raw uploads → S3
- Async processing (Lambda/worker) → generate multiple resolutions (thumbnail, medium, full)
- Store all resolutions in S3
- CDN (CloudFront) in front of S3

## Database Choice
- **Users, Posts, Follows** → PostgreSQL (relational, ACID)
- **Feed cache** → Redis (pre-computed sorted lists)
- **Likes/Comments** → Cassandra (high write volume, time-ordered)
- **Search** → Elasticsearch (username/hashtag search)

---
