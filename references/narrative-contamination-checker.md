# Narrative Contamination Checker v2.0

> **Mục đích:** Phát hiện và loại bỏ facts bị contamination — claims được viết phù hợp với narrative đang xây dựng thay vì đúng với dữ liệu thực
> **Khi nào chạy:** Sau Phase 1.5 VERIFY, TRƯỚC Phase 2 INSIGHT
> **Nguyên tắc:** Mọi fact phải "fit" dữ liệu, không phải narrative phải "fit" fact

---

## 5 Loại Contamination

| # | Type | Mô tả | Ví dụ thực tế |
|---|------|--------|--------------|
| 1 | **Actor swap** | Viết sai actor (ai làm gì) | "Iran phóng 948 UAV" → thực tế Nga |
| 2 | **Number inflation** | Phóng đại số liệu để dramatic | "VN-Index mất 1.200 điểm" → thực tế 120 điểm |
| 3 | **Narrative fit** | Fact đúng nhưng được chọn vì phù hợp narrative | Chọn 3 tin bearish để xây "thị trường sụp đổ" → bỏ qua 5 tin bullish |
| 4 | **Causation inference** | Correlation bị gán thành causation | "Hôm đó dầu tăng → thị trường giảm" → không có bằng chứng causation |
| 5 | **Silent omission** | Cố tình bỏ qua facts conflict với narrative | Không đề cập Fed pivot vì conflict với bearish thesis |

---

## Automated Checklist (Điều tra từng fact)

### Checklist A: Actor Clarity
```
Với MỖI fact có action (tấn công, ký kết, phát biểu, phạt...):

□ Ai làm? → Ghi rõ tên nước/tổ chức/cá nhân
□ Ai nhận? → Ghi rõ đối tượng bị tác động
□ Nguồn xác nhận? → Có link đến article body không?
□ Fact có match với raw headline? → Copy exact phrase từ source
□ Có thay đổi actor trong quá trình paraphrase?

Hành động:
  - Mismatch: REJECT → vào rejected_facts[]
  - Partial match: MEDIUM + flag [ACTOR_UNCLEAR]
  - Exact match: HIGH
```

### Checklist B: Number Verification
```
Với MỖI fact có số liệu cụ thể:

□ Số này đến từ đâu trong raw data?
□ Số này trùng khớp với headline/snippet gốc?
□ Có phóng đại không? (VD: "120 điểm" → "1200 điểm")
□ Số có context đầy đủ? (VD: "tăng 24 điểm" — từ đâu, đến đâu?)
□ Số phù hợp với magnitude của sự kiện?

Hành động:
  - Number inflated: REJECT → vào rejected_facts[]
  - Number unverified: MEDIUM + flag [NUMBER_UNVERIFIED]
  - Number verified: HIGH
```

### Checklist C: Narrative Fit Check
```
Với MỌI facts trong report:

□ Fact này có TRÙNG KHỚP QUÁ MỨC với thesis đang viết?
□ Có bao nhiêu facts trong raw data ủng hộ thesis này?
□ Có bao nhiêu facts CONFLICT với thesis?
□ Ratio ủng hộ : conflict = ?

Decision:
  - Ratio > 5:1 → HIGH RISK → kiểm tra lại thesis
  - Có conflict facts bị bỏ qua → REJECT those omissions + note
  - Balanced: OK
```

### Checklist D: Causation vs Correlation
```
Với MỌI IEIA Impact/Action:

□ "Dầu tăng → thị trường giảm" — có evidence causation không?
□ Hay chỉ là temporal correlation?
□ Có chain of events rõ ràng?

Example of GOOD causation:
  "Dầu tăng → chi phí vận tải tăng 12% (VN logistics report)
   → margin VTP giảm → cổ phiếu VTP giảm"

Example of BAD causation (correlation):
  "Dầu tăng cùng ngày thị trường giảm → dầu gây giảm thị trường"
   → KHÔNG ĐỦ EVIDENCE
```

### Checklist E: Silent Omission Detection
```
Đọc lại raw data SAU KHI viết draft:

□ Có fact nào trong raw data mà tôi KHÔNG đưa vào report?
□ Tại sao tôi bỏ qua fact đó?
  - Vì không liên quan? → OK
  - Vì conflict với narrative? → REJECT → thêm vào report
□ Bias pattern: Tôi có xu hướng bỏ qua facts nào?

Tạo "Omission Log":
{
  "omitted_fact": "...",
  "reason_omitted": "conflict_with_thesis | not_relevant | oversight",
  "recovery_action": "ADD_TO_REPORT | ACKNOWLEDGE"
}
```

---

## Structured Output

```json
{
  "report_date": "YYYY-MM-DD",
  "check_version": "2.0",

  "contamination_report": {
    "total_facts_checked": 28,
    "clean_facts": 23,
    "actor_issues": 2,
    "number_issues": 1,
    "narrative_fit_issues": 1,
    "causation_issues": 0,
    "omission_issues": 1,

    "rejected_facts": [
      {
        "id": "F007",
        "type": "ACTOR_SWAP",
        "raw_claim": "Iran phóng 948 UAV vào Ukraine",
        "actual_source": "Russia launched 948 UAV at Ukraine (BBC headline)",
        "contamination": "Actor Iran không khớp với BBC headline",
        "action": "REJECTED",
        "correction": "Nga phóng 948 UAV vào Ukraine"
      }
    ],

    "flagged_facts": [
      {
        "id": "F012",
        "type": "ACTOR_UNCLEAR",
        "claim": "Mỹ đe dọa tấn công Iran",
        "issue": "Không rõ Mỹ đe dọa bằng cách nào, bối cảnh nào",
        "confidence": "MEDIUM",
        "action": "ADD [ACTOR_UNCLEAR] tag"
      }
    ],

    "omission_log": [
      {
        "fact": "VN-Index tăng 28 điểm phiên 27/3",
        "reason": "oversight",
        "recovery": "ADDED to Section II"
      }
    ],

    "causation_warnings": [
      {
        "ieia_id": "I-C-Impact-003",
        "claim": "Dầu tăng → thị trường giảm",
        "issue": "Không có evidence causation, chỉ correlation",
        "action": "Thêm qualifying language: 'có thể', 'tương quan'"
      }
    ],

    "narrative_fit_check": {
      "thesis": "Thị trường đang suy yếu",
      "supporting_facts": 8,
      "conflicting_facts": 3,
      "ratio": "2.7:1",
      "verdict": "ACCEPTABLE",
      "note": "Có 3 conflicting facts đã được add vào report"
    }
  },

  "verdict": "PASS | FAIL",
  "blocking_issues": [],
  "warnings": [],
  "operator_action_required": []
}
```

---

## Contamination Severity Scale

| Level | Type | Action |
|-------|------|--------|
| 🔴 CRITICAL | Actor swap (đổi nước/tổ chức) | REJECT + correct ngay |
| 🔴 CRITICAL | Number inflation >10x | REJECT + verify |
| 🟠 HIGH | Number inflation 2-10x | REJECT + correct |
| 🟠 HIGH | Causation claimed without evidence | ADD qualifying language |
| 🟡 MEDIUM | Narrative fit ratio >5:1 | REVIEW thesis — có thể REJECT |
| 🟡 MEDIUM | Silent omission (conflict fact) | ADD to report |
| 🟢 LOW | Actor unclear but verifiable | ADD [ACTOR_UNCLEAR] tag |

---

## Quick Scan (30 giây — dùng trước mỗi báo cáo)

```
Đọc lại 3 facts đầu tiên của mỗi section:
1. Ai làm gì? → Đúng actor?
2. Con số? → Đúng như source?
3. Tại sao fact này trong report? → Vì đúng hay vì phù hợp thesis?

Đọc thesis 1 lần nữa:
- Thesis có bị "overfit" với 1-2 dramatic facts không?
- Có facts ngược chiều thesis không? → Đã đưa vào chưa?
```

---

## Hard Rules

1. **Actor swap = immediate REJECT** — không có ngoại lệ
2. **Không bao giờ bỏ qua fact conflict với thesis** — phải đưa vào
3. **Correlation ≠ Causation** — luôn dùng qualifying language
4. **Số liệu phải match với raw source** — không được "round up/down" để dramatic
5. **5+:1 ratio = red flag** — kiểm tra lại thesis
