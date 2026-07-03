# Travala Hotel — Agent-Executable Test Cases

Converted from "Autotest pilot - Sheet2". Shared product behavior lives in `../knowledge_base/travala-common-behavior.md` (cited as KB §n). Priority omitted — not present in source.

| File | Source TC | Area | Notes |
|---|---|---|---|
| TC-001.md | TC-001 | Homepage load | — |
| TC-006.md | TC-006 | Autocomplete relevance | Expected to catch BUG-007 |
| TC-007.md | TC-007 | Cascade: destination → date picker | Depends on TC-006 setup |
| TC-009.md | TC-009 | Date selection | ⚠️ Source dates (Jun 2026) now past — parameterized to future Sat→Sun |
| TC-010.md | TC-010 | Search submit / URL params | Depends on TC-009 setup |
| TC-017.md | TC-017 | Re-search with new location | Source was high-level; interpretation flagged [ASSUMED] |
| TC-022.md | TC-022 | Discount badge math | Source "Pass" looks mislabeled — actual shows 88.4% vs 52% badge |
| TC-023.md | TC-023 | Card vs detail "from" price | Expected to catch BUG-002 |
| TC-034.md | TC-034 | Twin package filter | Source was high-level; interpretation flagged [ASSUMED] |
| TC-037.md | TC-037 | Booking step 1 layout | Setup path to /booking added [ASSUMED]; never enters payment |

Conventions: steps tagged [ACTION]/[CHECKPOINT]; anything not in the source is tagged [ASSUMED] with a reason; condition-based waits with fixed-time fallbacks per KB §8.
