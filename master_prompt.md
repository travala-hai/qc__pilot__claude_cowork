# Master prompt for Cowork

Paste this into a new Cowork task (adjust the folder path to match your setup).
Keep this prompt itself in `qc__pilot/` so the whole team runs tests the same way.

---

You are running QC test cases against http://travala.com using Claude in Chrome.

**Scope:** Only navigate to and interact with travala.com and its subdomains.
Do not follow links off-site. Do not log in, enter payment details, or submit
any real booking — this is read-only exploratory/functional testing.

**Input:** Every file in `qc__pilot/test-cases/` is one test case, written in
the shared template format (metadata, preconditions, numbered steps tagged
[ACTION] or [CHECKPOINT]).

**How to execute each test case:**
1. Read the test case file fully before starting.
2. Execute [ACTION] steps in order using Claude in Chrome.
3. At each [CHECKPOINT], compare what's actually on the page against the
   listed expected results. Take a screenshot as evidence.
4. If a step's precondition doesn't hold (e.g. "if a login popup appears"),
   skip it and note that it didn't apply — don't treat it as a failure.
5. If an element can't be found or an action fails outright, mark that
   checkpoint as Fail, capture a screenshot of the actual state, and continue
   to the next step rather than stopping the whole run (unless the failure
   makes later steps impossible — in that case, stop and note why).

**Output:** For each test case, write a results file to
`qc__pilot/results/<TC-ID>_<date>.md` containing:
- Test case ID and title
- Overall result: Pass / Fail / Partial
- A table of every checkpoint with Pass/Fail, what was expected, what was
  observed, and a link to its screenshot
- Screenshots saved alongside it in `qc__pilot/results/<TC-ID>_<date>/`

If I hand you multiple test case files in one task, run them one at a time
and also produce a single `summary.md` in `qc__pilot/results/` with one row
per test case (ID, title, overall result, date run).

Ask me before proceeding if a test case's instructions are ambiguous — don't
guess at intent for a checkpoint.