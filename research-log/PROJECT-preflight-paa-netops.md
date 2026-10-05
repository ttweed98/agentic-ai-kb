# PROJECT — `preflight`: a policy authorization hook for an agentic NetOps server

⚠ **RECONSTRUCTED 2026-10-05.** The September original was written to
`/mnt/user-data/outputs/` and never committed; this is a rebuild from the KB, not a restore.
Some original detail is lost. Treat decisions here as current-state, not as a record of what
was decided in September.

**Status: NOT STARTED. Begins after MCPA.**
**read this when:** the build resumes, or when anything in `research-log/build-queue.md`
needs its context.

---

## What it is

A **`before_tool_call` hook inside the MCP server** that runs deterministic policy over every
tool invocation and returns a decision plus a signed record.

```
agent → MCP server → [ preflight hook ] → tool → device
                          │
                          ├─ ALLOW / DENY / ESCALATE
                          └─ signed evidence record
```

This is **Q1 position 4** (`knowledge/q1-where-control-lives.md`). It composes with position 1
(the narrow typed tools already built) and sits where position 3 gateways sit, but inside the
server rather than in front of it.

**The substrate already exists:** five narrow typed tools, the cEOS Containerlab fabric, and
four deliberate fault states (`the-build`, `fault-scenarios`). `preflight` adds a control
plane to a thing that already works, rather than building another agent.

## Why this project and not another agent

1. **It is the Q4 answer.** Everything else in the KB on adversarial work is prior art.
2. **M1 converts MCPA study into build work** rather than competing with it.
3. **It produces numbers.** E1–E5 yield measurements, not assertions — which is what the
   agentic work on a résumé currently lacks.
4. **It is publishable.** Nothing in it touches employer data.

---

## Standing constraints

- ★ **NUC ONLY for all attack work** (`security-lab/`, E1, E3). Never the work laptop.
- ★ **Public and reproducible.** No Koch data, no Koch topology, no Koch configs, no internal
  system names. The fabric is his own cEOS lab.
- Personal API keys are personal. Never pointed at employer code.
- MCP goes in every lab (standing rule, 2026-09-21) — here it is the subject, not an addition.

---

## Milestones

### M0 — Baseline and instrumentation
Take the existing five-tool server and fabric as given. Add call-level logging: tool name,
arguments, caller identity as it currently exists, timestamp, outcome. **No policy yet.**
Exit criterion: a complete trace of a normal troubleshooting run against Fault Case 01.

*Why first:* every later measurement needs a before.

### ★ M1 — A real authorization chain in front of the server
OAuth 2.1 with PKCE, a resource server that validates tokens properly, and token exchange
(RFC 8693) for the delegated case. **This is the milestone that earns MCPA D4** — the exam's
heaviest domain becomes something built rather than read.

⚠ **B1 applies.** Settle how identity reaches a policy decision, and write the answer down.
The agentgateway example matches on `request.headers["x-team"]`, which is attacker-controllable
unless derived from an authenticated principal. The `act` claim carries delegation history but
is **informational only** — access control uses the current actor plus top-level claims
(`sources/aaif-agent-identity-delegated-access.md`).

Exit criterion: a call with no token, an expired token, and a token for the wrong audience each
fail closed, and the failure is in the record.

### M2 — The hook and the policy
`before_tool_call` → deterministic evaluation → **ALLOW / DENY / ESCALATE**.

Policy source is the E2 variable: asserts in the hook, a policy language, or Nautobot data.
Start with asserts so the hook is honest before it is clever.

⚠ **B2 is open and must be decided here.** agentgateway can *alter* a request — force
`max_tokens` regardless of what the caller asked. Alteration is neither allow nor deny, and
the caller cannot detect it. **Does `preflight` need a fourth outcome, and must the record show
it?** Current position: if alteration is supported at all, it is reported, or it is not
supported. An unreported alteration fails `method/refusal-is-not-safety.md` by making the
system look better than it is.

### M3 — The signed record
Every decision produces a record that a third party can verify: what was asked, what was
decided, which rule fired, what the inputs were. Signed, append-only.

*This is the piece that distinguishes the project from a config file.* A gateway that denies
leaves a log; `preflight` leaves evidence.

### M4 — The attack set
Run SAFE-MCP techniques against it (`sources/safe-mcp-taxonomy.md`): tool poisoning
(SAFE-T1001), prompt injection (SAFE-T1102), rug pull (SAFE-T1201), and the rest of the 80+.
Produce the E3 coverage matrix.

### M5 — Publish
Repo, README with the measurements, and the posts. Posts go out **only where there is a
result** — see framing discipline below.

---

## Experiments

| ID | Question | Design |
|---|---|---|
| **E1** | Do aggregate limits survive concurrency? | One policy held constant; vary concurrency C ∈ {1,2,5,10,20,40}; measure violation rate. ⚠ `method/sample-size-owed.md` first — 5 runs per cell has never survived a power analysis. |
| **E2** | Does *data* bind? | Policy semantics held constant; vary the source — asserts / a policy language / Nautobot config context. Measure deny correctness when healthy, and behaviour when the source is absent, malformed, or mutated out-of-band. **Sealed prediction: the data arm fails open somewhere the code arm does not.** |
| **E3** | Coverage | SAFE-MCP technique × {blocked, detected, out-of-scope, unknown} + the `deny_code` that fires. |
| **E4** | Does the trace contaminate the review? | Same artifact reviewed twice — once with the producer's trace, once without. One run, two arms. From `method/separate-the-validator.md`. |
| ★ **E5** | How leaky is a regex guard? | Hold the guard constant; vary obfuscation of the sensitive string (spacing, unicode digits, spelled-out, split across turns); measure escape rate. Report blocked-rate **and** false-positive rate together. **Sealed prediction: escape rate exceeds 20% under trivial obfuscation.** |

★ **B3:** add **Rego/OPA** as E2's third arm. It sits between code and data in a way Cedar
does not, and it is the most widely deployed of the five policy languages
(`knowledge/q1-where-control-lives.md`, append 2026-10-05).

★ Every experiment reports **name accuracy and argument accuracy separately** where both
apply — Caldeira's metric split, carried forward from Q1. A single aggregate overstates.

---

## Framing discipline for the posts

Four posts, and the rule is the same for all four:

1. **Post a result, not an intention.** "I built X" is not a post. "I measured X and got Y,
   and here is the prediction I wrote before running it" is.
2. **Publish the sealed prediction alongside the outcome** — including when it was wrong.
   A falsified prediction is a better post than a confirmed one and almost nobody writes them.
3. **Report the pair.** Blocked-rate without usefulness-rate is the refusal fallacy, and
   someone will say so.
4. **No employer, no internal systems, no implication of either.**

---

## What this project does NOT do

- It is not a gateway. agentgateway exists, is Apache-2.0, and does position 3 well
  (`sources/agentgateway.md`). `preflight` is inside the server, and the comparison against
  agentgateway is part of the writeup rather than a thing to rebuild.
- It is not a new agent. The agent exists.
- It is not Kubernetes. Deliberately — see `sources/agentic-ai-the-hard-way.md` for why that
  substrate was deferred.

## Open before M0

- `method/experiment-metadata-ablation.md` — 15 minutes, unrun, still blocking Q1.
- `method/unit-of-evidence-five-runs.md` — amendment pending the power analysis; read
  *Testing AI* ch03–04 before designing E1's cell count.
- Audio episode 5 (D4) — needs RFC 8693 and the mid-execution-validity rule. M1 depends on
  the same material, so episode 5 is the cheapest way in.
