# Quality Gate — Verification Checklist

> Kế thừa từ `verification-before-completion` skill: **Evidence before claims.**
> Báo cáo KHÔNG được publish nếu chưa pass TẤT CẢ items dưới đây.

---

## Pre-Push Checklist

### Cấu trúc (Structure)
- [ ] **has_thesis** — Có KEY THESIS (luận điểm xuyên suốt) ở đầu báo cáo?
- [ ] **has_all_sections** — Đủ 5 phần chính?
  - [ ] 🌍 I. Vĩ mô toàn cầu & Địa chính trị
  - [ ] 📊 II. Thị trường CK Việt Nam
  - [ ] ⚖️ III. Chính sách, Thuế, Luật
  - [ ] 💻 IV. Công nghệ, AI & Thiết bị
  - [ ] ✍️ V. Góc nhìn chuyên gia
- [ ] **has_xradar** — Có bảng X-RADAR (bảng chỉ số)?
- [ ] **has_takeaway** — Có STRATEGIC TAKEAWAY (≥3 khuyến nghị)?
- [ ] **has_cliffhanger** — Có cliff-hanger cho số tiếp theo?

### Nội dung (Content)
- [ ] **facts_have_sources** — Mỗi FACT có ≥1 link nguồn gốc?
- [ ] **views_have_ieia** — Mỗi VIEW có đủ 4 phần IEIA?
- [ ] **actions_specific** — Mọi Action trong VIEW cụ thể, đo lường được?
- [ ] **no_placeholders** — Không chứa "[TBD]", "TODO", "...", "[Cập nhật]"?
- [ ] **min_word_count** — ≥ 2000 từ?

### Độ sâu phân tích (Depth Gate — BẮT BUỘC)
- [ ] **min_facts_per_core_section** — Mỗi section I-IV có tối thiểu 5 FACT có nguồn?
- [ ] **second_order_present** — Mỗi VIEW có ít nhất 1 tác động bậc 2?
- [ ] **specific_triggers_present** — Có trigger rõ (mốc điểm/giá/điều kiện), không nói chung chung?
- [ ] **cross_asset_linkage** — Có liên kết chéo vĩ mô → ngành → doanh nghiệp?
- [ ] **no_technical_only_bias** — Không chỉ nói kỹ thuật/dòng tiền; có business context?

### Fact Verification (BẮT BUỘC — v2.0)
> Xem chi tiết: `references/fact-extraction-template.md`

- [ ] **has_fact_extraction** — Có file `fact_extraction.json` được tạo trong Phase 1.5?
- [ ] **no_low_geopolitical** — Không có FACT loại A (GEOPOLITICAL) nào có confidence = LOW?
- [ ] **no_low_financial_single** — Không có FACT loại B (FINANCIAL) đơn lẻ mà không verified?
- [ ] **all_reported_facts_in_extraction** — Mọi FACT trong báo cáo đều có trong fact_extraction.json?
- [ ] **narrative_contamination_check** — Mỗi FACT đã được đối chiếu với raw headline/snippet gốc?
- [ ] **actor_clarity** — Mọi FACT ghi rõ đúng actor (ai làm gì, ai nói gì)?
- [ ] **no_rejected_in_report** — Không có fact nào từ rejected_facts list được viết vào báo cáo?
- [ ] **verification_rate** — ≥70% facts có confidence = HIGH?
- [ ] **low_confidence_count** — Số facts LOW confidence = 0 (nếu >0 → FAIL)?

### Tone (Giọng văn)
- [ ] **has_hook** — Mở bài có Hook (Curiosity/Story/Contrarian/Data)?
- [ ] **narrative_flow** — Các phần liên kết mạch lạc (không liệt kê khô khan)?
- [ ] **no_generic** — Không có câu chung chung: "cần thận trọng", "nên theo dõi"?

### Doanh nghiệp / Ngành (Flexible Data Rule)
- [ ] **enterprise_optional_rule_applied** — Nếu không có dữ liệu sống còn (cổ tức/ĐHĐCĐ/hợp đồng/LN quý/nợ-cashflow) thì bỏ qua, không bịa?
- [ ] **enterprise_news_if_available** — Nếu có tin DN/ngành liên quan hoạt động tài chính thì đã đưa vào đúng section?
- [ ] **no_forced_fields** — Không ép đủ 5 ô DN khi dữ liệu nguồn không có?

### Metadata
- [ ] **has_metadata** — Header có: version, date, models, sources count?
- [ ] **correct_filename** — Filename đúng format: `News_YYYY-MM-DD_HHMM_vX.Y.md`?

---

## Fail Actions

```
IF any item FAILS:
  → Log lỗi cụ thể
  → Quay lại Phase 3 (STRATEGY) để fix
  → KHÔNG push lên GitHub
  → KHÔNG gửi Telegram notification (nếu có cấu hình)

IF ALL items PASS:
  → Version tag
  → Git commit: "📰 Daily Briefing YYYY-MM-DD vX.Y"
  → Git push (nếu repo accessible từ Claude CLI)
  → Thông báo kết quả trong Console output

⚠️ Claude Code CLI Notes:
  - GitHub push: Có thể SKIP nếu repo không accessible. Báo cáo vẫn lưu trong NewsReport/.
  - Telegram: Không có MCP Telegram. User tự copy/paste hoặc cấu hình MCP server riêng.
```

---

## Quality Score

| Score | Nghĩa | Điều kiện |
|-------|--------|-----------|
| ⭐⭐⭐⭐⭐ (5) | Excellent | Pass all + Insight sâu + Hook mạnh |
| ⭐⭐⭐⭐ (4) | Good | Pass all + 1-2 VIEW chưa sâu |
| ⭐⭐⭐ (3) | Acceptable | Pass all nhưng tone chưa tốt |
| ⭐⭐ (2) | Poor | Fail 1-2 items → phải fix |
| ⭐ (1) | Reject | Fail >3 items → rebuild |

### Fact Verification Failure Actions

```
IF any Fact Verification item FAILS:
  → [no_low_geopolitical]     → BLOCK ngay. Không push. Quay lại Phase 1.5.
  → [no_low_financial_single]→ BLOCK ngay. Thêm flag [UNVERIFIED] hoặc xóa fact.
  → [narrative_contamination]→ REJECT fact đó. Log vào rejected_facts.
  → [verification_rate <70%] → WARN. Cho phép proceed nhưng note trong report.
  → [low_confidence_count >0]→ FAIL → Không push cho đến khi = 0.

IF ALL Fact Verification items PASS:
  → Proceed với Phase 4 thông thường.
```

### Alert Thresholds

| Condition | Severity | Action |
|-----------|----------|--------|
| Any GEOPOLITICAL = LOW | 🔴 CRITICAL | Immediate block |
| Any FINANCIAL conflict | 🔴 CRITICAL | Immediate reject |
| >30% facts = LOW | 🔴 CRITICAL | Stop, report sources issue |
| Narrative contamination detected | 🟠 HIGH | Reject + log |
| Verification rate <70% | 🟡 WARN | Allow with note |
| Source fetch fail >50% | 🟡 WARN | User decision |
