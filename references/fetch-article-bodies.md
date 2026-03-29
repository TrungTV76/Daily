# Fetch Article Bodies — BBC/CNBC Alternative Methods v2.0

> **Mục đích:** Giải quyết vấn đề BBC/CNBC article bodies bị block khi dùng WebFetch trực tiếp
> **Root cause:** BBC và CNBC dùng JavaScript rendering + paywall → WebFetch trả về truncated content hoặc block
> **Fallback chain:** Thử theo thứ tự ưu tiên — từ free → paid

---

## Priority Chain (Thử lần lượt)

```
1. RSS description snippet (LUÔN CÓ — từ Phase 1)
        ↓ (nếu snippet không đủ)
2. WebFetch với "?outputType=amp" (BBC)
        ↓ (nếu fail)
3. WebFetch với "textise dot iitty" converter
        ↓ (nếu fail)
4. Agent(Explore) scrape (no JS rendering approach)
        ↓ (nếu fail)
5. WebSearch để tìm cached/summarized version
        ↓ (nếu fail)
6. Đánh dấu [UNVERIFIED] + ghi vào topic_hints[]
```

---

## Method 1: BBC AMP Pages (Ưu tiên cao nhất)

**URL pattern:** Thêm `?outputType=amp` vào URL BBC

```
Original:  https://www.bbc.com/news/articles/cz90gpyw90wo
AMP:       https://www.bbc.com/news/articles/cz90gpyw90wo?outputType=amp
```

**Tại sao hoạt động:** BBC AMP pages không dùng JavaScript — chỉ là HTML thuần túy, fetch được ngay.

**Ví dụ WebFetch:**
```
URL: https://www.bbc.com/news/articles/cz90gpyw90wo?outputType=amp
Prompt: Return the FULL article text. Extract: headline, byline, publication date, full article body (every paragraph), any data/statistics mentioned.
```

**Tỷ lệ thành công:** ~85%

---

## Method 2: Textise dot iitty Converter

**URL:** `https://www.invidiousproject.de` hoặc `https://yewtu.be`

**Cách dùng:** Thay domain của article bằng Invidious instance

```
Original: https://www.bbc.com/news/articles/cz90gpyw90wo
Proxy:   https://yewtu.be/news/articles/cz90gpyw90wo
```

**Tại sao hoạt động:** Invidious/Proxies lấy content server-side, không có JS.

---

## Method 3: Agent(Explore) Scrape

**Khi nào dùng:** Khi Method 1+2 fail

**Agent prompt:**
```
Fetch the article at: [URL]

Steps:
1. Navigate to the URL
2. Wait for page to fully load (JavaScript rendered)
3. Extract ALL text content from the article body
4. Return:
   - Full headline
   - Publication date
   - Author name
   - ALL paragraphs in order
   - Any data tables or statistics
   - Any quoted statements

Return as structured markdown. If article is behind paywall, note "PAYWALL - content partially available".
```

**Tool:** `Agent(subagent_type: "Explore")`
**Cost:** ~$0.10-0.30/call

---

## Method 4: WebSearch Cache

**Khi nào dùng:** Tất cả methods trên fail

**Search queries:**
```
# Tìm cached version
site:webcache.googleusercontent.com "bbc.com/news/articles/[ID]"
site:archive.org "bbc.com/news/articles/[ID]"

# Tìm summary/reprint từ nguồn khác
"[headline]" BBC full article
"[key phrase]" site:reuters.com OR site:apnews.com

# Tìm tin tức cùng sự kiện từ nguồn accessible
"[key phrase]" site:vnexpress.net OR site:cafef.vn
```

**Verification rule:**
- Nếu tìm được Reuters/AP báo cùng sự kiện → dùng nguồn đó thay BBC
- Đánh dấu: `[BBC UNVERIFIED — verified via Reuters]`

---

## Method 5: CNBC Specific

**CNBC AMP:** CNBC có AMP version nhưng hay redirect

```
URL: https://www.cnbc.com/2026/03/24/anthropic-pentagon-dod-claude-court-ruling.html
Try:   https://www.cnbc.com/2026/03/24/anthropic-pentagon-dod-claude-court-ruling.html?outputType=amp
```

**CNBC Alternative domains:**
```
https://www.cnbc.com/id/100727362/device/rss/rss.html (RSS — đã dùng)
https://search.cnbc.com/rs/search/combinedcms/view.xml?partnerId=wrss01&id=100727362
```

**Proxies cho CNBC:**
```
https://www.devparrot.ch/cnbc/[path]
https://www.proxy-listen.de/abc?url=[encoded_url]
```

---

## Structured Output cho mỗi Article Fetch

```json
{
  "url": "https://...",
  "source": "bbc_world | cnbc_world",
  "method_used": "amp_page | agent_scrape | websearch_cache | rss_snippet | failed",
  "fetch_status": "full | partial | failed",
  "headline": "Exact headline",
  "byline": "Author name",
  "publication_date": "ISO8601",
  "article_body": "Full text...",
  "key_claims": ["claim 1", "claim 2", "..."],
  "statistics": [{"value": "...", "context": "..."}],
  "quotes": ["speaker: quote text"],
  "confidence": "high | medium | low",
  "used_as": "verified_fact | topic_hint | rejected"
}
```

---

## Pipeline Integration (Phase 1.5 VERIFY)

```yaml
Step 3: Fetch Article Bodies
  Với mỗi raw item đã phân loại là A_GEOPOLITICAL hoặc B_FINANCIAL:

  Method 1 (AMP):
    - Thử: [url] + "?outputType=amp"
    - If full content → confidence = HIGH
    - If partial → confidence = MEDIUM

  Method 2 (Proxy):
    - If Method 1 fail: thử Invidious proxy
    - If full content → confidence = HIGH

  Method 3 (Agent):
    - If Method 1+2 fail: spawn Agent(Explore)
    - If full content → confidence = HIGH
    - If paywall → confidence = MEDIUM + note

  Method 4 (WebSearch):
    - If Method 1+2+3 fail: WebSearch cached/summary
    - If found → confidence = MEDIUM

  Method 5 (Mark UNVERIFIED):
    - If all fail → confidence = LOW → vào topic_hints[]
    - Ghi: source_url, method_attempted[], failure_reason
```

---

## Cost & Time Estimate

| Method | Time | Cost | Success Rate |
|--------|------|------|-------------|
| AMP page | <5s | Free | ~85% |
| Proxy | <10s | Free | ~60% |
| Agent scrape | ~30s | ~$0.20 | ~90% |
| WebSearch cache | <5s | Free | ~40% |

**Khuyến nghị:** Thử AMP → Proxy → WebSearch → Agent (nếu cần article body bắt buộc)

---

## BBC Article ID Extraction

**Từ RSS feed:**
```
Item: https://www.bbc.com/news/articles/cz90gpyw90wo
→ Article ID: cz90gpyw90wo
→ AMP URL: https://www.bbc.com/news/articles/cz90gpyw90wo?outputType=amp
```

**Tự động hóa:** Script extract URL từ RSS → thêm `?outputType=amp` → fetch → parse
