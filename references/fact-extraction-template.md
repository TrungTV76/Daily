# Fact Extraction Template v2.0

> **Mục đích:** Bước trung gian bắt buộc giữa FETCH và INSIGHT.
> **Tại sao:** Ngăn hallucination, confirmation bias, narrative contamination khi viết FACT.
> **Khi nào chạy:** Sau Phase 1 (FETCH), trước Phase 2 (INSIGHT).

---

## 🎯 Fact Confidence Classification

Mỗi fact phải được phân loại theo **source type** và **fetch status**, sau đó gán **confidence level** tương ứng.

### 6 Loại Fact & Confidence Rules

| Loại | Mô tả | Nguồn ví dụ | Requires | Confidence |
|------|--------|-------------|---------|-----------|
| **A. GEOPOLITICAL** | Sự kiện quân sự, chính trị, ngoại giao cụ thể | BBC, Reuters, CNBC, Bloomberg | Article body (không chỉ RSS) | 🔴 **always verify** |
| **B. FINANCIAL** | Chỉ số thị trường, giá cổ phiếu, tỷ giá, lãi suất | CafeF, VnExpress, VnEconomy | ≥2 nguồn độc lập | 🔴 **always verify** |
| **C. REGULATORY** | Luật, thuế, thông tư, nghị định chính thức | Vneconomy, Thuvienphapluat, Chinhphu.vn | Official source hoặc official summary | 🟡 **verify when possible** |
| **D. EXPERT_OPINION** | Quan điểm, phân tích từ chuyên gia/substack | Noah Smith, Mollick, Hồ Quốc Tuấn | Article body gốc | 🟡 **verify when possible** |
| **E. TECH_AI** | Tin công nghệ, sản phẩm, funding rounds | HN, The Verge, CNBC, GenK | RSS headline + snippet (acceptable) | 🟢 **acceptable with source** |
| **F. MARKET_COLOR** | Hành vi thị trường, dòng tiền, cổ đông lớn | CafeF, VnExpress | ≥1 source acceptable | 🟡 **verify when possible** |

---

## 🔴 Confidence Levels

| Level | Ý nghĩa | Có trong báo cáo? |
|-------|---------|-----------------|
| **high** | Có article body + ≥2 nguồn (nếu là type B) | ✅ Có |
| **medium** | Có RSS/headline nhưng không fetch được body | ⚠️ Dùng được, ghi rõ nguồn |
| **low** | Không fetch được, chỉ có title/summary từ nguồn khác | ❌ Không dùng làm FACT, chỉ dùng làm **TOPIC HINT** |
| **rejected** | Conflicting facts, hallucinated, contradicted by raw data | ❌ Tuyệt đối không dùng |

---

## 📋 Required Output: `fact_extraction.json`

Schema bắt buộc cho mỗi fact:

```json
{
  "schema_version": "2.0",
  "report_date": "YYYY-MM-DD",
  "run_timestamp": "ISO8601",

  "facts": [
    {
      "id": "A01",
      "category": "A_GEOPOLITICAL | B_FINANCIAL | C_REGULATORY | D_EXPERT_OPINION | E_TECH_AI | F_MARKET_COLOR",
      "source": "cafef_ck",
      "source_url": "https://...",
      "source_fetch_method": "rss | web_scrape | agent_scrape | websearch",
      "source_fetch_status": "article_body | headline_only | failed | fallback_used",
      "source_fetch_note": "Mô tả lỗi cụ thể nếu fail",

      "raw_headline": "Tiêu đề gốc từ nguồn",
      "raw_snippet": "Nội dung gốc từ source (exact copy, không paraphrase)",

      "verified_claim": "Claim đã được xác nhận CHÍNH XÁC từ source",
      "verification_method": "article_body | cross_reference | single_source_acceptable",
      "verification_note": "Ghi chú: tại sao claim này đáng tin",

      "confidence": "high | medium | low | rejected",
      "used_in_report": true,
      "report_location": "Section I, FACT #3",
      "report_wording": "Exact wording đã dùng trong báo cáo",

      "bias_check": {
        "narrative_contamination": false,
        "confirmation_bias": false,
        "self_consistency": true,
        "conflict_with_other_facts": false,
        "notes": "Nếu có vấn đề, ghi rõ"
      }
    }
  ],

  "topic_hints": [
    {
      "id": "H01",
      "topic": "Mô tả topic được nhắc đến nhưng CHƯA verify",
      "source": "bbc_world",
      "confidence": "low",
      "note": "Dùng để brainstorm IEIA, không dùng làm FACT"
    }
  ],

  "rejected_facts": [
    {
      "id": "R01",
      "raw_claim": "Claim bị loại",
      "reason": "contradicts_raw_data | hallucinated | unverified_geopolitical | conflicting_sources",
      "found_in": "Draf nào đã dùng claim này"
    }
  ],

  "fetch_summary": {
    "total_raw_items": 40,
    "total_verified_facts": 28,
    "total_topic_hints": 4,
    "total_rejected": 2,
    "verification_rate": "70%",
    "high_confidence_count": 22,
    "medium_confidence_count": 6,
    "low_confidence_count": 0,
    "rejected_count": 2
  }
}
```

---

## 🔍 Verification Decision Tree

```
Mỗi fact mới → Đi qua DECISION TREE:

1. Loại fact là gì?
   ├── A_GEOPOLITICAL hoặc B_FINANCIAL?
   │   ├── Có article body?
   │   │   ├── Có → confidence = HIGH → Dùng được
   │   │   └── Không → confidence = LOW → Dùng làm TOPIC HINT, KHÔNG viết FACT
   │   └── Type B_FINANCIAL thêm: ≥2 nguồn độc lập?
   │       ├── Có → confidence = HIGH
   │       └── Không → confidence = MEDIUM → Ghi rõ nguồn đơn lẻ
   │
   ├── C_REGULATORY?
   │   ├── Từ nguồn official (gov.vn, thuvienphapluat)?
   │   │   ├── Có → confidence = HIGH
   │   │   └── Không → confidence = MEDIUM
   │   └── Có đang trong giai đoạn "đề xuất" không?
   │       └── Có → thêm prefix "[ĐỀ XUẤT] " trong báo cáo
   │
   ├── D_EXPERT_OPINION?
   │   ├── Có article body từ Substack/blog gốc?
   │   │   ├── Có → confidence = HIGH
   │   │   └── Không → confidence = MEDIUM → Ghi rõ đang dùng summary
   │
   ├── E_TECH_AI?
   │   └── headline + snippet đủ → confidence = MEDIUM (acceptable)
   │
   └── F_MARKET_COLOR?
       └── ≥1 nguồn → confidence = MEDIUM
```

---

## ⚠️ Hard Rules (Cannot Override)

1. **GEOPOLITICAL + LOW confidence = NEVER in report**
   - Không viết "Nước X tấn công nước Y" khi chỉ có RSS title
   - Không viết số lượng tên lửa, quân sự khi không đọc được article

2. **FINANCIAL + SINGLE SOURCE = MEDIUM only**
   - "VN-Index tăng 24 điểm" — phải có tối thiểu 2 nguồn
   - Không có exception cho "vì đây là tin nóng"

3. **CONFLICT = REJECT both**
   - Nếu 2 nguồn conflict về fact → REJECT cả 2, dùng topic hint
   - Không "vote majority" giữa các nguồn

4. **NARRATIVE CONTAMINATION = REJECT**
   - Nếu claim được viết phù hợp với narrative đang build → REJECT
   - Dấu hiệu: claim trùng khớp quá hoàn hảo với thesis đang viết

---

## 📝 Bias Check Questions

Với mỗi fact trước khi xác nhận, tự hỏi:

1. **Narrative contamination:** Claim này có phải tôi "muốn" nó đúng không? (Vì nó phù hợp narrative?)
2. **Source match:** Claim có TRÙNG KHỚP với raw headline/snippet không, hay đã được paraphrase/sửa đổi?
3. **Confidence alignment:** Tôi đang gán HIGH confidence vì nó đúng hay vì nó phù hợp?
4. **Contradiction check:** Có fact nào TRONG cùng raw data mà contradict claim này không?
5. **Actor clarity:** Tôi có chắc đúng actor (ai làm gì, ai nói gì) không?

---

## 📁 Tích hợp Pipeline

```
Phase 1: FETCH
  Output: raw_data.json (tất cả items từ RSS/WebScrape/WebSearch)

Phase 1.5: VERIFY  ← BƯỚC MỚI
  Input:  raw_data.json
  Output: fact_extraction.json
  Action: Đi qua decision tree cho TỪNG fact
  Gate:   Không proceed nếu >20% facts có confidence < MEDIUM

Phase 2: INSIGHT
  Input:  fact_extraction.json  ← THAY ĐỔI: dùng verified facts
  Output: draft_report.md
  Rule:   CHỈ viết FACT từ fact_extraction.json

Phase 3: STRATEGY
  Input:  draft_report.md
  Output: final_report.md

Phase 4: PUSH (Quality Gate)
  Input:  final_report.md + fact_extraction.json
  Gate:   Kiểm tra fact_extraction.json confirmation
```

---

## 🚨 Alert Triggers (Auto-flag cho operator)

| Trigger | Action |
|---------|--------|
| >30% facts = LOW confidence | Dừng lại, báo cáo sources có vấn đề, không proceed |
| Any GEOPOLITICAL = LOW | Block ngay, không viết vào report |
| Any FINANCIAL conflict | Reject both, flag trong report |
| Narrative contamination detected | Reject fact, ghi log bias_check |
| Source fetch fail rate >50% | Báo cáo nguồn không khả dụng, user decide có tiếp tục không |
