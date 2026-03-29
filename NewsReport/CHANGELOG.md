# Daily Macro Briefing — Changelog

> Pipeline: **v6.3** (2026-03-29)
> Full pipeline: FETCH → **FETCH_VERIFY** → X/AGENT → BBC/BODY → VERIFY → CROSS-REF → NARRATIVE → INSIGHT → STRATEGY → **CONTENT_CHANGE_GATE** → QGATE v2.0 → CHANGELOG → PUSH → NOTIFY

> Lịch sử phiên bản báo cáo Daily Macro Briefing — ghi nhận tất cả thay đổi, cải tiến, và lý do cập nhật qua mỗi version.

---

---

## SKILL CONFIG v6.3 — 29/03/2026 | ✅ Phiên bản skill hiện tại

> **Root cause fixed:** Pipeline tạo version mới dù không fetch được tin mới (v6.1/v6.2 giống hệt nhau)

**Thay đổi lớn:** Thêm 2 gates bắt buộc ngăn silent fail và version bloat

**Cải tiến:**
- ✅ Thêm **Fetch Verification Gate** vào Phase 1 — ngăn silent fail
  - Tổng items >= 20, Tier 1-2 >= 10 items có content
  - FAIL nếu: tổng items < 10 HOẶC tất cả sources return empty
  - FAIL action: thử fallback methods → STOP + báo user "Fetch thất bại"
- ✅ Thêm **Content Change Gate** vào Quality Gate
  - has_new_facts: >= 5 facts mới so với phiên trước
  - thesis_changed: Key Thesis thay đổi đáng kể
  - sources_fetched: liệt kê cụ thể nguồn đã fetch (không "multiple sources")
  - FAIL action: Không tạo version mới, chỉ cập nhật timestamp trên file cũ
- ✅ Thêm **Versioning Rules** bắt buộc
  - Trigger tạo version mới: >= 5 facts mới HOẶC Key Thesis thay đổi HOẶC breaking news
  - Trigger cập nhật file cũ: chỉ thay đổi timestamp
  - Ghi `[NO_NEW_CONTENT — timestamp update only]` khi không có content mới
- ✅ Root cause ghi nhận: v6.1 (13:00) và v6.2 (19:00) giống hệt nhau vì pipeline không fetch được dữ liệu mới trong khoảng 13:00-19:00 29/03/2026

**Pipeline v6.3:**
```
FETCH → FETCH_VERIFY → X/AGENT → BBC/BODY → VERIFY → CROSS-REF → NARRATIVE → INSIGHT → STRATEGY → CONTENT_CHANGE_GATE → QGATE v2.0 → CHANGELOG → PUSH → NOTIFY
```

---

## SKILL CONFIG v6.0 — 29/03/2026

> **Ghi chú:** Skill config files nằm trong `~/.claude/skills/daily-macro-briefing/` — chưa sync riêng repo. Các files báo cáo nằm trong `NewsReport/`.

**📌 Thay đổi lớn:** Nâng cấp pipeline từ v5.0 lên v6.0 — 5 modules mới

**Nguyên nhân:** Thực hiện roadmap cải tiến đã đề xuất trong CHANGELOG v5.2

**Cải tiến:**
- ✅ Thêm `references/x-twitter-agent.md` — Agent-based scraping Tier 5 X/Twitter
- ✅ Thêm `references/fetch-article-bodies.md` — BBC/CNBC body fetch (AMP→Proxy→Agent→WebSearch→UNVERIFIED)
- ✅ Thêm `references/cross-reference-rules.md` — Auto financial fact ≥2 nguồn
- ✅ Thêm `references/narrative-contamination-checker.md` — Auto bias detection 5 loại
- ✅ Thêm `references/auto-changelog-generator.md` — Auto update CHANGELOG per version
- ✅ Update `references/quality-gate.md` — Fact Verification v2.0 (9 items bắt buộc)
- ✅ Update `SKILL.md` — Pipeline v6.0 + Full Reference Map

**Pipeline v6.0:**
```
FETCH → X/AGENT → BBC/BODY → VERIFY → CROSS-REF → NARRATIVE → INSIGHT → STRATEGY → QGATE v2.0 → CHANGELOG → PUSH → NOTIFY
```

**Files mới:**
| File | Lines | Mục đích |
|------|-------|---------|
| `references/x-twitter-agent.md` | ~150 | Agent scraping 10 accounts X/Twitter |
| `references/fetch-article-bodies.md` | ~180 | BBC/CNBC body fetch 5 methods |
| `references/cross-reference-rules.md` | ~200 | Financial fact ≥2 nguồn |
| `references/narrative-contamination-checker.md` | ~250 | 5-type bias detection |
| `references/auto-changelog-generator.md` | ~200 | Auto changelog per version |

---

## REPORT v6.2-OPUS — 29/03/2026 19:00

**📌 Thay đổi lớn:** Xuất bản snapshot OPUS 19:00 theo framework mới (Depth Gate + Enterprise Flexible Rule).

**Cải tiến:**
- ✅ Cập nhật mốc thời gian phiên tối 19:00 cho toàn bộ metadata và nội dung.
- ✅ Giữ cấu trúc phân tích sâu theo IEIA + scenario + contrarian + trigger levels.
- ✅ Áp dụng policy DN linh hoạt: chỉ đưa dữ liệu đã verify, thiếu thì ghi rõ trạng thái dữ liệu.

**File:** `NewsReport/2026/03/News_2026-03-29_1900_v6.2-OPUS.md`

---

## REPORT v6.2-OPUS — 29/03/2026 19:00

**📌 Thay đổi lớn:** Xuất bản snapshot OPUS 19:00 theo framework mới (Depth Gate + Enterprise Flexible Rule).

**Cải tiến:**
- ✅ Cập nhật mốc thời gian phiên tối 19:00 cho toàn bộ metadata và nội dung.
- ✅ Giữ cấu trúc phân tích sâu theo IEIA + scenario + contrarian + trigger levels.
- ✅ Áp dụng policy DN linh hoạt: chỉ đưa dữ liệu đã verify, thiếu thì ghi rõ trạng thái dữ liệu.

**File:** `NewsReport/2026/03/News_2026-03-29_1900_v6.2-OPUS.md`

---

## FRAMEWORK PATCH v6.1.1 — 29/03/2026

**📌 Thay đổi lớn:** Chuyển chính sách doanh nghiệp từ "bắt buộc 5 ô" sang "optional-if-available", đồng thời siết độ sâu nội dung bằng Depth Gate bắt buộc.

**📌 Thay đổi lớn:** Chuyển chính sách doanh nghiệp từ "bắt buộc 5 ô" sang "optional-if-available", đồng thời siết độ sâu nội dung bằng Depth Gate bắt buộc.

**📌 Thay đổi lớn:** Chuyển chính sách doanh nghiệp từ "bắt buộc 5 ô" sang "optional-if-available", đồng thời siết độ sâu nội dung bằng Depth Gate bắt buộc.

**Cập nhật framework:**
- ✅ Enterprise Flexible Rule: không ép đủ 5 ô (cổ tức/ĐHĐCĐ/hợp đồng/LN quý/nợ-cashflow) khi nguồn không có.
- ✅ Nếu không có dữ liệu verify → bỏ qua hoặc ghi rõ trạng thái dữ liệu, KHÔNG suy diễn.
- ✅ Depth Gate bắt buộc trong quality gate:
  - min_facts_per_core_section (I-IV >= 5 facts)
  - second_order_present
  - specific_triggers_present
  - cross_asset_linkage
  - no_technical_only_bias
- ✅ Cập nhật `SKILL.md` để đồng bộ policy mới.
- ✅ Cập nhật `references/quality-gate.md` với checklist mới.

**Files:**
- `SKILL.md`
- `references/quality-gate.md`
- `NewsReport/CHANGELOG.md`

---

## REPORT v6.1-OPUS — 29/03/2026 13:00

**📌 Thay đổi lớn:** Bổ sung lớp “Enterprise Survival Status” cho các mã được chỉ định (ACB, MSN, PVD, HAH, GMD).

**Cải tiến:**
- ✅ Section địa chính trị cập nhật nóng Mỹ–Israel–Iran (multi-front escalation, Hormuz, tác động vĩ mô).
- ✅ Chèn dữ liệu doanh nghiệp theo hướng optional-if-available (không ép đủ form khi thiếu nguồn verify).
- ✅ Nếu chưa có dữ liệu verify thì ghi rõ "Chưa có cập nhật mới đã xác minh" (không suy diễn).
- ✅ Tách rõ dữ liệu verify được và dữ liệu narrative để tránh nhầm.

**File:** `NewsReport/2026/03/News_2026-03-29_1300_v6.1-OPUS.md`

---

## REPORT v5.2-OPUS — 29/03/2026

**📌 Thay đổi lớn:** Rewrite toàn bộ 5 sections đầu — bổ sung Section VI nhóm cổ phiếu VN

**Nguyên nhân:** v5.0/v5.1 báo cáo ngắn bất thường (~120 dòng cho 5 sections). Lỗi root cause: viết report TRƯỚC khi fetch đầy đủ dữ liệu → Sections I-V chỉ có 3-5 facts, Section VI (viết sau khi đọc data) mới đầy đủ.

**Cải tiến:**
- ✅ Mở rộng Section I (Vĩ mô): 11 facts thay vì 3 (thêm ADB ASEAN -2.3% GDP, ECB tăng lãi suất, Trung Quốc điều tra Mỹ, Ấn Độ mua dầu Iran, Global Forecast 4.2% inflation, Zimbabwe 15 công dân chết...)
- ✅ Mở rộng Section II (VN Market): 12 facts thay vì 3 (thêm VN-Index tăng 28 điểm, bluechip áp lực, FLC 150K tỷ, Novaland hoàn nhập 3.3K tỷ, FDI tăng 200%, Gia Lai 800K tỷ...)
- ✅ Mở rộng Section III (Chính sách): 12 facts thay vì 3 (thêm 3 loại thuế về 0%, Nga cấm xuất khẩu xăng, kho dự trữ Nghi Sơn, thuế quan Mỹ 3 kịch bản...)
- ✅ Mở rộng Section IV (Tech/AI): 14 facts thay vì 3 (thêm Meta -8%, Anthropic thắng kiện...)
- ✅ Mở rộng Section V (Chiến lược): Đầy đủ X-RADAR, Scenario, Contrarian, Trigger Levels
- ✅ Thêm Section VI hoàn chỉnh: 6 nhóm cổ phiếu VN với khuyến nghị cụ thể từng mã

**Files:** `NewsReport/2026/03/News_2026-03-27_0905_v5.2-OPUS.md`

---

## REPORT v5.1-OPUS — 29/03/2026

**📌 Thay đổi:** Bổ sung Section VI — 6 nhóm cổ phiếu VN

**Nguyên nhân:** User yêu cầu thêm nhóm cổ phiếu VN để phục vụ phân tích danh mục.

**Cải tiến:**
- ✅ Thêm **Section VI: Nhóm cổ phiếu VN** — 6 nhóm theo ngành
- ✅ Nhóm Ngân hàng: VCB TPB VIB MBB ACB — VPBank 100K tỷ, VIB Max Card, BIDV bán nợ xấu
- ✅ Nhóm Tiêu dùng: SAB VNM QNS MSN — MWG cổ tức 3K tỷ
- ✅ Nhóm Dầu khí & NL: GAS PVD BSR REE — VinEnergo 8 tỷ USD, Nga cấm xuất khẩu xăng
- ✅ Nhóm Logistics: TMS GMD VTO HAH SCS — TMS rủi ro pháp lý
- ✅ Nhóm Chứng khoán: SSI VCI ORS — VN-Index +28 điểm
- ✅ Nhóm Phòng thủ: VEA

**⚠️ Lưu ý:** v5.1 vẫn giữ nguyên Sections I-V từ v5.0 (ngắn). Xem v5.2 cho full rewrite.

**Files:** `NewsReport/2026/03/News_2026-03-27_0905_v5.0-OPUS.md`

---

## REPORT v5.0-OPUS — 27/03/2026 | ❌ Lỗi: Nội dung ngắn

**📌 Thay đổi:** Nâng cấp pipeline, thêm Phase 1.5 VERIFY, cải thiện verification

**Nguyên nhân:** Bước đầu nâng cấp từ v4.1, áp dụng Phase 1.5 verification framework mới.

**Cải tiến:**
- ✅ Thêm Phase 1.5 VERIFY (fact extraction + confidence scoring)
- ✅ Thêm `fact-extraction-template.md` vào references
- ✅ Thêm verification section vào metadata header
- ✅ Cấu trúc quality gate nâng cấp lên v2.0

**⚠️ Lỗi:** Sections I-V ngắn (~120 dòng) — root cause: viết trước khi fetch đầy đủ.

**Files:** `NewsReport/2026/03/News_2026-03-27_0905_v5.0-OPUS.md`

---

## REPORT v4.1-OPUS — 24/03/2026 14:30

**📌 Thay đổi:** OPUS mode đầu tiên cho ngày 24/03

**Cải tiến:**
- ✅ Nâng cấp lên OPUS mode — deep narrative, second-order effects, scenario analysis
- ✅ Bull/Base/Bear scenario cho VN-Index
- ✅ Contrarian view đầu tiên
- ✅ Trigger levels cụ thể
- ✅ Hook mạnh — "bóng mát toàn cầu"
- ✅ Narrative flow cho 5 sections thay vì liệt kê bullet points

**Điểm nổi bật:** Câu chuyện xuyên suốt về chiến tranh thương mại Mỹ-Trung, dòng tiền dịch chuyển, và cơ hội cho Việt Nam.

**Files:**
- `NewsReport/2026/03/News_2026-03-24_1430_v4.1-OPUS.md`
- `NewsReport/2026/03/News_2026-03-24_1016_v4.1.md` (v4.1 Gemini trước đó)

---

## REPORT v4.1 — 24/03/2026 10:16

**📌 Thay đổi:** Bản Gemini nâng cấp từ v1.0

**Cải tiến:**
- ✅ Bổ sung thêm tin từ Tier 3-4 (Tech/Substack)
- ✅ IEIA framework đầy đủ
- ✅ Tone of voice cải thiện

**Files:** `NewsReport/2026/03/News_2026-03-24_1016_v4.1.md`

---

## REPORT v1.0 — 24/03/2026 06:46

**📌 Thay đổi:** Bản đầu tiên cho ngày 24/03

**Cải tiến:**
- ✅ Pipeline cơ bản: FETCH → INSIGHT → STRATEGY → PUSH
- ✅ RSS feeds từ Vietstock, CafeF, VnExpress
- ✅ 5 sections theo framework

**Files:** `NewsReport/2026/03/News_2026-03-24_0646_v1.0.md`

---

## REPORT v4.0 — 23/03/2026 22:06

**📌 Thay đổi:** Bản đầu tiên cho ngày 23/03

**Cải tiến:**
- ✅ Pipeline: Gemini Scrapper → Sonnet BA/BI → Opus CFA/Editor
- ✅ Hook mạnh — "300 điểm VN-Index đã mất"
- ✅ Narrative flow bài viết xuyên suốt
- ✅ 6 nguồn, 30+ items
- ✅ Key thesis rõ ràng

**Điểm nổi bật:** Báo cáo đầu tiên có đủ cấu trúc OPUS. KEY THESIS: "Xung đột Trung Đông tái định hình dòng chảy tài chính toàn cầu."

**Files:** `NewsReport/2026/03/News_2026-03-23_2206_v4.0.md`

---

## So sánh qua các phiên bản

| Tiêu chí | v4.0 | v4.1 | v4.1-OPUS | v5.0 | v5.1 | v5.2 |
|----------|------|------|-----------|------|------|------|
| Sections | 5 | 5 | 5 + Strategy | 5 | **6** | **6** |
| Facts/section (tb) | 5-8 | 6-10 | 8-12 | 3-5 ❌ | 3-5 ❌ | **10-15 ✅** |
| Nhóm cổ phiếu VN | ❌ | ❌ | ❌ | ❌ | ✅ | ✅ |
| Phase 1.5 VERIFY | ❌ | ❌ | ❌ | ✅ | ✅ | ✅ |
| Scenario Bull/Base/Bear | ❌ | ❌ | ✅ | ✅ | ✅ | ✅ |
| Contrarian View | ❌ | ❌ | ✅ | ✅ | ✅ | ✅ |
| Trigger Levels | ❌ | ❌ | ✅ | ✅ | ✅ | ✅ |
| Total sources | 6 | 10 | 10 | ~212 | ~212 | ~212 |
| Word count ước tính | ~2500 | ~3000 | ~3500 | ~1200 ❌ | ~1500 ❌ | **~4000 ✅** |

---

## Issues đã được fix

| Issue | Phát hiện | Đã fix | Phiên bản |
|-------|-----------|--------|-----------|
| Nội dung ngắn (I-V) | v5.0/v5.1 | ✅ Rewrite toàn bộ | v5.2 |
| Không có nhóm cổ phiếu VN | v4.1 | ✅ Thêm Section VI | v5.1 |
| Không có fact verification | v4.0 | ✅ Phase 1.5 VERIFY | v5.0 |
| Không có OPUS extras | v4.1 | ✅ Scenario + Contrarian | v4.1-OPUS |
| Không có narrative flow | v1.0 | ✅ Hook + flow | v4.0 |

---

## Issues đang open

| Issue | Severity | Trạng thái |
|-------|----------|-----------|
| BBC/CNBC article bodies bị block — nhiều facts [UNVERIFIED] | HIGH | ⚠️ Đang theo dõi |
| Section VI VEA thiếu thông tin doanh nghiệp | MEDIUM | ⚠️ Cần bổ sung từ nguồn chuyên biệt |
| Không fetch được Tier 5 (X/Twitter) | MEDIUM | ⚠️ Cần giải pháp JS rendering |

---

## Roadmap cải tiến

- [ ] Fetch BBC/CNBC article bodies qua alternative method (RSS content extension)
- [ ] Bổ sung Tier 5 X/Twitter qua Agent-based scraping
- [ ] Tạo `fact_extraction.json` tự động cho mỗi phiên bản
- [ ] Cross-reference financial facts tự động với ≥2 nguồn
- [ ] Thêm automated narrative contamination detection
- [ ] Export changelog tự động mỗi phiên bản mới

---

*Changelog được cập nhật lần cuối: 29/03/2026*
*File gốc: `NewsReport/CHANGELOG.md`*.

**📌 Thay đổi lớn:** Rewrite toàn bộ 5 sections đầu — bổ sung Section VI nhóm cổ phiếu VN

**Nguyên nhân:** v5.0/v5.1 báo cáo ngắn bất thường (~120 dòng cho 5 sections). Lỗi root cause: viết report TRƯỚC khi fetch đầy đủ dữ liệu → Sections I-V chỉ có 3-5 facts, Section VI (viết sau khi đọc data) mới đầy đủ.

**Cải tiến:**
- ✅ Mở rộng Section I (Vĩ mô): 11 facts thay vì 3 (thêm ADB ASEAN -2.3% GDP, ECB tăng lãi suất, Trung Quốc điều tra Mỹ, Ấn Độ mua dầu Iran, Global Forecast 4.2% inflation, Zimbabwe 15 công dân chết...)
- ✅ Mở rộng Section II (VN Market): 12 facts thay vì 3 (thêm VN-Index tăng 28 điểm, bluechip áp lực, FLC 150K tỷ, Novaland hoàn nhập 3.3K tỷ, FDI tăng 200%, Gia Lai 800K tỷ...)
- ✅ Mở rộng Section III (Chính sách): 12 facts thay vì 3 (thêm 3 loại thuế về 0%, Nga cấm xuất khẩu xăng, kho dự trữ Nghi Sơn, thuế quan Mỹ 3 kịch bản...)
- ✅ Mở rộng Section IV (Tech/AI): 14 facts thay vì 3 (thêm Meta -8%, Anthropic thắng kiện...)
- ✅ Mở rộng Section V (Chiến lược): Đầy đủ X-RADAR, Scenario, Contrarian, Trigger Levels
- ✅ Thêm Section VI hoàn chỉnh: 6 nhóm cổ phiếu VN với khuyến nghị cụ thể từng mã

**Files:** `NewsReport/2026/03/News_2026-03-27_0905_v5.2-OPUS.md`

---

## REPORT v5.1-OPUS — 29/03/2026

**📌 Thay đổi:** Bổ sung Section VI — 6 nhóm cổ phiếu VN

**Nguyên nhân:** User yêu cầu thêm nhóm cổ phiếu VN để phục vụ phân tích danh mục.

**Cải tiến:**
- ✅ Thêm **Section VI: Nhóm cổ phiếu VN** — 6 nhóm theo ngành
- ✅ Nhóm Ngân hàng: VCB TPB VIB MBB ACB — VPBank 100K tỷ, VIB Max Card, BIDV bán nợ xấu
- ✅ Nhóm Tiêu dùng: SAB VNM QNS MSN — MWG cổ tức 3K tỷ
- ✅ Nhóm Dầu khí & NL: GAS PVD BSR REE — VinEnergo 8 tỷ USD, Nga cấm xuất khẩu xăng
- ✅ Nhóm Logistics: TMS GMD VTO HAH SCS — TMS rủi ro pháp lý
- ✅ Nhóm Chứng khoán: SSI VCI ORS — VN-Index +28 điểm
- ✅ Nhóm Phòng thủ: VEA

**⚠️ Lưu ý:** v5.1 vẫn giữ nguyên Sections I-V từ v5.0 (ngắn). Xem v5.2 cho full rewrite.

**Files:** `NewsReport/2026/03/News_2026-03-27_0905_v5.0-OPUS.md`

---

## REPORT v5.0-OPUS — 27/03/2026 | ❌ Lỗi: Nội dung ngắn

**📌 Thay đổi:** Nâng cấp pipeline, thêm Phase 1.5 VERIFY, cải thiện verification

**Nguyên nhân:** Bước đầu nâng cấp từ v4.1, áp dụng Phase 1.5 verification framework mới.

**Cải tiến:**
- ✅ Thêm Phase 1.5 VERIFY (fact extraction + confidence scoring)
- ✅ Thêm `fact-extraction-template.md` vào references
- ✅ Thêm verification section vào metadata header
- ✅ Cấu trúc quality gate nâng cấp lên v2.0

**⚠️ Lỗi:** Sections I-V ngắn (~120 dòng) — root cause: viết trước khi fetch đầy đủ.

**Files:** `NewsReport/2026/03/News_2026-03-27_0905_v5.0-OPUS.md`

---

## REPORT v4.1-OPUS — 24/03/2026 14:30

**📌 Thay đổi:** OPUS mode đầu tiên cho ngày 24/03

**Cải tiến:**
- ✅ Nâng cấp lên OPUS mode — deep narrative, second-order effects, scenario analysis
- ✅ Bull/Base/Bear scenario cho VN-Index
- ✅ Contrarian view đầu tiên
- ✅ Trigger levels cụ thể
- ✅ Hook mạnh — "bóng mát toàn cầu"
- ✅ Narrative flow cho 5 sections thay vì liệt kê bullet points

**Điểm nổi bật:** Câu chuyện xuyên suốt về chiến tranh thương mại Mỹ-Trung, dòng tiền dịch chuyển, và cơ hội cho Việt Nam.

**Files:**
- `NewsReport/2026/03/News_2026-03-24_1430_v4.1-OPUS.md`
- `NewsReport/2026/03/News_2026-03-24_1016_v4.1.md` (v4.1 Gemini trước đó)

---

## REPORT v4.1 — 24/03/2026 10:16

**📌 Thay đổi:** Bản Gemini nâng cấp từ v1.0

**Cải tiến:**
- ✅ Bổ sung thêm tin từ Tier 3-4 (Tech/Substack)
- ✅ IEIA framework đầy đủ
- ✅ Tone of voice cải thiện

**Files:** `NewsReport/2026/03/News_2026-03-24_1016_v4.1.md`

---

## REPORT v1.0 — 24/03/2026 06:46

**📌 Thay đổi:** Bản đầu tiên cho ngày 24/03

**Cải tiến:**
- ✅ Pipeline cơ bản: FETCH → INSIGHT → STRATEGY → PUSH
- ✅ RSS feeds từ Vietstock, CafeF, VnExpress
- ✅ 5 sections theo framework

**Files:** `NewsReport/2026/03/News_2026-03-24_0646_v1.0.md`

---

## REPORT v4.0 — 23/03/2026 22:06

**📌 Thay đổi:** Bản đầu tiên cho ngày 23/03

**Cải tiến:**
- ✅ Pipeline: Gemini Scrapper → Sonnet BA/BI → Opus CFA/Editor
- ✅ Hook mạnh — "300 điểm VN-Index đã mất"
- ✅ Narrative flow bài viết xuyên suốt
- ✅ 6 nguồn, 30+ items
- ✅ Key thesis rõ ràng

**Điểm nổi bật:** Báo cáo đầu tiên có đủ cấu trúc OPUS. KEY THESIS: "Xung đột Trung Đông tái định hình dòng chảy tài chính toàn cầu."

**Files:** `NewsReport/2026/03/News_2026-03-23_2206_v4.0.md`

---

## So sánh qua các phiên bản

| Tiêu chí | v4.0 | v4.1 | v4.1-OPUS | v5.0 | v5.1 | v5.2 |
|----------|------|------|-----------|------|------|------|
| Sections | 5 | 5 | 5 + Strategy | 5 | **6** | **6** |
| Facts/section (tb) | 5-8 | 6-10 | 8-12 | 3-5 ❌ | 3-5 ❌ | **10-15 ✅** |
| Nhóm cổ phiếu VN | ❌ | ❌ | ❌ | ❌ | ✅ | ✅ |
| Phase 1.5 VERIFY | ❌ | ❌ | ❌ | ✅ | ✅ | ✅ |
| Scenario Bull/Base/Bear | ❌ | ❌ | ✅ | ✅ | ✅ | ✅ |
| Contrarian View | ❌ | ❌ | ✅ | ✅ | ✅ | ✅ |
| Trigger Levels | ❌ | ❌ | ✅ | ✅ | ✅ | ✅ |
| Total sources | 6 | 10 | 10 | ~212 | ~212 | ~212 |
| Word count ước tính | ~2500 | ~3000 | ~3500 | ~1200 ❌ | ~1500 ❌ | **~4000 ✅** |

---

## Issues đã được fix

| Issue | Phát hiện | Đã fix | Phiên bản |
|-------|-----------|--------|-----------|
| Nội dung ngắn (I-V) | v5.0/v5.1 | ✅ Rewrite toàn bộ | v5.2 |
| Không có nhóm cổ phiếu VN | v4.1 | ✅ Thêm Section VI | v5.1 |
| Không có fact verification | v4.0 | ✅ Phase 1.5 VERIFY | v5.0 |
| Không có OPUS extras | v4.1 | ✅ Scenario + Contrarian | v4.1-OPUS |
| Không có narrative flow | v1.0 | ✅ Hook + flow | v4.0 |

---

## Issues đang open

| Issue | Severity | Trạng thái |
|-------|----------|-----------|
| BBC/CNBC article bodies bị block — nhiều facts [UNVERIFIED] | HIGH | ⚠️ Đang theo dõi |
| Section VI VEA thiếu thông tin doanh nghiệp | MEDIUM | ⚠️ Cần bổ sung từ nguồn chuyên biệt |
| Không fetch được Tier 5 (X/Twitter) | MEDIUM | ⚠️ Cần giải pháp JS rendering |

---

## Roadmap cải tiến

- [ ] Fetch BBC/CNBC article bodies qua alternative method (RSS content extension)
- [ ] Bổ sung Tier 5 X/Twitter qua Agent-based scraping
- [ ] Tạo `fact_extraction.json` tự động cho mỗi phiên bản
- [ ] Cross-reference financial facts tự động với ≥2 nguồn
- [ ] Thêm automated narrative contamination detection
- [ ] Export changelog tự động mỗi phiên bản mới

---

*Changelog được cập nhật lần cuối: 29/03/2026*
*File gốc: `NewsReport/CHANGELOG.md`*.

```
FETCH (WebFetch RSS feeds)
    ↓
VERIFY (Phase 1.5 — confidence scoring)
    ↓
INSIGHT (FACT/VIEW + IEIA)
    ↓
STRATEGY (Thesis + Scenario + Trigger Levels)
    ↓
PUSH (GitHub: main branch)
```

---

## Phiên bản chi tiết

### v5.2-OPUS — 29/03/2026 | ✅ Phiên bản hiện tại

**📌 Thay đổi lớn:** Rewrite toàn bộ 5 sections đầu — bổ sung Section VI nhóm cổ phiếu VN

**Nguyên nhân:** v5.0/v5.1 báo cáo ngắn bất thường (~120 dòng cho 5 sections). Lỗi root cause: viết report TRƯỚC khi fetch đầy đủ dữ liệu → Sections I-V chỉ có 3-5 facts, Section VI (viết sau khi đọc data) mới đầy đủ.

**Cải tiến:**
- ✅ Mở rộng Section I (Vĩ mô): 11 facts thay vì 3 (thêm ADB ASEAN -2.3% GDP, ECB tăng lãi suất, Trung Quốc điều tra Mỹ, Ấn Độ mua dầu Iran, Global Forecast 4.2% inflation, Zimbabwe 15 công dân chết...)
- ✅ Mở rộng Section II (VN Market): 12 facts thay vì 3 (thêm VN-Index tăng 28 điểm, bluechip áp lực, FLC 150K tỷ, Novaland hoàn nhập 3.3K tỷ, FDI tăng 200%, Gia Lai 800K tỷ...)
- ✅ Mở rộng Section III (Chính sách): 12 facts thay vì 3 (thêm 3 loại thuế về 0%, Nga cấm xuất khẩu xăng, kho dự trữ Nghi Sơn, thuế quan Mỹ 3 kịch bản, Luật Đô thị đặc biệt TP.HCM...)
- ✅ Mở rộng Section IV (Tech/AI): 14 facts thay vì 3 (thêm Meta -8%, Anthropic thắng kiện, AI bot vượt người thật, China AI refueling, Samsung Z Fold8, Xiaomi 17 Ultra, Apple Music Vietnam concert, Drop Hi-Fi shutdown...)
- ✅ Mở rộng Section V (Chiến lược): Đầy đủ X-RADAR table, Bull/Base/Bear scenario, Contrarian View, Trigger Levels
- ✅ Thêm Section VI hoàn chỉnh: 6 nhóm cổ phiếu VN với facts, rủi ro, khuyến nghị từng mã

**Files:** `NewsReport/2026/03/News_2026-03-27_0905_v5.2-OPUS.md`
**Sources:** Vietstock (76) + VnExpress (60) + CNBC (30) + Tinhte (20) + BBC (26) = ~212 items total

---

### v5.1-OPUS — 29/03/2026

**📌 Thay đổi:** Bổ sung Section VI — 6 nhóm cổ phiếu VN

**Nguyên nhân:** User yêu cầu thêm nhóm cổ phiếu VN để phục vụ phân tích danh mục.

**Cải tiến:**
- ✅ Thêm **Section VI: Nhóm cổ phiếu VN** — 6 nhóm theo ngành
- ✅ Nhóm Ngân hàng: VCB TPB VIB MBB ACB — VPBank 100K tỷ, VIB Max Card, BIDV bán nợ xấu
- ✅ Nhóm Tiêu dùng: SAB VNM QNS MSN — MWG cổ tức 3K tỷ
- ✅ Nhóm Dầu khí & NL: GAS PVD BSR REE — VinEnergo 8 tỷ USD, Nga cấm xuất khẩu xăng
- ✅ Nhóm Logistics: TMS GMD VTO HAH SCS — TMS rủi ro pháp lý
- ✅ Nhóm Chứng khoán: SSI VCI ORS — VN-Index +28 điểm
- ✅ Nhóm Phòng thủ: VEA

**⚠️ Lưu ý:** v5.1 vẫn giữ nguyên Sections I-V từ v5.0 (ngắn). Xem v5.2 cho full rewrite.

**Files:** `NewsReport/2026/03/News_2026-03-27_0905_v5.0-OPUS.md` (đổi tên thành v5.1)

---

### v5.0-OPUS — 27/03/2026 | ❌ Lỗi: Nội dung ngắn

**📌 Thay đổi:** Nâng cấp pipeline, thêm Phase 1.5 VERIFY, cải thiện verification

**Nguyên nhân:** Bước đầu nâng cấp từ v4.1, áp dụng Phase 1.5 verification framework mới.

**Cải tiến:**
- ✅ Thêm Phase 1.5 VERIFY (fact extraction + confidence scoring)
- ✅ Thêm `fact-extraction-template.md` vào references
- ✅ Thêm verification section vào metadata header
- ✅ Cấu trúc quality gate nâng cấp lên v2.0

**⚠️ Lỗi:** Sections I-V ngắn (~120 dòng) — root cause: viết trước khi fetch đầy đủ.

**Files:** `NewsReport/2026/03/News_2026-03-27_0905_v5.0-OPUS.md`

---

### v4.1-OPUS — 24/03/2026 14:30

**📌 Thay đổi:** OPUS mode đầu tiên cho ngày 24/03

**Cải tiến:**
- ✅ Nâng cấp lên OPUS mode — deep narrative, second-order effects, scenario analysis
- ✅ Bull/Base/Bear scenario cho VN-Index
- ✅ Contrarian view đầu tiên
- ✅ Trigger levels cụ thể
- ✅ Hook mạnh — "bóng mát toàn cầu"
- ✅ Narrative flow cho 5 sections thay vì liệt kê bullet points

**Điểm nổi bật:** Câu chuyện xuyên suốt về chiến tranh thương mại Mỹ-Trung, dòng tiền dịch chuyển, và cơ hội cho Việt Nam.

**Files:**
- `NewsReport/2026/03/News_2026-03-24_1430_v4.1-OPUS.md`
- `NewsReport/2026/03/News_2026-03-24_1016_v4.1.md` (v4.1 Gemini trước đó)

---

### v4.1 — 24/03/2026 10:16

**📌 Thay đổi:** Bản Gemini nâng cấp từ v1.0

**Cải tiến:**
- ✅ Bổ sung thêm tin từ Tier 3-4 (Tech/Substack)
- ✅ IEIA framework đầy đủ
- ✅ Tone of voice cải thiện

**Files:** `NewsReport/2026/03/News_2026-03-24_1016_v4.1.md`

---

### v1.0 — 24/03/2026 06:46

**📌 Thay đổi:** Bản đầu tiên cho ngày 24/03

**Cải tiến:**
- ✅ Pipeline cơ bản: FETCH → INSIGHT → STRATEGY → PUSH
- ✅ RSS feeds từ Vietstock, CafeF, VnExpress
- ✅ 5 sections theo framework

**Files:** `NewsReport/2026/03/News_2026-03-24_0646_v1.0.md`

---

### v4.0 — 23/03/2026 22:06

**📌 Thay đổi:** Bản đầu tiên cho ngày 23/03

**Cải tiến:**
- ✅ Pipeline: Gemini Scrapper → Sonnet BA/BI → Opus CFA/Editor
- ✅ Hook mạnh — "300 điểm VN-Index đã mất"
- ✅ Narrative flow bài viết xuyên suốt
- ✅ 6 nguồn, 30+ items
- ✅ Key thesis rõ ràng

**Điểm nổi bật:** Báo cáo đầu tiên có đủ cấu trúc OPUS. KEY THESIS: "Xung đột Trung Đông tái định hình dòng chảy tài chính toàn cầu."

**Files:** `NewsReport/2026/03/News_2026-03-23_2206_v4.0.md`

---

## So sánh qua các phiên bản

| Tiêu chí | v4.0 | v4.1 | v4.1-OPUS | v5.0 | v5.1 | v5.2 |
|----------|------|------|-----------|------|------|------|
| Sections | 5 | 5 | 5 + Strategy | 5 | **6** | **6** |
| Facts/section (tb) | 5-8 | 6-10 | 8-12 | 3-5 ❌ | 3-5 ❌ | **10-15 ✅** |
| Nhóm cổ phiếu VN | ❌ | ❌ | ❌ | ❌ | ✅ | ✅ |
| Phase 1.5 VERIFY | ❌ | ❌ | ❌ | ✅ | ✅ | ✅ |
| Scenario Bull/Base/Bear | ❌ | ❌ | ✅ | ✅ | ✅ | ✅ |
| Contrarian View | ❌ | ❌ | ✅ | ✅ | ✅ | ✅ |
| Trigger Levels | ❌ | ❌ | ✅ | ✅ | ✅ | ✅ |
| Total sources | 6 | 10 | 10 | ~212 | ~212 | ~212 |
| Word count ước tính | ~2500 | ~3000 | ~3500 | ~1200 ❌ | ~1500 ❌ | **~4000 ✅** |

---

## Issues đã được fix

| Issue | Phát hiện | Đã fix | Phiên bản |
|-------|-----------|--------|-----------|
| Nội dung ngắn (I-V) | v5.0/v5.1 | ✅ Rewrite toàn bộ | v5.2 |
| Không có nhóm cổ phiếu VN | v4.1 | ✅ Thêm Section VI | v5.1 |
| Không có fact verification | v4.0 | ✅ Phase 1.5 VERIFY | v5.0 |
| Không có OPUS extras | v4.1 | ✅ Scenario + Contrarian | v4.1-OPUS |
| Không có narrative flow | v1.0 | ✅ Hook + flow | v4.0 |

---

## Issues đang open

| Issue | Severity | Trạng thái |
|-------|----------|-----------|
| BBC/CNBC article bodies bị block — nhiều facts [UNVERIFIED] | HIGH | ⚠️ Đang theo dõi |
| Section VI VEA thiếu thông tin doanh nghiệp | MEDIUM | ⚠️ Cần bổ sung từ nguồn chuyên biệt |
| Không fetch được Tier 5 (X/Twitter) | MEDIUM | ⚠️ Cần giải pháp JS rendering |

---

## Roadmap cải tiến

- [ ] Fetch BBC/CNBC article bodies qua alternative method (RSS content extension)
- [ ] Bổ sung Tier 5 X/Twitter qua Agent-based scraping
- [ ] Tạo `fact_extraction.json` tự động cho mỗi phiên bản
- [ ] Cross-reference financial facts tự động với ≥2 nguồn
- [ ] Thêm automated narrative contamination detection
- [ ] Export changelog tự động mỗi phiên bản mới

---

*Changelog được cập nhật lần cuối: 29/03/2026*
*File gốc: `NewsReport/CHANGELOG.md`*
