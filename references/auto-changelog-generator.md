# Auto-Changelog Generator v2.0

> **Mục đích:** Tự động tạo và cập nhật CHANGELOG mỗi khi có phiên bản mới
> **Trigger:** Sau khi push file báo cáo lên Git (Phase 4 PUSH)
> **Output:** Cập nhật `NewsReport/CHANGELOG.md`

---

## Chạy tự động sau mỗi phiên bản

### Bước 1: Extract Metadata từ file báo cáo

```yaml
# Đọc header YAML metadata từ file báo cáo mới nhất
Input: News_2026-03-29_1200_v5.2-OPUS.md

Extract:
  version:     v5.2-OPUS
  date:        2026-03-29
  mode:        OPUS
  sources:     Vietstock (76) + VnExpress (60) + CNBC (30) + Tinhte (20) + BBC (26)
  word_count:  ~4000
  sections:     6
  verification: [UNVERIFIED] tags count
```

### Bước 2: So sánh với phiên bản trước

```yaml
Compare v5.2 vs v5.1:
  Added:
    - Section VI (VN stock groups) expanded with 12 new facts
    - Word count increased from ~1500 to ~4000
    - Section I: 3 → 11 facts
    - Section II: 3 → 12 facts
    - Section III: 3 → 12 facts
    - Section IV: 3 → 14 facts
    - Narrative flow improved (liệt kê → essay)
    - 212 total sources fetched (vs ~30 before)

  Fixed:
    - [BUG] Sections I-V were too short (3 facts each) — root cause: write before fetch
    - [BUG] Narrative contamination: "Iran" actor incorrectly attributed

  Removed:
    - Section VI placeholder from v5.1 (full rewrite)
```

### Bước 3: Generate Changelog Entry

```markdown
### v5.2-OPUS — 29/03/2026 | ✅ Phiên bản hiện tại

**📌 Thay đổi lớn:** Rewrite toàn bộ 5 sections đầu

**Nguyên nhân:** v5.0/v5.1 báo cáo ngắn bất thường (~120 dòng cho 5 sections).
Lỗi root cause: viết report TRƯỚC khi fetch đầy đủ dữ liệu.

**Cải tiến:**
- ✅ Mở rộng Section I: 3 → 11 facts (thêm ADB ASEAN -2.3% GDP, ECB tăng lãi suất...)
- ✅ Mở rộng Section II: 3 → 12 facts (VN-Index +28 điểm, FLC 150K tỷ...)
- ✅ Mở rộng Section III: 3 → 12 facts (3 loại thuế về 0%, Nga cấm xuất khẩu xăng...)
- ✅ Mở rộng Section IV: 3 → 14 facts (Meta -8%, Anthropic thắng kiện...)
- ✅ Mở rộng Section V: Đầy đủ X-RADAR + Bull/Base/Bear + Contrarian View
- ✅ Mở rộng Section VI: 12 facts mới

**Issues Fixed:**
- ✅ [BUG] Sections I-V ngắn bất thường
- ✅ [BUG] Iran actor swap contamination

**Issues Open:**
- ⚠️ BBC/CNBC article bodies blocked
- ⚠️ VEA thiếu thông tin

**Files:** `NewsReport/2026/03/News_2026-03-29_1200_v5.2-OPUS.md`
**Sources:** ~212 items (Vietstock + VnExpress + CNBC + Tinhte + BBC)
```

### Bước 4: Cập nhật CHANGELOG.md

```bash
# Tự động chạy sau git commit

# 1. Đọc version mới nhất
NEW_VERSION=$(ls NewsReport/2026/03/*.md -t | head -1)

# 2. Extract version number
VERSION=$(grep "^version:" "$NEW_VERSION" | cut -d: -f2 | tr -d ' ')

# 3. Tạo changelog entry
python3 << 'PYEOF'
import re
from datetime import datetime

new_file = "NewsReport/2026/03/News_2026-03-29_1200_v5.2-OPUS.md"
changelog = "NewsReport/CHANGELOG.md"

# Extract metadata
with open(new_file) as f:
    content = f.read()

version = re.search(r'version:\s*(v[\d.]+)', content)
date = re.search(r'date:\s*(\d{4}-\d{2}-\d{2})', content)
sources = re.search(r'sources_count:\s*(.+)', content)
word_count = re.search(r'word_count:\s*(.+)', content)

# Generate entry
entry = f"""
### {version.group(1)} — {date.group(1)}

**Ngày tạo:** {datetime.now().strftime('%d/%m/%Y %H:%M')}

[Auto-generated entry — details to be filled by operator]

**Files:** `{new_file}`
"""

# Prepend to changelog
with open(changelog) as f:
    old = f.read()

with open(changelog, 'w') as f:
    f.write(entry + "\n" + old)

print(f"✅ Changelog updated for {version.group(1)}")
PYEOF

# 5. Git add + commit
git add NewsReport/CHANGELOG.md
git commit -m "docs: auto-update CHANGELOG for v${VERSION}"
git push
```

---

## Structured Changelog Schema

```json
{
  "changelog_schema_version": "2.0",
  "reports": {
    "2026-03": {
      "versions": [
        {
          "version": "v5.2-OPUS",
          "date": "2026-03-29",
          "time": "12:00",
          "mode": "OPUS",
          "changes": {
            "added": ["Section VI VN stocks", "212 sources fetched", "Full rewrite"],
            "fixed": ["Short content bug", "Iran actor swap"],
            "removed": []
          },
          "issues": {
            "fixed": ["[BUG] Short sections I-V", "[BUG] Iran contamination"],
            "open": ["BBC/CNBC blocked", "VEA data missing"]
          },
          "metrics": {
            "facts_total": 72,
            "facts_per_section_avg": 12,
            "sources_count": 212,
            "word_count": 4000,
            "verification_rate": "78%"
          },
          "files": ["NewsReport/2026/03/News_2026-03-29_1200_v5.2-OPUS.md"],
          "git_commit": "e4ad669"
        }
      ]
    }
  }
}
```

---

## Git Hook: Auto-run on commit message pattern

```bash
#!/bin/bash
# .git/hooks/post-commit
# Tự động update CHANGELOG khi commit message chứa "Daily Briefing"

COMMIT_MSG=$(git log -1 --pretty=%B)

if [[ "$COMMIT_MSG" == *"Daily Briefing"* ]]; then
    echo "📋 Auto-updating CHANGELOG..."
    # Chạy script cập nhật changelog
fi
```

---

## So sánh tự động với phiên bản trước

```python
def compare_versions(new_file, prev_file):
    """So sánh 2 phiên bản và tạo diff summary"""

    new_words = count_words(new_file)
    prev_words = count_words(prev_file)

    new_facts = count_facts(new_file)
    prev_facts = count_facts(prev_file)

    new_sources = extract_sources(new_file)
    prev_sources = extract_sources(prev_file)

    changes = {
        "word_count_delta": new_words - prev_words,
        "fact_count_delta": new_facts - prev_facts,
        "new_sources_added": list(set(new_sources) - set(prev_sources)),
        "sources_removed": list(set(prev_sources) - set(new_sources)),
        "sections_added": detect_new_sections(new_file) - detect_new_sections(prev_file),
        "issues_fixed": detect_fixed_issues(new_file),
        "issues_new": detect_new_issues(new_file)
    }

    return changes
```

---

## Quality Metrics Dashboard

```
Version Comparison Dashboard (auto-generated):

| Version | Date | Facts/section | Words | Sources | Verification Rate |
|---------|------|-------------|-------|---------|----------------|
| v5.2    | 29/3 | 12.0 avg | ~4000 | 212 | 78% |
| v5.1    | 29/3 | 3.0 ❌   | ~1500 | 212 | 60% |
| v5.0    | 27/3 | 3.0 ❌   | ~1200 | ~212 | 60% |
| v4.1    | 24/3 | 8.0     | ~3000 | 10 | N/A |
| v4.0    | 23/3 | 5-8     | ~2500 | 6 | N/A |
```

---

## Operator Checklist (trước khi commit)

```
□ Đã tạo changelog entry cho phiên bản mới?
□ Đã so sánh với phiên bản trước?
□ Đã ghi rõ: nguyên nhân thay đổi, cải tiến, issues fixed/open?
□ Đã cập nhật comparison table?
□ Đã push changelog cùng báo cáo?
□ File schema version: 2.0 (latest)?
```

---

## Lưu ý

- Changelog template được auto-generate, nhưng **phần "Nguyên nhân" và "Cải tiến"** cần operator điền thủ công vì không thể auto-detect ý nghĩa business
- Script chỉ điền: version, date, file path, metrics numbers
- Operator điền: narrative change description
