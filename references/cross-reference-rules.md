# Cross-Reference Rules — Financial Fact Verification v2.0

> **Mục đích:** Tự động cross-reference facts loại B (FINANCIAL) với ≥2 nguồn độc lập trước khi đưa vào báo cáo
> **Nguyên tắc:** Không có fact tài chính nào đứng một mình — luôn cần ít nhất 2 nguồn xác nhận
> **Trigger:** Mọi fact có source_id thuộc tier1_vn_market hoặc B_FINANCIAL

---

## Fact Categories & Cross-Reference Requirements

| Category | Required Sources | Acceptable Single Source? |
|----------|----------------|------------------------|
| B_FINANCIAL (market prices) | ≥2 nguồn độc lập | ❌ No — block |
| B_FINANCIAL (earnings/guidance) | ≥2 nguồn độc lập | ❌ No — block |
| B_FINANCIAL (M&A/deals) | ≥2 nguồn độc lập | ❌ No — block |
| C_REGULATORY (laws/policy) | ≥1 official source | ✅ Acceptable |
| C_REGULATORY (proposed) | ≥1 source | ✅ Acceptable + "[PROPOSAL]" tag |
| A_GEOPOLITICAL | Article body bắt buộc | ❌ No body → LOW → topic_hint |
| E_TECH_AI | Headline+snippet acceptable | ✅ Acceptable |
| F_MARKET_COLOR | ≥1 source | ✅ Acceptable |

---

## Source Pairs (Cross-Reference Map)

```yaml
vn_market_index:
  - ["cafef_ck", "vnexpress_kd"]
  - ["cafef_ck", "vneconomy_tc"]
  - ["cafef_ck", "vietstock_dn"]

vn_market_earnings:
  - ["cafef_ck", "vietstock_dn"]
  - ["cafef_ck", "vneconomy_tc"]
  - ["vneconomy_tc", "vnexpress_kd"]

fx_rate_usd_vnd:
  - ["vneconomy_tc", "cafef_ck"]
  - ["cafef_ck", "bloomberg"]

oil_price:
  - ["bloomberg", "cnbc_world"]
  - ["bloomberg", "bbc_world"]
  - ["cnbc_world", "reuters_biz"]

fed_rate_decision:
  - ["cnbc_world", "bloomberg"]
  - ["bloomberg", "bbc_world"]

trade_tariff_news:
  - ["bbc_world", "cnbc_world"]
  - ["reuters_biz", "bbc_world"]
  - ["cnbc_world", "vnexpress_kd"]
```

---

## Cross-Reference Decision Tree

```
FACT: "VN-Index tăng 24 điểm"
Source: CafeF
Confidence: ?

1. Category = B_FINANCIAL (market price)?
   → YES
2. Required pair: vn_market_index = [cafef_ck, vnexpress_kd]
3. Check: vnexpress_kd có confirm?
   → YES ("VN-Index tăng 24.5 điểm" — gần trùng khớp)
   → confidence = HIGH
   → proceed to report

FACT: "Fed giữ nguyên lãi suất"
Source: CNBC
Confidence: ?

1. Category = B_FINANCIAL (policy)?
   → YES
2. Required pair: fed_rate_decision = [cnbc_world, bloomberg]
3. Check: bloomberg có confirm?
   → NO (hoặc fail)
   → confidence = MEDIUM
   → add [UNVERIFIED - single source]
   → warn operator

FACT: "Apple WWDC công bố AI mới"
Source: VnExpress
Confidence: ?

1. Category = E_TECH_AI?
   → YES
2. Required pair: Not required for E_TECH_AI
   → confidence = MEDIUM (acceptable)
   → proceed with source attribution
```

---

## Conflict Resolution

Khi 2 nguồn conflict về fact:

```
Source A: "VN-Index tăng 24 điểm"
Source B: "VN-Index giảm 12 điểm"

→ REJECT both
→ Create conflict record:
  {
    "id": "CONFLICT_001",
    "fact_claim_a": "VN-Index tăng 24 điểm",
    "fact_claim_b": "VN-Index giảm 12 điểm",
    "source_a": "cafef_ck",
    "source_b": "bloomberg",
    "time_a": "14:30",
    "time_b": "15:00",
    "resolution": "INCONCLUSIVE",
    "action": "Discard both from report"
  }
```

**Không bao giờ "vote majority"** giữa các nguồn conflict. Luôn reject cả 2.

---

## Quantitative Fact Verification

Với facts có số liệu cụ thể (%, điểm, tỷ đồng, USD...):

```
Rule: "Numbers must match within acceptable tolerance"

Tolerance:
  - Price/index levels:     ±0.5%
  - Percentages:             ±0.1%
  - Large sums (>1B):        ±2%
  - Small sums (<1B):       ±5%

Example:
  CafeF:  "VN-Index tăng 24.37 điểm"
  Vietstock: "VN-Index tăng 24.42 điểm"
  → DIFFERENCE: 0.05 điểm = 0.05%
  → TOLERANCE: ±0.5%
  → ACCEPT: difference within tolerance
  → confidence = HIGH
```

---

## Auto-Generate Cross-Reference Report

```json
// Tạo tự động sau khi chạy Phase 1.5 VERIFY

{
  "report_date": "YYYY-MM-DD",
  "total_financial_facts": 28,
  "cross_referenced": 22,
  "single_source": 4,
  "conflicts": 2,
  "verification_rate": "78.6%",

  "verified_facts": [
    {
      "id": "F001",
      "claim": "VN-Index tăng 24.37 điểm",
      "sources": ["cafef_ck", "vietstock_dn"],
      "confidence": "high",
      "cross_reference_match": true
    }
  ],

  "single_source_facts": [
    {
      "id": "F015",
      "claim": "Fed giữ lãi suất",
      "source": "cnbc_world",
      "confidence": "medium",
      "tag": "[UNVERIFIED - single source]",
      "action_required": "Tìm nguồn thứ 2 hoặc xóa"
    }
  ],

  "conflicts": [
    {
      "id": "CONFLICT_001",
      "claim_a": "...",
      "claim_b": "...",
      "action": "REJECTED"
    }
  ],

  "fail_conditions": [
    {
      "fact_id": "F003",
      "reason": "GEOPOLITICAL + LOW confidence",
      "action": "BLOCK — không đưa vào report"
    }
  ]
}
```

---

## Quality Gate Integration

```
IF cross_reference_failures > 0:
  → WARN: "[X] financial facts không có đủ 2 nguồn"
  → Operator decide: tìm thêm nguồn HOẶC đánh dấu [UNVERIFIED]
  → KHÔNG BLOCK (vì có thể tìm thêm source)

IF conflict_count > 0:
  → BLOCK: "Có [X] conflicts giữa các nguồn"
  → Phải resolve trước khi proceed

IF verification_rate < 70%:
  → WARN: "Chỉ [X]% facts verified"
  → Operator decide có proceed không
```
