# Arbon — *How AI Tests Software* (first reading draft)

- **question:** DES-7 / Q5 primary; Q2, Q4 secondary
- **captured:** local — personalized review copy, decrypted at
  `D:\Downloads\how-ai-tests-software.pdf` on PLEX-SERVER
- **read:** 2026-09-28 (targeted chapters only)

Jason Arbon, *How AI Tests Software: Agentic Testing, Evidence, and Release Confidence.*
33 chapters in 9 parts + Field Guide. Forewords by James Whittaker and two others.
**A first reading draft**, free edition.

⚠ **Not the same book as Arbon's *Testing AI*.** Second Arbon artifact. See
`method/same-author-is-one-source.md` before citing both.

## ⚠ Caveats that shape how much weight the claims carry

- Front matter contains the author's **live TODO list**, including *"Audit every Bing example
  for factual accuracy and ensure each AI Check example includes the relevant captured
  artifact bundle."* ⇒ **examples are unverified by his own admission.**
- Doubles as documentation for his product — ads for **CARBON / testers.ai**, and chapters
  end with `Try it with CARBON: /carbon-human …`. Discount product-shaped claims.
- ✓ Credit where due: the stated method matches our own standard — *"it argues from observed
  implementation experience, but it does not turn a story into a benchmark or a prediction
  into a measurement. Places that require verification… remain marked."*
- ⚠ **Privacy:** the watermark carries `ttweed98@gmail.com` in the **text layer** on every
  page. Strip before quoting or screenshotting publicly.

## ★★ DES-7 / `find_activities` — independent convergence on option C

Chapter 18:

> "If privacy is excluded, that exclusion **stays visible** and AI Confidence drops with it."

> "The report must show accessibility as excluded by steering or supported by named existing
> evidence — **not silently treat it as passed**."

**General form: an omission must be reported as an omission, and must reduce the confidence
number.** Not "the tool quietly returns less."

The open Case 02 decision was whether `find_activities` should return the whole catalogue
with a count so that a 7-day plan built from 3 activities becomes a *visible* choice rather
than a silent gap. This is that principle, arrived at independently in a different domain.
**Argues for option C.**

## ★ Chapter 18 — authorization material wearing testing clothes

> "Treat documents as **untrusted reference data**; user authority, safety gates, and
> evidence rules **outrank embedded text**."

> "the harness **does not inherit an authority it has not been delegated**"

> "An unanswered question remains visible; **silence is not approval**."

First = non-bypassability. Second = delegation, which OAP v1.0 explicitly does not formalize
and A2A punts on. Third = fail-closed applied to human input — the honest version of
ESCALATE: an unanswered approval is not an approval. All three are better phrased than the
specs. → `preflight/docs/prior-art.md`.

Also from ch18: steering scopes (global / project-release / run-pass / phase / domain /
test-persona), the "Custom Test Prompt" definition, and the rule that the harness records
prompt, author, scope, timestamp and resulting plan so a run stays explainable.

## ★★ Q5 — the second leg

> "Prefer **deterministic oracles**; use calibrated semantic or visual judges only where
> needed."

Compare the finance book ch4: *"if a verdict can be computed by code, it should be, and the
LLM should only be asked to relay it."* **Two independent books, different domains, same
rule.** With our own 288-run evidence that is three legs ⇒ `knowledge/q5-what-makes-an-
instruction-bind.md` is writable.

## Reading plan — four chapters, ~1 hour. Not a cover-to-cover read.

| Ch | Why |
|---|---|
| 18 Context, Steering, Test State, Authority | settled DES-7; best phrasing of delegation and non-bypassability |
| 9 Skepticism, Oracles, Models Judging Models | the Q5 write-up's second citation, in his own words |
| 17 What an AI Test Harness Actually Is | second anatomy vs the finance book's six blocks |
| 21–22 Confidence Testing / Do We Know Enough to Release | "do we know enough to let this proceed" = what an ALLOW answers |

Optional: ch11 portable intent contracts, if Q1's intent-shaped framing needs support.

**Skip** Parts I–III (web UI checks, crawling, WCAG, personas — concepts generalise, examples
don't) and Parts VII–IX (economics, transformation, futures — opinion, and it's a draft).

No formal review is owed: this arrived as a thank-you for a survey, not a review request.

