# QC run summary — travala.com

- **Date run:** 2026-07-03
- **Runner:** Claude in Chrome (desktop, logged out, USD), per `prompts/prompt__master.md` + `knowledge_base/travala_com.md`
- **Requested scope:** TC-001 to TC-009. Only TC-001, TC-006, TC-007, TC-009 exist in `test_cases/` (TC-002–TC-005, TC-008 not present — not run).

| ID | Title | Overall result | Date run |
|---|---|---|---|
| TC-001 | Homepage loads with primary search form visible | ✅ Pass | 2026-07-03 |
| TC-006 | Destination autocomplete returns relevant categorized suggestions | ⚠️ Partial — CP8 relevance Fail (BUG-007 reproduced) | 2026-07-03 |
| TC-007 | Selecting a destination auto-opens the date picker | ✅ Pass | 2026-07-03 |
| TC-009 | Selecting check-in and check-out dates | ✅ Pass | 2026-07-03 |

## Key findings

1. **BUG-007 reproduced (TC-006 CP8):** query "Hanoi" returns three "Hanover" (USA/Canada) fuzzy matches ranked above Hanoi-Vietnam Property entries, plus the lowercase "hanoi, Bogotá, CO" Property. Order matches the KB fixture exactly — stable behavior, not a flake.
2. **Test-case wording gaps (suggested edits):**
   - TC-006 CP7 badge list omits `City and vicinity` (present in KB §3 taxonomy and observed on the top suggestion) — passed with note.
   - TC-006 CP8 criterion (b) ("fuzzy matches excluded") conflicts with KB §3 ("fuzzy allowed, demoted") — failed per the test case as written; team should pick one interpretation.
3. Date format on site zero-pads days ("08 Aug 2026"); summary-string separator is a plain hyphen. Both accepted as matching the expected patterns.

## Run deviations

- Fresh incognito session unavailable in Claude in Chrome; ran in a new tab of the existing profile. Prior-session carry-over (pre-filled destination, remembered dates) occurred and was handled per KB §2.
- No cookie banner or signup modal appeared during the run (defensive steps skipped as "did not apply").
- Screenshot evidence is embedded in the Cowork session run log (IDs referenced per checkpoint in each results file); the browser tooling did not export image files, so no `results/<TC-ID>_<date>/` screenshot folders were created.
