---
name: verosight-monitor
description: "Integrate Verosight API for social media intelligence and cyber monitoring. Sentiment analysis, trend detection, influencer identification, and bot detection across X (Twitter), Instagram, TikTok, YouTube, Threads, and news portals. Use when: user asks for sentiment analysis, social media monitoring, trend analysis, influencer tracking, bot detection, or mentions 'Verosight', 'social listening', 'cyber monitoring', 'digital reputation'. Covers auth setup, querying posts, analytics (sentiment/trending/volume), profile search, and report generation."
---

# Verosight Monitor — Social Media Intelligence

Integrate Verosight API for real-time social media monitoring and digital reputation management.

## Quick Setup

1. Get API key from https://verosight.com/dashboard/keys
2. Store key securely (env var or config file, never in chat)
3. Run auth flow: exchange API key → JWT token (24h expiry)
4. Query endpoints with JWT Bearer token

## Auth Flow

API key starts with `vlt_live_` (production) or `vlt_test_` (test).

```bash
# Step 1: Get JWT token
JWT=$(curl -s -X POST "https://api.verosight.com/v1/auth/token" \
  -H "X-API-Key: YOUR_API_KEY" | python3 -c "import sys,json; print(json.load(sys.stdin)['token'])")

# Step 2: Use JWT for queries
curl -s "https://api.verosight.com/v1/posts?query=KEYWORD&sources=x,instagram&limit=10" \
  -H "Authorization: Bearer $JWT"
```

JWT expires after 24 hours. Re-authenticate if you get 401.

## Endpoints

### Posts
```
GET /v1/posts?query=KEYWORD&sources=x,instagram,tiktok&limit=10&days=7&sentiment=negative
```

### Analytics
```
GET /v1/analytics/sentiment?query=KEYWORD&sources=x,instagram&days=7
GET /v1/analytics/volume?query=KEYWORD&days=7
GET /v1/analytics/trending?query=KEYWORD&limit=10
```

### Profiles
```
GET /v1/profiles?query=ACCOUNT&source=instagram
GET /v1/profiles/ACCOUNT/stats?source=instagram
```

### Account
```
GET /v1/account/balance    (check credits, no cost)
GET /v1/account/usage      (usage history)
```

### Search
```
POST /v1/search
Body: {"query": "KEYWORD", "sources": ["x", "instagram"], "days": 7}
```

## Common Workflows

### Sentiment Analysis Report
See [references/sentiment-workflow.md](references/sentiment-workflow.md) for full report template.

1. Authenticate → get JWT
2. Query sentiment endpoint → get positive/negative/neutral breakdown
3. Query posts with `sentiment=negative` → identify vocal accounts
4. Query volume → trend over time
5. Compile report: overview → trend → narratives → insights → recommendations

### Influencer Identification
1. Query posts with high engagement (likes, shares, views)
2. Sort by engagement metrics
3. Cross-reference with sentiment (positive vs negative influencers)
4. Profile top accounts for follower count and verification status

### Bot Detection
1. Query posts about target topic
2. Look for patterns: multiple posts from same account, identical content, coordinated timing
3. Check account age vs activity level (if available)
4. Flag suspicious accounts for manual review

## Response Format

```json
{
  "data": { ... },
  "meta": {
    "request_id": "uuid",
    "credits_used": 2,
    "credits_remaining": 998
  },
  "pagination": {
    "next_cursor": "base64...",
    "has_more": true,
    "total": 38113
  }
}
```

## Platform Coverage

X (Twitter), Instagram, TikTok, YouTube, Threads, Facebook, LinkedIn, News Portals

## Notes

- News portal results dominate volume (~70%), social platforms lower
- Semantic search returns broadly relevant results — source/date filters are limited
- Instagram direct scraping often blocked — use Verosight for Instagram data
- Each successful call (2xx) costs credits; 4xx/5xx do not deduct credits
- Rate limit: 60 req/min (standard tier)

## PDF Report Generation

See [references/pdf-template.md](references/pdf-template.md) for generating professional PDF reports with tables, charts, and formatted layouts using pdfkit (Node.js).
