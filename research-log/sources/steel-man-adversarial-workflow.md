# steel-man — adversarial multi-model coding workflow

- **question:** Q5 primary (deterministic gate vs model panel — a *measurement*); Q4 secondary
- **captured:** MISS — https://github.com/Roderick-Clemente/steel-man
- **read:** 2026-09-29 (README only)

Roderick Clemente. Apache-2.0, Python 3.10+, **4★ / 0 forks**, 504 passed / 6 skipped on a
fresh clone. Built on **Factory** primitives (`droid exec`, hooks, pinned model seats) behind
vendor adapters. Phases 0–4.5 complete, Phase 5 in progress. Pilot repo
`Roderick-Clemente/quantum-bank` pinned at `2b70eae1`.

Orchestrates planner / reviewer / executor / validator across **different model families**,
with structural separation to prevent groupthink. The author ran the process manually for
months, copy-pasting between frontier models, then automated it.

## The four enforced invariants

1. **Family separation** — reviewers and validators come from a different model family than
   the producer.
2. **Fresh review context** — validators see specs and diffs, **never the executor's
   reasoning.**
3. **Locked tests** — executors cannot modify tests; **content-hash enforced via PreToolUse
   hooks.**
4. **Valid RED before GREEN** — tests must fail *for the expected reason* before
   implementation starts.

## ★★ Arbon's rule, implemented as infrastructure

> "Do not let the same AI that created the risky behavior be the only validator of that
> behavior." — Arbon, `testing-ai-book/SKILL.md`

Invariants 1 and 2 are that sentence enforced structurally rather than stated. **Third
independent arrival** at the principle — Arbon (prose), our own DEV AI Runs 5–8 (empirical,
the two-layer finding and the circular seams), and this (executable). Different author,
different domain, so it counts separately under `method/same-author-is-one-source.md`.

Invariant 2 is the sharpest part and the one we don't do: **withholding the producer's
reasoning from the reviewer.** Our review passes usually hand over the whole trace. Worth
testing whether that contaminates the review.

## ★★ Q5 — a fourth leg, and this one is a measurement

From the author's live runs:

> "A deterministic gate outperformed a cross-family model panel at catching test-independence
> defects."

Q5's claim is *prefer deterministic oracles; use calibrated judges only where needed*. The
existing legs are our 288 runs, the finance book ch4, and Arbon. All three are assertions or
observations about instruction-following. **This one is a head-to-head against the same defect
class, and the gate won.**

★ Note the intellectual honesty: **that finding partially undercuts his own multi-model
premise**, and he published it as a headline finding anyway. That is the behaviour
`method/hold-the-policy-constant.md` asks for and the OAP paper didn't deliver.

## Three more findings that echo ours

- **"Forged transcripts passed permissive defaults; a one-line fix was needed."**
  = fail-open. `method/fail-open-is-a-pattern`. Committed fixture at
  `tools/fixtures/rung7b-fakepass/`.
- **"Wrong model assignment went undetected until commit records were reviewed; protocol now
  broadcasts model identity."** = the attribution defect from DEV AI Run 4 / Run 8,
  rediscovered independently.
- **"Review panels converged without flagging scope issues; operators must assess breadth
  independently."** = panel agreement is not evidence. Arbon ch05's disagreement problem,
  observed in the wild rather than argued.

## ★★★ For `preflight` — working implementations of what OAP only specifies

| steel-man | Spec equivalent |
|---|---|
| locked tests, content-hash enforced via **PreToolUse hook** | `before_tool_call` doing per-resource integrity enforcement |
| **HMAC-signed evidence tokens** | OAP's Ed25519 signed decision records, cheaper |
| **REJECT / STOP blocks merge** in GitHub Actions | fail-closed, at a real gate |

"Executors cannot modify tests" is a per-resource policy evaluated at a tool-call hook —
`preflight` M2, built and passing tests, by someone not selling a control plane.

CI runs an evidence provider and a validator panel on every PR; deterministic detect →
classify → gate. Secrets: `EVIDENCE_SIGNING_KEY` (essential), validator API keys, optional
`DROID_BIN_ENV`.

## ★★ The one file to read: `tools/OPERATING-RULES.md`

**24 rules, §1–§24, each tied to a real incident.**

That is our `method/` directory's design, arrived at independently, with a longer incident
run behind it. The closest peer artifact to our rules log found so far. Reading how someone
else structures the same thing is worth more here than another source entry.

`PRD.md` second — problem statement, invariants, phase plan, evaluation design.

Also worth a look as a documentation model for `preflight`: `tools/KNOWN-ISSUES.md` commits
security-probe results, and `evidence/` holds probes, envelopes and signed tokens as a build
record. "Commits test evidence, findings, and incident records rather than making claims."

## Layout

```
PRD.md                    spec, problem statement, invariants, phases
tools/                    runner, gates, adapters, operating rules
tools/OPERATING-RULES.md  ★ 24 rules, each with incident history
tools/KNOWN-ISSUES.md     committed security-probe results
templates/                canonical method + per-pilot overlays
skills/                   agent skill assets (digest, index, rehydration)
planning/                 per-phase plans, prompts, run records
evidence/                 build record — probes, envelopes, signed tokens
tests/                    510 tests — gates, runner, lint, layout
droid-wiki/               findings, method, contribution guide
```

Invoked through per-pilot overlays (`.adversarial-sprint/bin/run-sprint`), not a framework
CLI. Debug surface: `tools/sprint-loop.py --help`.

## ⚠ Evidence caveat — our own standard, applied

The findings come from **live runs by a single operator, with no control arm**, self-reported,
n unknown. Nothing is recorded about what else varied alongside the adversarial process. **504
passing tests prove the framework works; they do not show the framework improves outcomes.**

Same gap identified in the OAP paper, at smaller scale. See
`method/hold-the-policy-constant.md`.

**4★ / 0 forks** — one person's system, unvalidated by adoption. And the Factory dependency is
real even behind adapters; we don't have `droid`.

The author is explicit about scope: not a correctness oracle, and different families provide
an independence *control*, not proof. Value claimed is making assumptions, disagreements and
evidence visible and auditable — which is the honest version of the claim.

## Verdict

**Read `tools/OPERATING-RULES.md` and `PRD.md`. Steal the operating-rules structure and the
evidence-directory pattern. Do not install.**
