# X/Twitter Agent Scraper — Tier 5 v2.0

> **Mục đích:** Fetch real-time market intelligence từ X/Twitter bằng Agent subagent thay vì direct scraping (cần JS rendering)
> **Thay thế cho:** Phương án cũ (WebSearch, không hiệu quả cho X)
> **Phương pháp:** Agent(Explore) subagent với prompt chuyên biệt cho từng account

---

## Agent Configuration

### Tool Mapping
```yaml
Tool: Agent(subagent_type: "Explore")
Prompt: Fetch latest tweets from X.com/@username
Max output: 3-5 tweets mới nhất
Cost: ~0.10-0.30/agent call
```

### Account Priority Matrix

| Account | Expertise | Priority | Tác động market |
|---------|-----------|---------|---------------|
| @DeItaone | Real-time headlines | 🔴 CAO | Trực tiếp |
| @zerohedge | Breaking news, contrarian | 🔴 CAO | Gián tiếp |
| @business | Bloomberg breaking | 🔴 CAO | Trực tiếp |
| @FT | Global finance | 🟡 TRUNG BÌNH | Gián tiếp |
| @SpencerHakimian | Macro trading, FX | 🟡 TRUNG BÌNH | Gián tiếp |
| @realDonaldTrump | US policy, tariffs | 🔴 CAO | Trực tiếp |
| @elonmusk | Tech, crypto, sentiment | 🟡 TRUNG BÌNH | Gián tiếp |
| @Osint613 | Geopolitics, Middle East | 🔴 CAO | Trực tiếp |
| @real_stevele | Chứng khoán Mỹ, macro | 🟡 TRUNG BÌNH | Gián tiếp |
| @financialjuice | Real-time economic data | 🟢 THẤP | Gián tiếp |

---

## Agent Prompt Template

```markdown
Bạn là trợ lý research cho báo cáo Daily Macro Briefing.

NHIỆM VỤ: Fetch tweets mới nhất từ X.com/@[username]

YÊU CẦU:
1. Truy cập https://x.com/@[username]
2. Lấy 5 tweets mới nhất (trong 24 giờ)
3. Với mỗi tweet, ghi:
   - Nội dung chính (exact text)
   - Thời gian đăng
   - Số likes/reposts (nếu có)
   - Tweet có media (ảnh/video) không?
4. Lọc: chỉ lấy tweets liên quan đến:
   - Thị trường chứng khoán, forex, crypto
   - Địa chính trị, xung đột
   - Chính sách thương mại, thuế quan
   - Tin nóng (breaking news)
5. Loại bỏ: memes, off-topic, quảng cáo

OUTPUT FORMAT (JSON):
{
  "account": "@[username]",
  "fetch_time": "ISO8601",
  "tweets": [
    {
      "text": "exact tweet content",
      "time": "relative time",
      "likes": N,
      "reposts": N,
      "media": true/false,
      "relevance": "high/medium/low",
      "market_impact": "bullish/bearish/neutral"
    }
  ],
  "summary": "1-2 câu tóm tắt themes chính"
}

Nếu không truy cập được X.com: trả về error và gợi ý fallback.
```

---

## Fallback khi Agent fail

Nếu Agent không truy cập được X.com:

### Fallback 1: WebSearch
```
Search: "@DeItaone latest tweets March 2026"
Search: "site:x.com/@username + market"
```

### Fallback 2: Nitter (open-source Twitter front-end, no JS)
```
https://nitter.net/@username
```
⚠️ Nitter bị rate-limit hoặc shutdown thường xuyên

### Fallback 3: syndication API
```
https:// syndication.twitter.com/srv/timeline-list/profile?screen_name=username
```

---

## Pipeline Integration

```yaml
# Trong Phase 1 (FETCH), sau khi đã fetch RSS feeds xong:

Step 5: X/Twitter Agent Scrape
  Tool: Agent(subagent_type: "Explore")

  # Chạy song song cho 5 accounts ưu tiên cao
  Parallel agents:
    - @DeItaone       (5 tweets)
    - @zerohedge      (5 tweets)
    - @realDonaldTrump (5 tweets)
    - @business       (3 tweets)
    - @Osint613       (3 tweets)

  # Sau đó cho 5 accounts ưu tiên trung bình
  Sequential (nếu còn token):
    - @real_stevele
    - @SpencerHakimian
    - @FT
    - @elonmusk
    - @financialjuice

  Cost estimate: 5 agents × ~$0.20 = ~$1.00/phiên

Output:
  x_twitter_raw.json:
  {
    "accounts": [...],
    "total_tweets_fetched": N,
    "high_relevance_count": N,
    "bullish_count": N,
    "bearish_count": N,
    "neutral_count": N,
    "key_themes": [...]
  }
```

---

## Confidence Rules (Tier 5 — X/Twitter)

| Tweet Type | Confidence | Ghi chú |
|-----------|-----------|---------|
| Breaking news / headline | MEDIUM | Verify với nguồn chính |
| Opinion / sentiment | LOW | Không dùng làm FACT, chỉ làm topic hint |
| Data claim (số cụ thể) | LOW | Bắt buộc cross-reference ≥1 nguồn khác |
| Policy announcement (Trump, Fed) | MEDIUM | Verify với CNBC/Federal Reserve |
| Contrarian view (@zerohedge) | LOW | Không dùng làm FACT độc lập |

---

## Alert Triggers

| Trigger | Action |
|---------|--------|
| >3 accounts cùng tweet về 1 sự kiện | Promote thành FACT (MEDIUM confidence) |
| Trump tweet đề cập thuế/tariff | Đánh dấu HIGH PRIORITY — verify ngay |
| @Osint613 tweet về xung đột | Đánh dấu GEOPOLITICAL flag |
| Contrarian view khác consensus | Ghi vào Contrarian View section |

---

## Cost Optimization

- **Max agents/phiên:** 5 (ưu tiên cao) + 3 (ưu tiên trung bình) = 8 agents
- **Max cost/phiên:** ~$2.00
- **Trigger:** Chỉ chạy Tier 5 khi Tier 1-4 không đủ tin hot
- **Skip nếu:** Token budget < 20% còn lại
- **Alternative:** Dùng WebSearch làm fallback không tốn thêm cost
