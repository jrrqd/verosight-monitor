# Sentiment Analysis Workflow

## Step-by-Step

### 1. Authenticate
```bash
JWT=$(curl -s -X POST "https://api.verosight.com/v1/auth/token" \
  -H "X-API-Key: $VEROSIGHT_API_KEY" | python3 -c "import sys,json; print(json.load(sys.stdin)['token'])")
```

### 2. Get Sentiment Data
```bash
curl -s "https://api.verosight.com/v1/analytics/sentiment?query=KEYWORD&sources=x,instagram,tiktok&days=7" \
  -H "Authorization: Bearer $JWT"
```

Response includes:
- `positive`, `negative`, `neutral` counts and percentages
- `weighted_positive_pct`, `weighted_negative_pct`, `weighted_neutral_pct`
- `sample_positive`, `sample_negative` — sample posts with engagement data
- `sample_independent_negative` — social media comments with negative sentiment

### 3. Get Volume Trend
```bash
curl -s "https://api.verosight.com/v1/analytics/volume?query=KEYWORD&days=7" \
  -H "Authorization: Bearer $JWT"
```

### 4. Get High-Engagement Negative Posts
```bash
curl -s "https://api.verosight.com/v1/posts?query=KEYWORD&sources=x,threads&sentiment=negative&limit=15&days=7" \
  -H "Authorization: Bearer $JWT"
```

### 5. Compile Report

Report structure:
1. **Executive Summary** — status (CRITICAL/HIGH/MEDIUM/LOW), key metrics
2. **Sentiment Overview** — table with positive/negative/neutral breakdown
3. **Volume Trend** — bar chart or timeline
4. **Top Narratives** — what people are saying (positive and negative)
5. **Vocal Accounts** — who's driving the conversation
6. **Platform Breakdown** — where the conversation lives
7. **Insights** — key findings
8. **Predictions** — what could happen next
9. **Recommendations** — immediate, short-term, medium-term actions

## Report Template

```
# 🔍 SDV REPORT
## Keyword: "[KEYWORD]"
**Periode:** [START] – [END] | **Sources:** [PLATFORMS]

---

## 📊 SENTIMENT OVERVIEW
| Sentimen | Jumlah | % | Weighted |
|----------|--------|---|----------|
| Positif  | X      | X%| X%       |
| Negatif  | X      | X%| X%       |
| Netral   | X      | X%| X%       |

Status: [LEVEL]

## 📈 VOLUME TREND
[Bar chart or daily breakdown]

## 🔴 TOP NEGATIVE NARRATIVES
1. [Narrative] — @account (Platform, X likes)
2. ...

## 🟢 TOP POSITIVE NARRATIVES
1. [Narrative] — @account (Platform, X likes)
2. ...

## 💡 INSIGHTS
1. ...

## ⚠️ PREDICTIONS
1. ...

## 📋 RECOMMENDATIONS
### Immediate (0-24h)
### Short-term (1-7 days)
### Medium-term (1-4 weeks)
```
