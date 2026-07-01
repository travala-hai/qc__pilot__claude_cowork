# Test Case Rewriting Prompt — for Claude Cowork Automation

Use this as a standing prompt. Paste raw test case(s) at the bottom and run it
per test case (or in small batches) so you can review the `[ASSUMED]` tags
before they pile up.

---

## The prompt

```
You are converting manually-written QA test cases into an unambiguous,
step-by-step format that an autonomous agent (Claude Cowork) can execute
directly, without a human filling in gaps.

OUTPUT FORMAT (per test case)
- ID, Title, URL(s) under test, Priority (carry over if given, else omit),
  Preconditions
- Steps: flat, sequential numbering. Tag each step [ACTION] or [CHECKPOINT].
  An ACTION is something the agent does. A CHECKPOINT is something the agent
  observes and reports pass/fail on.

REWRITING RULES
1. Split every compound instruction into atomic steps — one action per line.
   ("Click search, type Hanoi, wait" becomes three steps, not one.)
2. Turn vague expected results into literal, checkable assertions: exact
   text, exact badge/label, position in a list, count, URL pattern, etc.
   If the source says "relevant results" or "works correctly," define what
   that means concretely, or flag that it can't be inferred.
3. Run the COMMON-SENSE CHECKLIST below against every step and insert
   anything missing. Tag every step you add (that wasn't in the source)
   with [ASSUMED], plus a short reason. Never silently invent a step.
4. Preserve the original test's intent and scope. Make it executable —
   don't add new coverage, don't remove coverage, don't change what's
   being verified.
5. Prefer condition-based waits over fixed-time sleeps where the agent can
   check for a signal (element appeared, loading spinner gone, text
   changed). Use a fixed wait only as a fallback, and say what it's a
   fallback for.

COMMON-SENSE CHECKLIST — apply at every step
| Category              | When to add                          | Example |
|------------------------|---------------------------------------|---------|
| Element existence      | Before every click / type / read      | "Confirm [element] is visible before interacting; if not present after a reasonable wait, fail the step" |
| Field hygiene           | Before typing into any input          | "If the field already contains text, clear it first" |
| Post-navigation wait    | After any page load / route change    | "Wait for [specific loading indicator] to clear, or wait up to Ns" |
| Post-action wait        | After search / filter / submit / autocomplete | "Wait for [result container] to populate rather than a blind sleep, if the agent can detect it" |
| Ambient obstacles       | Any step where a popup/banner/modal could appear | "If [popup] appears, dismiss it via [close button]; otherwise skip" |
| Known starting state    | Start of test case                    | "Start from a fresh/logged-out session unless stated otherwise" |
| Assertion specificity   | Every expected result                 | Replace "looks right" with the literal text/attribute/order to check |
| Failure behavior        | Every checkpoint                      | State what counts as fail: stop-and-report vs. retry-once-then-report |

Given the raw test case(s) below, rewrite each one following the format and
rules above. Do one test case at a time. Where you infer behavior not
stated in the source, mark it [ASSUMED] and briefly explain the inference.

---
RAW TEST CASE(S):
[paste here]
```

---

## Why the [ASSUMED] tag matters

The checklist above will make Claude add real steps you didn't write —
clearing fields, waiting for popups, checking element existence. That's the
point. But some of those insertions encode an assumption about how the site
actually behaves (e.g. "wait up to 5s" — is 5s actually enough on this
site? does this popup *always* appear, or only for new sessions?). Tagging
them means you can skim just the `[ASSUMED]` lines across a whole rewritten
suite and correct the ones that don't match reality, instead of re-reading
every step.

## One nuance worth knowing

Since Cowork drives a real browser, it can often check for a condition
(element present, spinner gone, text changed) rather than blindly sleeping
for a fixed number of seconds. Fixed waits are more brittle — too short and
the step fails on a slow load, too long and every test run gets slower.
Rule 5 above pushes the rewrite toward "wait for X to appear" and reserves
"wait N seconds" for cases where there's genuinely no better signal to
check against.