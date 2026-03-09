# 8. Design a Web Crawler

## Functional Requirements
- Fetch URLs from the web
- Parse HTML to extract links
- Store crawled content
- Prioritize and schedule crawls

## Non-Functional Requirements
- Scalable to billions of pages
- Respectful (obey robots.txt)
- Avoid duplicate crawling
- Distributed architecture

## Core Components
```
[URL Frontier (Queue)] → [Fetcher Workers] → [HTML Parser]
         ↑                                          ↓
[DNS Resolver]                          [URL Extractor → filter → URL Frontier]
                                                    ↓
                                          [Content Storage (S3)]
                                          [Duplicate Detector (Bloom Filter)]
```

## URL Frontier
- Priority queue of URLs to crawl
- **Priority:** importance (PageRank-like), freshness (news sites recrawled more)
- **Politeness:** Don't hammer the same domain — add delay between crawls of same host

## Duplicate Detection
1. **URL-level:** Bloom filter to check if URL was already crawled (memory efficient)
2. **Content-level:** Hash page content (SimHash/MinHash) to detect near-duplicate pages

## robots.txt
- Download and parse `robots.txt` for each domain before crawling
- Cache robots.txt per domain
- Respect `Crawl-delay` and `Disallow` directives

## Storage
- Raw HTML pages → S3
- Parsed metadata (title, links, last crawled) → Cassandra or Elasticsearch
- URL queue state → Redis

## Scaling
- Run many fetcher worker instances
- Partition the URL frontier by domain hash → each worker owns domains
- Ensures politeness (only one worker per domain)

---
