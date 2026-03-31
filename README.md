# 🔍 Verosight Monitor

**Social Media Intelligence Skill for OpenClaw Agents**

Integrate [Verosight API](https://verosight.com) for real-time social media monitoring, sentiment analysis, trend detection, influencer identification, and bot detection.

## Features

- 📊 **Sentiment Analysis** — Positive/negative/neutral breakdown across platforms
- 📈 **Trend Detection** — Volume tracking over time with visual charts
- 👤 **Influencer Identification** — Find the most vocal accounts
- 🤖 **Bot Detection** — Identify coordinated posting patterns
- 📄 **PDF Reports** — Professional reports with tables and charts
- 🌐 **Multi-Platform** — X (Twitter), Instagram, TikTok, YouTube, Threads, News Portals

## Platforms Supported

X (Twitter) · Instagram · TikTok · YouTube · Threads · Facebook · LinkedIn · News Portals

## Quick Start

### 1. Get API Key
Sign up at [verosight.com](https://verosight.com) and generate an API key from [API Keys](https://verosight.com/dashboard/keys).

### 2. Install Skill
```bash
# Via OpenClaw
clawhub install verosight-monitor

# Or manually
git clone https://github.com/slameticon/verosight-monitor.git
cp -r verosight-monitor ~/.openclaw/workspace/skills/
```

### 3. Authenticate
```bash
# Get JWT token (valid 24h)
JWT=$(curl -s -X POST "https://api.verosight.com/v1/auth/token" \
  -H "X-API-Key: vlt_live_YOUR_KEY" | jq -r '.token')
```

### 4. Query
```bash
# Sentiment analysis
curl -s "https://api.verosight.com/v1/analytics/sentiment?query=KEYWORD&sources=x,instagram&days=7" \
  -H "Authorization: Bearer $JWT"

# Search posts
curl -s "https://api.verosight.com/v1/posts?query=KEYWORD&sources=x,instagram&limit=10" \
  -H "Authorization: Bearer $JWT"
```

## Endpoints

| Endpoint | Description |
|----------|-------------|
| `GET /v1/posts` | Search posts with filters |
| `GET /v1/analytics/sentiment` | Sentiment analysis |
| `GET /v1/analytics/volume` | Post/comment volume over time |
| `GET /v1/analytics/trending` | Trending posts and profiles |
| `GET /v1/profiles` | Browse social media profiles |
| `GET /v1/account/balance` | Check credit balance |

## Skill Structure

```
verosight-monitor/
├── SKILL.md                              # Main skill instructions
├── references/
│   ├── sentiment-workflow.md             # Sentiment analysis workflow
│   └── pdf-template.md                   # PDF report generation guide
└── scripts/
    ├── verosight-auth.sh                 # Auth helper
    └── quick-sentiment.sh                # Quick sentiment check
```

## Use Cases

- **Digital Reputation Management** — Monitor brand mentions and sentiment
- **Crisis Detection** — Early warning for negative viral content
- **Competitor Analysis** — Track competitor mentions and sentiment
- **Influencer Marketing** — Identify key voices in your industry
- **Political Monitoring** — Track public opinion on policies
- **Bot Detection** — Identify coordinated inauthentic behavior

## Credits

Each API call costs credits. Free accounts start with 1,000 credits. Check balance with:
```bash
curl -s "https://api.verosight.com/v1/account/balance" \
  -H "Authorization: Bearer $JWT"
```

## License

MIT

## Links

- [Verosight API](https://verosight.com)
- [Verosight Dashboard](https://verosight.com/dashboard)
- [OpenClaw](https://github.com/openclaw/openclaw)
- [ClawHub](https://clawhub.com)
