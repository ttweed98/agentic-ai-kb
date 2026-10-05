# Sample size owed — n=5 has never been justified

**Added 2026-09-28.** An open challenge to `method/unit-of-evidence-five-runs.md`, raised by
Arbon *Testing AI* ch03–ch04.

## The standing rule, and what it rests on

Our unit of evidence is **five runs**. It was adopted because one run is an anecdote and five
was affordable. It has never been checked against a power analysis.

## The challenge

*Testing AI* ch03 (Sampling and Uncertainty) covers sample size, confidence intervals,
repeated runs and the difference between model-reported confidence and statistical
confidence. ch04 (Statistical Tests for AI Quality) covers t-test, chi-squared, p-value,
**effect size**, **practical significance**, **power analysis**, multiple comparisons,
F-score, precision, recall.

**Power analysis is the specific thing owed.** For the effects we actually measure — a
violation rate changing with concurrency, a deny-correctness difference between policy
sources — n=5 per cell may be too few to detect a real difference, which means a null result
would be uninformative rather than evidence of no effect.

Multiple comparisons is the second exposure: E1 runs six concurrency levels, so six tests,
and the chance of one crossing a threshold by accident rises accordingly.

## What to do

1. Read ch03 and ch04 before running E1, not after.
2. Work out the minimum detectable effect at n=5, and either justify five or raise it.
3. If n stays at 5, **say so explicitly in the write-up and state what it cannot detect.**
   An underpowered result reported honestly is still useful; reported as a finding it is not.
4. Amend `method/unit-of-evidence-five-runs.md` with whatever the analysis says — including
   "five is fine for this effect size," if that's the answer.

## Note

This is the most valuable thing the Arbon pairing has produced so far: not a new idea, but a
**challenge to a rule already in use**. Sources that confirm what we do are cheap; sources
that audit it are not.

