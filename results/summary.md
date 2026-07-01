# Travala QC Run — Summary

**Date run:** 2026-07-01
**Target:** https://www.travala.com (read-only exploratory/functional testing)
**Executed by:** Cowork + Claude in Chrome
**Scope:** 10 canonical test cases, run one at a time.

| TC-ID | Title | Overall result | Date run |
|-------|-------|----------------|----------|
| TC-001 | Homepage loads with primary search form visible | Pass | 2026-07-01 |
| TC-006 | Destination autocomplete returns categorized suggestions | Pass | 2026-07-01 |
| TC-007 | Selecting a destination auto-opens the date picker | Pass | 2026-07-01 |
| TC-009 | Selecting check-in / check-out dates | Blocked (stale test data) | 2026-07-01 |
| TC-010 | Search submit navigates to results with correct URL params | Partial | 2026-07-01 |
| TC-017 | Search results update when changing location | Pass | 2026-07-01 |
| TC-022 | Discount badge math vs strikethrough/actual (Apricot Hotel) | Pass | 2026-07-01 |
| TC-023 | Search "from" price matches lowest package on detail page | **Fail** | 2026-07-01 |
| TC-034 | Packages display when applying a room-type (Twin) filter | Pass | 2026-07-01 |
| TC-037 | Customer Information form layout & right-side summary | Partial | 2026-07-01 |

## Tally
- **Pass:** 6 (TC-001, TC-006, TC-007, TC-017, TC-022, TC-034)
- **Partial:** 2 (TC-010, TC-037)
- **Fail:** 1 (TC-023)
- **Blocked:** 1 (TC-009)

## Key findings worth attention
- **TC-023 (Fail):** For *InterContinental Hanoi Westlake by IHG*, the search-results
  "from" price was **US$189.38** while the detail page's genuinely lowest package
  (and its own header "from") was **US$181.40** — the search card overstates the
  cheapest price by **US$7.98**.
- **TC-009 (Blocked):** The test's target dates (13–14 Jun 2026) are in the past
  relative to the run date. The calendar correctly refuses to navigate before the
  current month, so the dates are unreachable. Test data needs refreshing to future
  dates. This blocker cascades into **TC-010 (Partial)**, which was run with valid
  dates (08–09 Jul 2026); its structure passed but the exact stale-date param
  assertion could not be tested.
- **TC-037 (Partial):** The Customer Information page is correct except that the
  right-side summary had **no "BEST PRICE" box** — the booked package (not the
  cheapest) showed a "27% OFF TODAY" badge instead. Recommend clarifying whether
  the checkpoint expects "BEST PRICE" on every summary or only for the lowest-price
  package.
- **TC-006 (Pass, with note):** Fuzzy "Hanover" matches still appear in Hanoi
  autocomplete but are now ranked **below** all genuine Hanoi entries (improved vs
  the historical BUG-007); recorded for review, not failed.

## Cross-cutting notes
- **Screenshots:** This session could not persist browser screenshots to disk, so
  each per-test results file records detailed observed-state text plus in-session
  screenshot IDs in lieu of image files; the per-test screenshot folders are empty.
- **Fresh-session precondition:** The form loaded pre-populated (Hanoi + dates)
  from prior local storage, so the session was not perfectly fresh. This did not
  affect any checkpoint outcome.
- **TC-017 target:** Used Bangkok as the flagged placeholder destination (source
  did not specify one).
- No logins, payments, bookings, or form submissions were performed at any point.
