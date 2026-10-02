# Separate the validator from the producer

**Added 2026-10-02.** Promoted from finding to rule: **four independent arrivals.**

## The rule

**The thing that produced the work must not be the only thing that validates it.**

Separation has to be structural, not an instruction. "Review your own work carefully" is prose,
and prose does not bind (`knowledge/q5-what-makes-an-instruction-bind.md`).

## Why this is now a rule and not a finding

Four arrivals, different people, different domains, different evidence types:

| Source | Form | Evidence |
|---|---|---|
| **Our DEV AI Runs 5–8** | empirical | the two-layer finding; circular seams (2 genuine / 4 circular); failure-fixture-as-success-evidence |
| **Arbon**, `testing-ai-book/SKILL.md` | stated as a rule | "Do not let the same AI that created the risky behavior be the only validator of that behavior." |
| **steel-man** (`sources/steel-man-adversarial-workflow.md`) | enforced structurally | reviewers and validators from a **different model family**; validators see specs and diffs, **never the executor's reasoning** |
| **LangChain**, via `sources/alake-harness-engineering-course.md` | measured | top failure mode on Terminal-Bench 2.0: "the agent wrote a fix, re-read its own code, decided it looked fine, and stopped" — inside a +13.7-point harness change |

Four arrivals meets the bar. Note that `method/same-author-is-one-source.md` applies and passes:
these are four genuinely different authors.

## What structural separation looks like

In rough order of strength:

1. **Different model family** for the validator (steel-man's invariant 1).
2. **Withhold the producer's reasoning** from the validator — specs and diffs only
   (steel-man's invariant 2). ★ **We do not do this.** Our review passes hand over the whole
   trace including the rationale.
3. **A deterministic gate** where the check can be computed at all — and note steel-man's own
   result: *a deterministic gate outperformed a cross-family model panel* at catching
   test-independence defects.
4. **A separate agent** that has not seen the work being produced, for high-stakes checks.

## ★ The cheap test we owe

Invariant 2 is a gap in our own practice and costs almost nothing to test: **review the same
artifact twice — once with the producer's trace, once without — and compare what each review
catches.** One run, one artifact, two arms.

If the no-trace review catches more, that is a method finding with immediate consequences for
how every subsequent run is reviewed.

## Where it bites beyond review

- **Self-authored skills** (`sources/hermes-nous-research.md`): an agent writing the rule it
  will later be judged against is the same circularity relocated.
- **LLM judges** calibrated by the same model family they are judging
  (Arbon ch05, Cohen kappa / Krippendorff alpha).
- **Panel convergence**: steel-man observed "review panels converged without flagging scope
  issues." Agreement among validators is not evidence if they share a producer or a family.

