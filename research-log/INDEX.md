# agentic-ai-kb — INDEX

**Paste this file at the start of a session.** It is the router: it says what each file settles
and when to read it, so Claude asks for two or three files instead of the whole repo.

Last updated **2026-10-02**. Entry point for questions is `questions.md`; this is the
file-first view of the same thing.

---

## Read-me-first rules

These change how everything else is read. Four of five are rules about *evidence*, not about AI.

| File | The rule |
|---|---|
| `method/same-author-is-one-source.md` | Two works by one author are ONE source. May be cited for depth, never as independent corroboration. |
| `method/hold-the-policy-constant.md` | Vary exactly one variable. Derived from a confound in the OAP paper's headline number. |
| `method/refusal-is-not-safety.md` | A control that denies everything scores 100% safety, 0% usefulness. Always report the pair. |
| `method/sample-size-owed.md` | n=5 has never survived a power analysis. Read *Testing AI* ch03–04 before running E1. |
| `method/separate-the-validator.md` | The producer must not be the only validator. Four independent arrivals — this one is settled. |

---

## By question

### Q1 — where control lives *(was: narrow tools vs capability layer)*
**→ `knowledge/q1-where-control-lives.md`** — five positions, four policy languages, evidence ledger.

Supporting: `sources/agentgateway.md` (position 3, made real) · `sources/cedar-dogwood-temporal.md` (the policy languages) · `sources/openai-agents-api.md` (`allowed_tools` = filtering as authorization) · `sources/netmiko-guardian-and-netmiko-mcp.md` (positions 1–3 side by side).

**Still blocked on:** `method/experiment-metadata-ablation.md` — 15 minutes, unrun.

### Q2 — procedural
Material identified, not yet read. `sources/arbon-testing-ai-skills-repo.md` has the TOC and the verdict that *Testing AI* is methodological rather than descriptive (ch03, 04, 05, 10, 12).

### Q4 — adversarial
**Unblocked.** `sources/safe-mcp-taxonomy.md` supplies the attack catalogue — 14 tactics, 80+ techniques, SAFE-T IDs. Next artifact is a coverage matrix; ~1 day, no build.

Prior art, in order of usefulness: `sources/oap-open-agent-passport.md` (the five properties; the formal model) · `sources/cedar-dogwood-temporal.md` (temporal policy, and its concurrency hole) · `sources/aaif-agent-identity-delegated-access.md` (delegation, RFC 8693, mid-execution revalidation) · `sources/agentgateway.md` (what already exists) · `sources/batfish-as-policy-predicate.md` (the predicate only we can build) · `sources/steel-man-adversarial-workflow.md` (hooks and signed evidence, built) · `sources/alake-harness-engineering-course.md` (the incident case studies).

### Q5 — what makes an instruction bind
**→ `knowledge/q5-what-makes-an-instruction-bind.md`** — four legs, the counter-case, and the open sub-question.

Supporting: `sources/netpicker-rule-syntax.md` (the claim as a shipped product) · `sources/arbon-how-ai-tests-software.md` · `sources/steel-man-adversarial-workflow.md` (the measurement) · `sources/hermes-nous-research.md` (does a self-written skill bind?).

### MCPA / D4
**→ `sources/aaif-agent-identity-delegated-access.md`** — the cert body on the exam's heaviest domain. **Adds RFC 8693 to the D4 chain.**

---

## Corrections logged — read these before repeating a claim

| What was wrong | Where it's fixed |
|---|---|
| arXiv 2603.20953 called "the Netmiko Guardian paper" — it is **OAP / APort**, unrelated vendor | `sources/oap-open-agent-passport.md` |
| "Aggregate limits are the open frontier" — **Dogwood made them expressible** (Aug 2026); the frontier moved to concurrency | `sources/cedar-dogwood-temporal.md` |
| agentgateway dismissed as "k8s-shaped, wrong for a NUC" — **false, it ships as a standalone binary** | `sources/agentgateway.md` |
| `makenotion/notion-mcp-server` filed as Q1 evidence — repo unmaintained, hosted server is the live product | `knowledge/q1-where-control-lives.md` |

---

## The experiments

| ID | Question | Design |
|---|---|---|
| **E1** | Do aggregate limits survive concurrency? | One policy held constant; vary concurrency C ∈ {1,2,5,10,20,40}; measure violation rate. 5 runs per cell — ⚠ see `method/sample-size-owed.md` first. |
| **E2** | Does data bind? | Policy semantics held constant; vary the source — asserts / Cedar-CEL / Nautobot data. Measure deny correctness, and behaviour when the source is missing, malformed or mutated. Sealed prediction recorded. |
| **E3** | Coverage | SAFE-MCP technique × {blocked, detected, out-of-scope, unknown} + the `deny_code` that fires. |
| **E4** | Does the trace contaminate the review? | Same artifact reviewed twice — once with the producer's trace, once without. One run, two arms. From `method/separate-the-validator.md`. |

---

## Standing debts

- **Capture debt:** every source note carries `captured: MISS`. Nothing has been through
  `capture.sh`. URLs are in each file.
- **`questions.md`** — status-board changes from 2026-09-28 may still be unmerged.
- **`method/unit-of-evidence-five-runs.md`** — amendment pending the power analysis.
- **Audio episode 5 (D4)** — now needs RFC 8693 and the mid-execution-validity rule.
- **Verify:** whether the 2026-07-28 `_meta` trace-context propagation survived from RC to
  final; what MCP revision agentgateway's MCP gateway speaks.

---

## How to get this to Claude

The repo is private and inside WSL, where the device bridge cannot reach (UNC paths are
refused — tested 2026-09-29). So:

1. **Paste this INDEX** at the start of a session. It names the files worth asking for.
2. Claude asks for two or three; paste those.
3. If a session needs more, mirror to a grantable Windows folder:
   ```bash
   mkdir -p /mnt/c/Users/tonyt/kb-mirror
   rsync -a --delete ~/projects/agentic-ai-kb/ /mnt/c/Users/tonyt/kb-mirror/
   ```
   then have Claude request access to `C:\Users\tonyt\kb-mirror`. **One direction only** —
   WSL → Windows. Files Claude writes go to Downloads and get `mv`'d in, never written to the
   mirror.


---

## Added 2026-10-05

**New sources**
- `sources/agentgateway-model-object.md` — Q1 position 3 with a config file; CEL doing
  transformation as well as authz; regex prompt guards. ⚠ same author as the next entry.
- `sources/agentic-ai-the-hard-way.md` — Levan's K8s agentic repo. **Placed in the Kubernetes
  block (next year).** ⚠ same author as the entry above — one source between them.
- `sources/reichert-platform-engineering.md` — Packt platform-engineering book. **Placed after
  the Kubernetes study.** Surfaced the OPA gap and a memory-graph-over-MCP design for Case 03.

**New method rule**
- `method/read-and-run-is-the-default.md` — four arrivals. Check the step verbs before buying;
  a book is input, not a rep.

**New file**
- `build-queue.md` — findings owed to `preflight`, which starts after MCPA. Holds B1-B4 and
  **E5 (how leaky is a regex guard)**.

**Knowledge updated**
- `knowledge/q1-where-control-lives.md` — appended: Rego as a fifth policy language; position 3
  made concrete; **alteration named as a third outcome the table does not cover.**
