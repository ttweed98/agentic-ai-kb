# `jarbon/testing-ai-skills` — the *Testing AI* companion skills

- **question:** Q5 / DES-7; Q2; skill-authoring (MCPA Stage 3)
- **captured:** local clone — `~/projects/ai-testing/testing-ai-skills`
- **read:** 2026-09-28

22 `SKILL.md` files: `testing-ai-book/` (root, 40 lines) + `chapters/ch01–21` (34–35 lines
each). 764 lines total. **No `scripts/`, no `references/`, no bundled code anywhere.**

## ★ The root skill is the good part

Guardrails, verbatim:

> "Do not average away severe safety, privacy, security, medical, legal, or
> irreversible-action failures."
>
> "Do not treat a p-value, leaderboard score, LLM judge, or one-run demo as permission to
> ship."
>
> **"Do not let the same AI that created the risky behavior be the only validator of that
> behavior."**
>
> "The question is not whether the AI is impressive, but whether this system should have
> power in its world."

★★ **The third line is our own finding, stated as a rule.** Discovered empirically across
DEV AI Runs 5–8: the two-layer finding, the circular seams, failure-fixture-as-success-
evidence. He has the phrasing; we have the evidence. Add to the method notes verbatim with
citation.

The fourth line is `preflight`'s thesis, better compressed than we've managed.

## ★ Five decision states, not three

Root workflow step 7 recommends *"ship, hold, rollback, **sample more**, or escalate."*

OAP has three: ALLOW, DENY, ESCALATE. **"Sample more" exists in no authorization spec** —
not yes, not no, not ask-a-human, but *insufficient evidence; go get some.* Network version:
deny the fan-out, run a read-only probe, re-decide. → `preflight/docs/prior-art.md`.

## ⚠ The critique — this is the teaching value

All 21 chapter skills are **templated**. Identical five headings (Trigger vocabulary / Apply
the chapter / Produce these artifacts / Coding-agent prompt pattern / Quality bar). Every
description ends **byte-identically**: *"Apply the chapter to produce practical evals, tests,
traces, risk analysis, release evidence, or review guidance."*

1. **The description is the selection surface, and ~40% of each one is boilerplate.** The
   model chooses by reading descriptions; shared text across 21 candidates is noise competing
   with signal. Only the keyword list differentiates ⇒ effectively keyword-match skills.
   ★ This is *the* lesson for the Agent Skills course: description writing is the whole game,
   and this repo shows the failure mode at scale.
2. **The skills reference the book rather than containing it.** "Apply Chapter 5 practices" —
   but ch5 isn't in the file. The agent receives a title, a keyword list, four bullets and
   five artifact names. **A prompt scaffold, not knowledge transfer.** Works only if the model
   already knows the material. Legitimate for a book companion; know what it is.
3. **No progressive disclosure and no executable checks.** ch04 covers t-tests, chi-squared,
   effect size and power analysis and ships no way to compute any of them. ⇒ **DES-7 tension
   confirmed**: in a book about measurement, the checks that could be code are prose.

## ★★ ch03 + ch04 challenge our own method note

Sampling and uncertainty; sample size, confidence intervals, **power analysis**, multiple
comparisons. `method/unit-of-evidence-five-runs.md` has never been checked against a power
analysis. **n=5 may be underpowered for the effect sizes we measure.** Highest-value read in
the list, because it tests a rule already in use. See `method/sample-size-owed.md`.

## ★★ ch10 independently confirms the OAP critique

Anti-patterns named in the description: boolean pass/fail trap, percent passed, over-specific
tests, golden answer, whack-a-mole tuning, one-run demo, static test plan, **aggregate score
trap**, and ★**"refusal versus safety."**

A policy that denies everything scores 100% on safety and 0% on usefulness ⇒ OAP's 0%-breach
T5 result cannot be read as the architecture working. **Cite in the E1 write-up.** See
`method/refusal-is-not-safety.md`.

## ch13 — Q4 prior art, names MCP explicitly

Prompt injection, indirect prompt injection, OWASP LLM Top 10, **MCP security**, tool
permissions, provenance, guardrails, threat model, jailbreaks.

## ★ TOC recovered — resolves the open pairing question

ch01 one-run testing · 02 release evidence · 03 sampling+uncertainty · 04 statistical tests ·
05 judges+disagreement (Cohen kappa, Krippendorff alpha, LLM-judge calibration) · 06 evals
that matter · 07 release readiness · 08 observability+relevance+economics · 09 generated code
· 10 false-confidence antipatterns · 11 confidence engineer · 12 data,bias,raters ·
13 security+guardrails · 14 frontier safety · 15 how models work · 16 white-box introspection
· 17 personalized+dynamic products · 18 embodied+long-running · 19 governance+moral futures ·
20 practical playbook · 21 tokenized product future

⇒ **Verdict on pairing the two Arbon books: methodological, not descriptive** (ch03, 04, 05,
10, 12). The pairing is strong. Independence caveat still applies — see
`method/same-author-is-one-source.md`.

## Option, not a recommendation

21 domain skills + 1 router is **the exact shape of an MCPA study aid** (D1–D5 + an entry
skill), improved by distinct descriptions, real spec content in the file, and the checkable
bits shipped as code. Exam study that yields an artifact. **Not proposed** — `preflight` is
queued and Case 02 holds the hands-on slot. Filed as an option.

## Unrelated but adjacent

**qaskills.sh** — a 450+ "skill" directory for Claude Code, no affiliation with Arbon. Its
install instructions place things in `~/.claude/commands`, which would make them **slash
commands, not Agent Skills**. If accurate, it is a directory of one artifact labelled as
another. The word "skills" currently covers at least three different things; telling them
apart is most of what the Agent Skills course teaches.

