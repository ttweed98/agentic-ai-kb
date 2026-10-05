# PROJECT — `preflight`: Pre-Action Authorization for Network Agents

_Designed 2026-09-26. Original work, not a book rebuild. Public repo, public data, NUC only._

**One line:** an MCP server for a network fabric where every tool call passes a deterministic
policy gate before execution — and an experiment showing where that gate breaks under the
concurrency a real network runs at.

**Why it exists:** three vendors (APort/OAP, AWS/Cedar+Dogwood, Netmiko Guardian) shipped the
same architecture in 2026. All three evaluate policy against *tool calls*. None of them models
a network. Two of them admit the same hole. Nobody has measured it on a fabric.

---

## 0. The honest MCPA alignment

This is the part that decides whether the project is allowed to exist before the exam.

| Domain | Weight | Does `preflight` earn it? |
|---|---|---|
| D1 Fundamentals | 16% | Incidentally. Not a reason to build. |
| D2 Architecture | 14% | **Yes** — you build a server AND a client, both ends of the wire. |
| D3 Base protocol | **26%** | **Yes, strongly** — streamable HTTP, stateless 2026-07-28 core, lifecycle, errors, verified with the Inspector CLI in both protocol eras. |
| D4 Authorization | **24%** | **Only if you build M1 as specified below.** A policy hook teaches you nothing about OAuth 2.1. Putting the *real* MCP authorization chain in front of the server is what converts D4 study into build work. |
| D5 | 20% | Partial. |

**The design move that makes this legitimate:** M1 protects the server with the actual MCP
authorization spec — protected resource metadata (RFC 9728, a MUST), authorization server
metadata (RFC 8414), `iss` (RFC 9207), Client ID Metadata Documents (replacing RFC 7591 DCR),
resource indicators (RFC 8707). You cannot build that without reading the four authorization
pages, which is the D4 prep you already owe, and which is also the source material for audio
episode 5.

**One milestone, three debts paid: 24% of the exam, the owed episode, and the project's
identity layer.** That is the whole justification. If M1 gets skipped, this project is a
distraction and should be parked.

Standing order unchanged: MCPA is first. Case 02 holds the hands-on slot.

---

## 1. Components — adopt now vs later

| Component | Role | When |
|---|---|---|
| Containerlab + cEOS fabric | the target; where concurrency is real | **have it** |
| Your MCP server (narrow tools) | the control point | **have it** |
| `before_tool_call` hook | the gate: ALLOW / DENY / ESCALATE + signed record | **M2** |
| SAFE-MCP | the attack taxonomy — stop inventing attacks | **M0, free** |
| OAuth 2.1 chain per MCP spec | identity; **the D4 earner** | **M1** |
| Cedar / Dogwood | policy language comparison arm | M4 |
| Nautobot (clean lab instance) | policy-as-data arm; roles, tags, config context | M4 |
| Batfish | `DENY: breaks reachability X→Y` — the differentiator | M5 |
| chadell `network-observability-lab` | Loki/Prometheus/Grafana: makes aggregates visible | M5 |
| Netpicker | rule ergonomics to borrow; platform optional | read only |
| Soares `security-lab/` | the attack rig | M3, **NUC only** |

Not adopted, named so they stop taking up space: PCAS, AgentGuardian, E2B/Modal, Promptfoo,
Galileo, A2A, SuzieQ, agentgateway (read as prior art, don't run).

---

## 2. Milestones

Each milestone has **acceptance criteria that are checkable in code, not prose** — DES-7
applied to this project's own definition of done.

### M0 — SAFE-MCP coverage matrix · no build · ~1 day
Read the 14 tactics / 80+ techniques. Produce a matrix: technique ID × {blocked, detected,
out-of-scope, unknown} for your *current* build as it stands today.

- **Proves:** nothing yet. It establishes the baseline and the vocabulary.
- **Accept when:** every technique in the catalog has a verdict and a one-line reason, and
  every "blocked" cites the specific code path that blocks it.
- **Why first:** it's reading and writing, so it fits beside MCPA instead of displacing it —
  and it's the cheapest publishable artifact in the whole project.

### M1 — the server, properly authorized · ★the MCPA milestone
Streamable HTTP MCP server on the 2026-07-28 revision, stateless core, fronted by the real
OAuth 2.1 chain. Plus a minimal client, because D2 wants both ends.

- **Proves:** you can build to the revision the exam tests.
- **Accept when:** `tools/list` and `tools/call` both verified via the Inspector CLI; an
  unauthenticated call is refused; the protected-resource-metadata document resolves; a token
  issued for a *different* resource is rejected (that's RFC 8707 doing its job, and it's the
  detail most people get wrong).
- **Byproduct:** audio episode 5 writes itself from the same reading.

### M2 — the gate · v0
`before_tool_call` hook. Policies as decorated functions. One aggregate limit (distinct
devices per time window). Signed decision record on every path.

- **Proves:** the four properties you can actually claim — determinism, completeness,
  fail-closed, auditability. Non-bypassability you *design for* and test in M3.
- **Accept when:** identical inputs produce identical decisions across 5 runs; a missing or
  malformed policy source yields DENY (not ALLOW, not an exception); every decision — including
  refusals and exceptions — produces exactly one audit record; the Inspector can drive a
  denied call and see a well-formed MCP error rather than a crash.
- **Deliberately out of scope:** passports/Ed25519 and ESCALATE. OAP's own reference
  implementation doesn't ship ESCALATE either. Skip both in v0.

### M3 — ★the experiment that travels · E1 below
Attack M2's aggregate limit with concurrent fan-out.

- **Proves:** whether a temporal/aggregate policy survives the parallelism a network runs at.
  Dogwood *documents* that response-event-reading policies can be defeated by parallel calls.
  Nobody has measured it on a fabric.
- **Accept when:** you have a violation rate per concurrency level, 5 runs per cell, and a
  captured trace of one successful violation showing the race window in the decision timestamps.

### M4 — where does policy bind? · E2 below
Same policy semantics expressed three ways: asserts in the hook, Cedar policies, Nautobot data.

- **Proves:** Q5 asked about authorization instead of prompts. Prose doesn't bind, code does —
  **does data?**
- **Accept when:** all three arms agree on the same attack set when healthy, and you have
  documented behavior for each arm when its policy source is absent, malformed, or mutated
  out-of-band.

### M5 — the differentiator
Batfish as a policy predicate: deny a change that breaks reachability. Audit stream into
Loki, per-agent rates into Prometheus, one Grafana panel.

- **Proves:** a policy predicate no security vendor can copy, because it needs a network model.
- **Accept when:** a planned change that breaks a known reachability invariant is denied, and
  the denial cites the invariant.

---

## 3. The experiments, designed to avoid the OAP confound

The OAP paper's headline (74.6% → 0%) compares a permissive policy to a restrictive one, so it
cannot separate "the architecture works" from "denying more denies more." Don't repeat that.
**Hold the policy constant and vary one thing.**

### E1 — does an aggregate limit survive concurrency?
- **Held constant:** one policy — at most N distinct devices per 60s window. Same fabric, same
  tool, same credentials.
- **Varied:** concurrency C ∈ {1, 2, 5, 10, 20, 40}.
- **Measured:** *violation rate* = fraction of runs where the realized distinct-device count
  exceeded N despite the policy being in force. Plus decision latency and the race window
  between check and execute.
- **Runs:** 5 per cell (unit-of-evidence standard). 30 runs total.
- **Expected shape:** C=1 clean; violations appear somewhere above C=1 and the threshold is
  the finding. If nothing breaks at C=40, that is *also* a publishable result and it means
  your check-then-act is atomic — say so and show why.

### E2 — does data bind?
- **Held constant:** policy *semantics*, attack set, fabric.
- **Varied:** policy *source* — asserts / Cedar / Nautobot.
- **Measured:** (a) deny correctness when healthy; (b) **behavior when the source is missing,
  malformed, or stale** — fail-closed or fail-open; (c) drift detection: mutate the source
  out-of-band mid-run and see whether the hook notices.
- **Prediction to seal before running:** the data arm fails open somewhere the code arm
  doesn't. Write the prediction down first, then score it.

### E3 — coverage
SAFE-MCP technique × verdict × the `deny_code` that fires. A scorecard, not an experiment.

### Record per decision, or none of this is evidence
agent id · tool · argument hash · decision · deny_code · policy source + version · timestamp ·
latency · outcome on every path including exceptions. You already have `audited`; this is the
field list it needs to carry.

---

## 4. Repo

Its own public repo, not under the book-labs hub — this isn't a rebuild, and a standalone
repo with a README that reads on its own is what a LinkedIn post points at.

Proposed name: **`preflight`** — network people know what a preflight check is, and it says
"before the action" without jargon. Your call.

```
preflight/
  README.md            the thesis, the diagram, the findings, in that order
  server/              MCP server + client (M1)
  policy/              the hook, policies, the three arms (M2, M4)
  lab/                 containerlab topology, reset scripts
  experiments/
    e1-concurrency/    design.md, runner, raw results, findings.md
    e2-binding/        design.md, sealed-predictions.md, results
  coverage/
    safe-mcp.md        the matrix (M0)
  docs/
    prior-art.md       OAP, Cedar, Dogwood, Guardian, agentgateway — cited, not copied
```

`prior-art.md` matters more than it looks. It's what separates "I read three specs and built on
them" from "I reinvented a wheel and didn't check." It's also the post nobody else writes.

---

## 5. LinkedIn plan

Your distribution is genuinely good for this specific content — Eric Chou, John Capobianco, Sif
Baksh, Rick Donato / Packet Coders. **That audience is network automation people, not AI-safety
generalists.** Write for them: fabrics, fan-out, SSH sessions, audit trails. And note that
Netpicker and Netmiko sit inside Packet Coders' orbit, so anything touching them has a natural
amplifier.

**Post 1 — after M0. The scorecard.**
"I scored my own network MCP server against SAFE-MCP's 80+ techniques. Here's what it blocks,
what it doesn't, and the four I hadn't thought about."
Cheap, honest, useful to everyone who runs an MCP server, and it costs you no build time.

**Post 2 — after M1. The one that helps other engineers.**
"What it actually takes to put OAuth 2.1 in front of an MCP server, per the 2026-07-28 spec."
Most people running MCP servers have no authorization at all. Concrete and immediately useful.

**Post 3 — after M3. ★The one that travels.**
"AWS's Dogwood extends Cedar with temporal policies — 'at most N devices per minute.' Its docs
note parallel tool calls can defeat it. On a network, parallel *is* the normal mode. Here's the
trace: N=5, concurrency 20, 12 distinct devices touched. 5 runs per cell."

⚠ **Framing discipline, and this decides whether it lands well or badly.** Do **not** write "I
broke AWS's policy language." Dogwood *documents* this limitation; you are measuring a known
gap in a domain its authors didn't test. That framing is citable, generous to the authors, and
unattackable. The other framing invites a correction in the comments from someone who read the
limitations section, and you lose the post and some credibility with it.

**Post 4 — after M5.** "Denying a change because it breaks reachability" — the one only a
network engineer could have built.

Order matters: 1 and 2 build standing so 3 lands with people who already believe you're careful.

---

## 6. The calls that are yours

1. **M0 now, or after the exam?** It's reading and writing, ~1 day, no build. My read: it fits
   beside MCPA. But you set priority.
2. **Is M1 in?** If no, park the whole project — without it this competes with the exam instead
   of serving it.
3. **Repo name** — `preflight`, or yours.
4. **E2's three arms from the start, or code-only in v0?** Three arms is the better experiment;
   code-only is the faster build.
5. **Cedar or asserts for the primary arm?** Asserts are yours and fast. Cedar is analyzable and
   reviewable by people who aren't you.

---

## 7. Guardrails

- Public repo, **public/lab data only. No Koch data, ever.** Clean lab Nautobot, cEOS fabric only.
- Attack work (`security-lab/`, E1, E3) is **NUC only**. Never the work laptop.
- Personal Anthropic key stays personal.
- Cite prior art, don't copy it. `mcp-course` has no license — re-implement and attribute.
- No dumps, no item pools, ever. Self-tests are open recall.
- Claims in posts carry their run counts. A finding with n=1 is an anecdote and gets labeled one.

---

## ★ RESEARCH UPDATE — 2026-10-05

Everything above is the **2026-09-26 original, restored verbatim.** This section is appended,
not merged. Where the two disagree, the original states the design intent and this states the
current state of the world.

### §6.1 — DECIDED

**M0 runs after the MCPA reading is complete.** The reading is the priority; M0 does not start
beside it. His words: *"After I complete the reading for the exam. I need to put this as
priority."* The original's own read ("it fits beside MCPA") is overruled.

⇒ The ordered path from here: **MCPA reading → M0 → M1 → M2 → M3 → M4 → M5.** `START-HERE.md`
carries this for a new session.

Remaining §6 calls stay open: §6.2 is M1 in, §6.3 repo name, §6.4 E2 arms, §6.5 Cedar vs
asserts.

### What changed under the design

**§1 Batfish — it already ships what M5 was going to build.**
`pip install 'pybatfish[mcp]'` → `batfish-mcp`, a **first-party beta MCP server**, 36+ tools,
Apache-2.0, AWS-managed. `run_traceroute` takes `trace_format: text | structured | summary`, so
hop extraction needs no regex. ⚠ *"Tool names and parameters may still change."* M5 becomes
integration, not construction — and `pybatfish/mcp/server.py` is a working reference
implementation of a network-domain MCP server worth reading on its own. ⚠ Breaking in
v2026.08.27: **JDK 21 minimum**; `Next_Hop_IP` / `Next_Hop_Interface` removed from route queries.

**§1 Netpicker — the rule corpus is open, the runner is not.**
`netpicker/pytests-for-networking` is a public repo: `CIS/`, `CVEasy_examples/`, `tests/`. The
assert bodies are plain Python. ⚠ But the `@low`/`@medium`/`@high` decorators and the injected
`configuration` / `commands` / `device` fixtures are **product-supplied globals** — there is no
documented way to run a rule outside the platform, and compliance validation is capped at 10
devices below **$7,500/yr**. ⇒ **a free rule library, not a free runner.** This is **BC2**, the
strongest build candidate here.

**§1 SuzieQ — promote "not adopted" to decided-no.** 0.24.0 was 2025-05-09; 17 months stale, 114
open issues. Its `assert` is a fixed built-in on four tables and **is not user-definable.** Close
the thread.

**§2 M1 — the reference implementation is already on disk.**
`ktbyers/netmiko_mcp` at `~/projects/KBYERS_Netmiko_MCP/netmiko_mcp` imports
`from mcp.server import MCPServer` ⇒ SDK v2 ⇒ **it tracks the 2026-07-28 revision the exam
tests.** The only MCP codebase he owns that does.

**§2 M2 — the test rig is already on disk.** Soares module 04
`tests/test_pre_tool_hook.py` — same artifact name as OAP's `before_tool_call`.

**§2 M2 — four of the five properties already exist in Case 01.** The `audited` context manager
is auditability; the allowlist gate is determinism and fail-closed. ⚠ Missing: **non-bypassability
as a stated guarantee.** ESCALATE stays out of scope, as the original says.

**§3 E1 — splits, and gains a claimed fix to test.**
`dogwood-local-engine` **1.0.0 shipped 2026-09-29** (Apache-2.0, Rust, redb-backed,
**fsync-before-verdict**, *"a lock that admits one submission at a time"*). AWS's launch post
works the aggregate-limit-under-concurrency scenario directly and names a fix: **count request
events including in-flight ones, not response events.**

The original thesis is untouched — the original already says Dogwood *documents* the gap and
*"Nobody has measured it on a fabric."* E1 was never about discovering it. It now sharpens:

- **E1a** — does the Local Engine's linearization claim hold under fabric fan-out?
- **E1b** — does request-vs-response counting actually fix it?

⚠ The engine **issues verdicts; it does not enforce them**, and **its own log is pruned** after
snapshotting. ⇒ `preflight`'s signed record cannot be the engine's log. ⚠ Do **not** use
`dogwood-py` — unofficial, and it binds the reference interpreter, not the Local Engine.
★ **`dogwood-server`** is a sibling crate exposing **two Unix sockets** (control plane for
policy-set changes, data plane for history and decision events) — AWS's blog never mentions it.
A Python sidecar client is ~150 lines after one read of the Rust: **BC3**, with the caveat that
the crate is labelled a demo and the wire format is undocumented.

**§3/§6.5 E2 — new evidence for Cedar.**
`cedar-policy/cedar-for-agents` is **official AWS** and generates Cedar schemas **from MCP tool
definitions** — a tool call becomes a typed Cedar request. Ships a PyPI schema generator. That
schema seam is exactly where this project sits, and it is the strongest argument yet for Cedar
as the primary arm. Cedar is 4.13.0; Python via `cedarpy` (not AWS-supported, tracks upstream
within ~2 months). ⚠ Formal analysis (`cedar-policy-symcc`, Lean-proved) is **Rust-only** — the
analyzability argument comes with a tooling caveat. Also: the **Strands Agents SDK ships an
official Cedar authorization intervention** in Python, fail-closed, prior art worth reading.

**§3 — add Rego/OPA as E2's third arm.** It sits between code and data in a way Cedar does not,
and it is the most widely deployed of the five policy languages.

**§3 Record per decision — the field list now has a standard to borrow.**
OTel GenAI semantic conventions moved to `open-telemetry/semantic-conventions-genai` in v1.42.0
(2026-06-12) and define `gen_ai.tool.*`, `gen_ai.conversation.id`, and **`mcp.*`**
(`mcp.method.name`, `mcp.resource.uri`, `mcp.protocol.version`, `mcp.session.id`). ⚠ **Zero
releases, zero tags, everything marked `Development`.** Borrow the attribute names; pin nothing.
**Capture this one** — it will move.

### Build candidates where nothing good exists

| | What | Size |
|---|---|---|
| **BC1** | open-source Nautobot MCP server | 1–3 days |
| ★ **BC2** | open compliance-rule runner over the Netpicker corpus | days — **strongest** |
| **BC3** | Python client for `dogwood-server`'s two sockets | ~150 lines |

### Deferred, deliberately

Levan's `Agentic-AI-The-Hard-Way` → the Kubernetes block, next year. Reichert's *Agentic AI for
Platform Engineering* → after the Kubernetes study. agentgateway stays read-only prior art, as
§1 says.
