# Test case template

Copy this file for every new test case. Keep one test case per file so Cowork
can run, re-run, and report on them independently.

File naming convention: `TC-<3 digit number>_<short-slug>.md`
Example: `TC-001_homepage-search-hanoi.md`

---

## Test case metadata

- **ID:** TC-006
- **Title:** Homepage search suggestions — Hanoi
- **URL under test:** http://travala.com
- **Priority:** Medium
- **Owner:** Mya

## Preconditions

- Fresh browser session (no existing search text, logged out unless the test says otherwise)

## Steps

Each step is either an **action** (something Claude does) or a **checkpoint**
(a expected result Claude must verify and report pass/fail on). Numbering
stays flat — checkpoints are tagged inline so nothing gets missed.

1. **[ACTION]** Navigate to `http://travala.com`.
2. **[CHECKPOINT]** Confirm the following are true:
   - The search bar is visible
   - The "Stays" tab is active
3. **[ACTION]** If a login popup appears, click its close button. If no popup appears, skip this step.
4. **[ACTION]** Click the search bar. If it already contains text, clear it.
5. **[ACTION]** Type `Hanoi` into the search bar.
6. **[ACTION]** Wait 3 seconds.
7. **[ACTION]** Retrieve the full list of search suggestions shown in the dropdown.
8. **[CHECKPOINT]** Confirm the following are true:
   - The top suggestion is "Hanoi - Vietnam" with a "City" badge
   - Every other suggestion in the list is relevant to "Hanoi" and carries one of these category badges: City / Neighborhood / Airport / Train station / Property

## Reporting requirements

For every checkpoint, Cowork should record:
- Pass / Fail
- What was actually observed (exact text, badge labels, screenshot reference)
- A screenshot saved to `results/<TC-ID>/<step-number>.png`

## Notes
Nothing for now