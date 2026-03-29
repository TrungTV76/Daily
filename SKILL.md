---
name: daily-macro-briefing
description: "Tạo báo cáo Daily Macro Briefing chuyên sâu — thu thập tin tức từ 42+ nguồn (VN Market, Chính sách/Luật, Địa chính trị, Công nghệ/AI, X/Twitter, Substack Experts), phân tích theo framework IEIA, viết theo phong cách Substack writer. Hỗ trợ 2 chế độ: gemini (nhanh/daily) và opus (sâu/premium). Use when: daily news, macro briefing, tin tức hàng ngày, báo cáo thị trường, market analysis."
argument-hint: "[mode: gemini|opus] [optional: date YYYY-MM-DD]"
disable-model-invocation: false
user-invocable: true
metadata:
  category: research
  version: 5.0
  author: TrungTV
  triggers: daily news, macro briefing, tin tức, báo cáo, market report, thị trường
  references: tone-guide, ieia-framework, quality-gate, output-template, fact-extraction-template
  v5_update: "Thêm Phase 1.5 VERIFY — chống hallucination và narrative contamination"
---

# Daily Macro Briefing v5.0

> **Pipeline:** FETCH → X/AGENT → BBC/BODY → VERIFY → CROSS-REF → NARRATIVE → INSIGHT → STRATEGY → QGATE v2.0 → CHANGELOG → PUSH → NOTIFY
> **⚠️ v6.0 NEW:** X/Twitter Agent scraper, BBC/CNBC body fetch, Cross-reference, Narrative contamination checker, Auto-changelog
> **Kế thừa:** OpenClaw v1.0–v9.0 Super Platinum + Antigravity Skills Ecosystem
>
> **⚠️ Claude Code CLI Adapter:** Skill này đã được điều chỉnh để chạy trên Claude Code CLI. Các tool mapping: `read_url_content` → `WebFetch`, `browser_subagent` → `Agent (Explore/GeneralPurpose)`, `search_web` → `WebSearch`. GitHub push path được ghi chú trong Phase 4.

---

## Khi nào sử dụng skill này

- Tạo báo cáo tin tức hàng ngày/hàng tuần
- Cần tổng hợp macro toàn cầu + thị trường VN
- Muốn phân tích FACT/VIEW theo phong cách chuyên gia
- Thu thập và lọc tin từ nhiều nguồn tự động

## Không sử dụng khi

- Chỉ cần tra cứu 1 tin tức cụ thể
- Cần phân tích dữ liệu doanh nghiệp chi tiết (→ dùng `agentic-analytics`)

---

## ⚙️ Mode Selection (QUAN TRỌNG)

Skill hỗ trợ 2 chế độ chạy. Xác định mode từ argument đầu tiên:

### 🟢 Mode GEMINI (Default — "Anti Gemini")
```
Chạy skill daily-macro-briefing gemini
Chạy skill daily-macro-briefing gemini 2026-03-24
```
- **Tốc độ:** Nhanh (~3-5 min)
- **Chi phí:** Thấp (~$0.50-0.70/bài)
- **Phân tích:** Tốt — IEIA đầy đủ, đủ dùng hàng ngày
- **Dùng khi:** Daily routine, cập nhật nhanh

### 🟣 Mode OPUS (Premium — "Anti Opus")
```
Chạy skill daily-macro-briefing opus
Chạy skill daily-macro-briefing opus 2026-03-24
```
- **Tốc độ:** Chậm hơn (~5-10 min)
- **Chi phí:** Cao hơn (~$2-3/bài)
- **Phân tích:** Sâu nhất — contrarian views, portfolio thinking, sector rotation
- **Dùng khi:** Phiên quan trọng, cuối tuần, báo cáo đặc biệt

### Khác biệt giữa 2 mode:

| Tiêu chí | GEMINI | OPUS |
|----------|--------|------|
| FACT/VIEW | Đầy đủ IEIA | IEIA + **Second-order effects** |
| Strategic Takeaway | 3 khuyến nghị | 3 khuyến nghị + **Scenario analysis** (bull/bear/base) |
| Narrative | Hook + Flow | Hook + **Deep narrative** + Contrarian challenge |
| X-RADAR | 8 chỉ số | 8 chỉ số + **Correlation matrix** (cross-asset signals) |
| Sector Heatmap | Biến động % | Biến động + **Rotation signals** (money flow) |
| Expert Views | Tóm tắt | Tóm tắt + **So sánh quan điểm** các chuyên gia |
| Cliff-hanger | Có | Có + **Actionable trigger levels** (giá cụ thể) |

---

## Core Architecture

```
FETCH (RSS/WebScrape — No LLM)
    ↓
X/TWITTER AGENT (Explore subagent — Tier 5)
    ↓
BBC/CNBC BODY FETCH (AMP → Proxy → Agent — Tier 2)
    ↓
VERIFY Phase 1.5 (fact_extraction.json + confidence scoring)
    ↓
CROSS-REFERENCE (financial facts ≥2 nguồn)
    ↓
NARRATIVE CHECK (contamination detection)
    ↓
INSIGHT (FACT/VIEW + IEIA)
    ↓
STRATEGY (Thesis + Scenario + Trigger Levels)
    ↓
QUALITY GATE v2.0 (Fact Verification mandatory)
    ↓
AUTO-CHANGELOG (update per version)
    ↓
PUSH (GitHub)
    ↓
NOTIFY
```

### Full Reference Map (v6.0)

| Phase | Reference File | Purpose |
|-------|-------------|---------|
| FETCH | `sources.json` | Source list, tiers, RSS URLs |
| FETCH | `references/x-twitter-agent.md` | Tier 5 X/Twitter Agent scraping |
| FETCH | `references/fetch-article-bodies.md` | BBC/CNBC body fetch methods (AMP + Proxy + Agent) |
| VERIFY | `references/fact-extraction-template.md` | Fact extraction + 6-type confidence schema |
| VERIFY | `references/cross-reference-rules.md` | Financial fact ≥2 source verification |
| VERIFY | `references/narrative-contamination-checker.md` | 5-type bias/contamination detection |
| INSIGHT | `references/ieia-framework.md` | IEIA framework |
| STRATEGY | `references/tone-guide.md` | Hook + narrative + Substack style |
| QUALITY GATE | `references/quality-gate.md` | v2.0 — Fact Verification bắt buộc |
| PUSH | `references/auto-changelog-generator.md` | Auto-update CHANGELOG per version |

### Mode-aware Processing

| Phase | GEMINI mode | OPUS mode |
|-------|------------|------------|
| FETCH | Thu thập 42+ nguồn | Thu thập 42+ nguồn (giống nhau) |
| INSIGHT | FACT/VIEW + IEIA cơ bản | FACT/VIEW + IEIA **+ second-order analysis** |
| STRATEGY | Hook + 3 Takeaways | Hook + 3 Takeaways **+ scenario analysis + contrarian view** |
| PUSH | Quality Gate 8 items | Quality Gate 8 items **+ depth check** |
| NOTIFY | Summary + token usage | Summary + token usage + **key trigger levels** |

---

## Configuration Files

| File | Mô tả |
|------|--------|
| `sources.json` | Nguồn tin, phân tier, RSS URLs, fetch method |
| `cache.json` | Cache incremental, dedup hashes, history |
| `references/tone-guide.md` | Hook formulas, narrative patterns, Substack style |
| `references/ieia-framework.md` | Insight→Evidence→Impact→Action framework |
| `references/quality-gate.md` | Checklist verification trước khi push (v2.0 — Fact Verification bắt buộc) |
| `references/output-template.md` | Markdown template báo cáo |
| `references/fact-extraction-template.md` | Verification schema + confidence rules chống hallucination |
| `references/x-twitter-agent.md` | Agent-based scraping cho Tier 5 X/Twitter (v2.0 mới) |
| `references/fetch-article-bodies.md` | Alternative methods fetch BBC/CNBC article bodies (v2.0 mới) |
| `references/cross-reference-rules.md` | Auto cross-reference financial facts ≥2 nguồn (v2.0 mới) |
| `references/narrative-contamination-checker.md` | Auto bias detection 5 loại contamination (v2.0 mới) |
| `references/auto-changelog-generator.md` | Auto update CHANGELOG mỗi phiên bản (v2.0 mới) |

---

## Execution Process

### Phase 1: FETCH (Data Layer — No LLM)

**Nguyên tắc:** Tách biệt thu thập dữ liệu, không tốn token LLM.

```yaml
Steps:
  1. Xác định ngày (user argument hoặc ngày hiện tại)
  2. Load sources.json → danh sách nguồn tin
  3. Load cache.json → hash đã xử lý (incremental update)
  4. Tạo thư mục output NewsReport/YYYY/MM/

⚠️ TOOL MAPPING (Claude Code CLI):
  - read_url_content  → WebFetch (fetch RSS/HTML content từ URL)
  - browser_subagent   → Agent tool với subagent_type: "Explore" hoặc "GeneralPurpose"
  - search_web         → WebSearch (Brave Search tích hợp)
  - Git push           → Bash với git commands

Fetch strategy (theo tier):
  Tier 1 - VN Market:
    - RSS feeds: CafeF, Vietstock, VnEconomy, VnExpress
    - Tool: WebFetch (RSS URL)
    - Lấy 3-5 tin mới nhất mỗi nguồn

  Tier 2 - Global Macro & Policy:
    - RSS: BBC World, CNBC
    - Web scrape: Bloomberg, Reuters
    - Tool: WebFetch
    - Lấy 5-10 tin nổi bật nhất

  Tier 3 - Tech/AI:
    - RSS: Tinh tế, GenK, VnExpress Số hóa
    - Web scrape: Hacker News, HuggingFace Papers, The Verge
    - Tool: WebFetch + Agent (Explore) cho trang cần JS

  Tier 4 - Substack Experts:
    - Web scrape: Noahpinion, One Useful Thing, Hồ Quốc Tuấn, Tạp Chí Phố Wall, Viet Hustler, etc.
    - Tool: WebFetch
    - Chỉ lấy bài mới nhất mỗi tác giả

  Tier 5 - X/Twitter (Real-time):
    ⚠️ HẠN CHẾ: X/Twitter cần JavaScript rendering. Các phương án:
    a) Ưu tiên: Dùng WebFetch lấy RSS/JSON alternative (nếu có)
    b) Thay thế: Dùng WebSearch tìm "@username latest tweets"
    c) Nếu bắt buộc: Spawn Agent subagent_type:"GeneralPurpose" để scrape
    - Lấy 3-5 posts mới nhất mỗi account
    - Ưu tiên tin market-moving, breaking news

  Tier 6 - Brave Search (Discovery, optional):
    - ⚠️ API key auto-expire 31/03/2026
    - Tool: WebSearch (tích hợp sẵn trong Claude Code)
    - Max 3 queries mỗi lần chạy, 5 kết quả mỗi query
    - Dùng để khám phá tin nóng ngoài danh sách nguồn cố định

Incremental logic:
  - Hash mỗi URL → so sánh với cache.json
  - Chỉ fetch entries MỚI (chưa có trong cache)
  - TTL cache: 24h

Output: raw_data.json chứa {source_id, title, url, content_snippet, timestamp, hash}
```

### Phase 1.5: VERIFY (Fact Extraction + Confidence Gate) 🆕 v5.0

**Nguyên tắc:** Không viết FACT trực tiếp từ raw_data. BẮT BUỘC tạo lớp verification trung gian.

```yaml
Input: raw_data.json từ Phase 1

Steps:
  1. Load rules từ sources.json > confidence_rules
  2. Với mỗi raw item, classify fact type:
     - A_GEOPOLITICAL / B_FINANCIAL / C_REGULATORY / D_EXPERT_OPINION / E_TECH_AI / F_MARKET_COLOR

  3. Verification theo decision tree:
     - A_GEOPOLITICAL: bắt buộc article body; không có -> LOW -> chỉ làm topic_hint
     - B_FINANCIAL: cần >=2 nguồn độc lập; nguồn đơn -> MEDIUM + [UNVERIFIED]
     - C_REGULATORY: ưu tiên nguồn official
     - D_EXPERT_OPINION: ưu tiên article gốc
     - E_TECH_AI: headline+snippet acceptable
     - F_MARKET_COLOR: >=1 nguồn acceptable

  4. Tạo fact_extraction.json theo template:
     (xem references/fact-extraction-template.md)
     - facts[]            (verified claims)
     - topic_hints[]      (low confidence, không được dùng làm FACT)
     - rejected_facts[]   (conflict/hallucination/narrative contamination)

  5. Chạy bias check cho từng fact:
     - narrative_contamination?
     - actor_clarity? (ai làm gì, ai nói gì)
     - number_verification? (số liệu có đúng raw source?)

  6. Confidence Gate (BẮT BUỘC pass):
     - no_low_geopolitical = true
     - low_confidence_count = 0
     - verification_rate >= 70%
     - all_reported_facts_in_extraction = true

Output:
  - fact_extraction.json
  - verification_summary.json

⚠️ FAIL conditions:
  - Any GEOPOLITICAL fact = LOW -> BLOCK (không được qua Phase 2)
  - Any FINANCIAL conflict -> REJECT fact
  - >30% facts LOW -> STOP và báo user
```

### Phase 2: INSIGHT (BA/BI Analysis)

**Model:** Active model (Gemini hoặc Opus tùy mode)

```yaml
Input: fact_extraction.json từ Phase 1.5 (thay cho raw_data trực tiếp)

Steps (CHUNG cho cả 2 mode):
  1. Phân loại tin theo 5 mục:
     - 🌍 Vĩ mô toàn cầu & Địa chính trị
     - 📊 Thị trường CK Việt Nam (Bluechips, doanh nghiệp)
     - ⚖️ Chính sách, Thuế, Luật
     - 💻 Công nghệ, AI & Thiết bị
     - ✍️ Góc nhìn chuyên gia (Substack/Expert)

  2. Viết FACT cho mỗi mục:
     - CHỈ dùng claims từ fact_extraction.json > facts[]
     - Tuyệt đối không viết fact từ topic_hints[] hoặc rejected_facts[]
     - Sự kiện khách quan, có số liệu cụ thể
     - Link nguồn gốc bắt buộc
     - 2-4 câu mỗi fact
     - Nếu fact confidence=MEDIUM: thêm tag [UNVERIFIED] (nếu thuộc B_FINANCIAL) hoặc diễn đạt mềm hơn"theo nguồn..."

  3. Viết VIEW theo IEIA Framework:
     (Xem chi tiết: references/ieia-framework.md)
     - I: Insight — Phát hiện gì?
     - E: Evidence — Bằng chứng (con số)
     - I: Impact — Tác động?
     - A: Action — Nên làm gì?

  4. Tạo Data Visualization:
     - X-RADAR table: 6-8 chỉ số chính
     - Sector Heatmap: ASCII bar chart ngành
     (Xem mẫu: references/output-template.md)

  5. Chấm quality_score (1-5) cho mỗi item

🟣 OPUS MODE — Bổ sung thêm:
  6. Second-order effects analysis:
     - Mỗi VIEW thêm mục "Hiệu ứng bậc 2" — hệ quả gián tiếp
     - Ví dụ: Dầu tăng → chi phí logistics → margin DN vận tải giảm → cổ phiếu VTP, GMD rủi ro

  7. Correlation matrix (Cross-asset signals):
     - Bảng tương quan giữa các chỉ số: DXY vs VN-Index, Dầu vs Vàng, FED vs Bond yield
     - Xác định divergence (tín hiệu bất thường)

  8. Sector Rotation signals:
     - Money flow: tiền đang chảy vào ngành nào, rút khỏi ngành nào?
     - So sánh tuần này vs tuần trước

  9. Expert view comparison:
     - So sánh quan điểm giữa các chuyên gia (bullish vs bearish)
     - Highlight bất đồng lớn — đó thường là tín hiệu quan trọng

Output: draft_report.md
```

### Phase 3: STRATEGY (CFA/Editor Review)

**Model:** Active model (Gemini hoặc Opus tùy mode)

```yaml
Input: draft_report.md từ Phase 2

Steps (CHUNG cho cả 2 mode):
  1. Strategy Review:
     - Xây dựng KEY THESIS (1 luận điểm xuyên suốt báo cáo)
     - Viết STRATEGIC TAKEAWAY (3 khuyến nghị hành động cụ thể)
     - Kiểm tra nhất quán logic giữa các phần

  2. Tone of Voice Polish:
     (Xem chi tiết: references/tone-guide.md)
     - Viết Hook mở bài (chọn 1 trong 4 loại)
     - Chuyển từ liệt kê → narrative flow
     - Đảm bảo insight actionable
     - Kết bài có cliff-hanger

  3. Final Edit:
     - Kiểm tra link, số liệu, chính tả
     - Đảm bảo mỗi FACT có link nguồn
     - Format chuẩn Markdown

🟣 OPUS MODE — Bổ sung thêm:
  4. Scenario Analysis (Bull / Base / Bear):
     - Mỗi scenario: điều kiện kích hoạt + xác suất + tác động VN-Index
     - Ví dụ: Bull (Trump deal Iran, dầu <$90) 30% → VN-Index 1.650+
     -         Base (status quo, dầu $95-100) 50% → VN-Index 1.580-1.620
     -         Bear (escalation, dầu >$110) 20% → VN-Index test 1.500

  5. Contrarian View:
     - Chủ động đưa 1 quan điểm NGƯỢC với consensus
     - Giải thích logic và evidence
     - Mục đích: tránh confirmation bias

  6. Actionable Trigger Levels:
     - Giá cụ thể: "Mua nếu VN-Index < X", "Bán nếu Y > Z"
     - Thời điểm: "Chờ đến phiên ngày XX/XX trước khi quyết định"
     - Loại lệnh: Market/Limit, position size gợi ý

  7. Deep Narrative:
     - Viết dạng long-form essay, không liệt kê
     - Mỗi phần kết nối với phần trước bằng transition
     - Kết bài có "So what?" — 1 câu tóm gọn toàn bộ

Output: final_report.md
```

### Phase 4: PUSH (Quality Gate + Versioning + GitHub)

```yaml
Quality Gate — PHẢI pass ALL:
  (Xem chi tiết: references/quality-gate.md)
  ✓ has_thesis: Có KEY THESIS?
  ✓ has_all_sections: Đủ 5 phần chính?
  ✓ facts_have_sources: Mỗi FACT có ≥1 link?
  ✓ has_ieia: Mỗi VIEW có đủ 4 thành phần IEIA?
  ✓ min_word_count: ≥ 2000 từ?
  ✓ no_placeholders: Không "[TBD]", "TODO", "..."?
  ✓ has_hook: Có hook ở mở bài?
  ✓ has_takeaway: Có strategic takeaway ở kết bài?

→ FAIL? → Quay lại Phase 3, không push
→ PASS? → Tiếp tục:

Versioning:
  - Filename: News_YYYY-MM-DD_HHMM_vX.Y.md
  - Metadata header trong file
  - Lưu vào NewsReport/YYYY/MM/ (trong thư mục skill)

GitHub Auto-Push:
  Repo: TrungTV76/Daily (https://github.com/TrungTV76/Daily)
  Local: C:\Drive Trungtv.en\Code\Antigravity\Daily\  (hoặc /c/Drive Trungtv.en/Code/Antigravity/Daily/)

  ⚠️ Claude Code CLI (Windows Git Bash / WSL):
  Steps (LUÔN thực hiện sau khi pass Quality Gate):
    1. Tạo thư mục: mkdir -p "C:/Drive Trungtv.en/Code/Antigravity/Daily/YYYY/MM/"
    2. Copy file: cp NewsReport/YYYY/MM/News_*.md → Daily/YYYY/MM/
    3. cd "C:/Drive Trungtv.en/Code/Antigravity/Daily"
    4. git add .
    5. git commit -m "📰 Daily Briefing YYYY-MM-DD vX.Y"
    6. git push origin main
    7. Log kết quả push vào cache.json > quality_gate_log

  ⚠️ Lưu ý: Nếu không có quyền push trực tiếp, có thể:
    - Tạo file tại thư mục skill (NewsReport/) rồi thông báo cho user tự push
    - Hoặc dùng gh cli: gh auth login → gh repo clone → cp → commit → push
    - Bước git push có thể SKIP nếu repo không accessible từ Claude CLI
```

### Phase 5: NOTIFY

```yaml
Token Usage Tracking:
  Trước khi hoàn thành, GHI NHẬN vào cuối báo cáo:
  - Token count cho mỗi model (Input + Output)
  - Chi phí ước tính theo pricing: Sonnet ($3/$15 per 1M), Opus ($15/$75 per 1M)
  - WebSearch queries count × $0.005
  - Tổng chi phí toàn pipeline

⚠️ Model Note (Claude Code CLI):
  - Claude Code CLI KHÔNG hỗ trợ tự động routing giữa Gemini/Sonnet/Opus
  - Khi user gọi skill, Claude Code dùng MODEL HIỆN TẠI của session
  - gemini mode = dùng model hiện tại (nhanh, chi phí thấp)
  - opus mode = dùng model hiện tại (sâu hơn, chi phí cao hơn)
  - User có thể override model bằng: /model-route hoặc Claude Code settings

Timestamp:
  - Tiêu đề: "THE MACRO BRIEFING (DD/MM/YYYY • HH:MM ICT)"
  - Metadata header: "Cập nhật lúc: HH:MM ngày DD/MM/YYYY (ICT, GMT+7)"
  - Filename: News_YYYY-MM-DD_HHMM_vX.Y.md
  - Footer: "Cập nhật lúc: HH:MM ngày DD/MM/YYYY (ICT)"

Console Output:
  - In summary: Key Thesis + 5 highlights + link file + token usage

Telegram (nếu đã cấu hình MCP server):
  ⚠️ Claude Code CLI không có Telegram bot tích hợp sẵn
  - Có thể cấu hình qua MCP server tùy chỉnh
  - Hoặc: Tạo Telegram bot riêng + webhook endpoint (xem skill telegram-bot-builder)

Cross-posting (optional):
  - Twitter/X thread với key takeaways
  - ⚠️ Dùng Agent (GeneralPurpose) để compose tweet, user tự post
  - Hoặc cấu hình X/Twitter MCP server nếu có
```

---

## Constraints & Principles

1. **IEIA mandatory** — Mọi VIEW phải có Insight→Evidence→Impact→Action
2. **Substack tone** — Hook + Narrative + Actionable + Cliff-hanger
3. **Incremental first** — Chỉ fetch tin mới, không quét lại toàn bộ
4. **Quality Gate** — Không push nếu chưa pass checklist
5. **Evidence before claims** — Không tuyên bố hoàn thành nếu chưa verify
6. **Cost-aware** — Phase 1 không dùng LLM, Phase 2 dùng Sonnet, Phase 3 dùng Opus
7. **Fault tolerance** — 1 nguồn lỗi không block toàn pipeline

---

## Expected Performance

| Scenario | Thời gian | Ghi chú |
|----------|-----------|---------|
| Optimal | ~3 min | Tier 1-2 đủ tin, không cần browser |
| Normal | ~5 min | Cần Tier 3-4 bổ sung |
| Full scope | ~8 min | Tất cả tiers + browser scrape |

---

## Related Skills

- `agentic-analytics` — IEIA framework, grain analysis, DAX measures, enterprise BI standards
- `social-content` — Hook formulas, engagement patterns
- `content-creator` — Brand voice, SEO optimization
- `verification-before-completion` — Quality gate methodology
- `agent-orchestration-multi-agent-optimize` — Cost optimization
- `telegram-bot-builder` — Notification delivery
- `x-article-publisher` — Cross-posting
