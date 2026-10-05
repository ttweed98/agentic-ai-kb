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

Everything above is the original design of 2026-09-26, restored verbatim. This section records
what changed underneath it. **Nothing above has been edited** — where the two disagree, this
section wins on facts and the original wins on intent.

⚠ **A reconstruction of this file was committed on 2026-10-05 and was wrong in one material
way: it gave M1's RFC set as OAuth 2.1 + PKCE + RFC 8693 token exchange.** That is not this
design. The correct set is §0 above — RFC 9728, RFC 8414, RFC 9207, Client ID Metadata
Documents (replacing RFC 7591 DCR), RFC 8707. The reconstruction has been replaced by this file.

### M5 got cheaper — Batfish ships its own MCP server

`pip install 'pybatfish[mcp]'` → `batfish-mcp`. 36 tools at v2026.08.27, more since (PR #995,
merged 2026-09-29, added `generate_dataplane`, `get_lpm_routes`, `get_edges`). `run_traceroute`
gained `trace_format: text | structured | summary` — **structured JSON traces, no regex
parsing.** Batfish is AWS-managed and active; the maintenance question in
`sources/batfish-as-policy-predicate.md` is closed.

⇒ **M5 consumes a tool rather than building one.** The differentiator is unchanged — the
predicate still needs a network model and still cannot be copied by a security vendor — but
the work is integration, not construction. Release notes warn tool names may still change.

### E1 sharpens rather than dies

`dogwood-local-engine` **1.0.0 shipped 2026-09-29** — redb-backed durable log, periodic
snapshots, fsync before verdict, and *"a lock that admits one submission at a time"* to
linearize concurrent events. AWS's launch post works the aggregate-limit-under-concurrency
scenario directly and names the fix: **count request events including in-flight ones, not
response events.**

The original thesis — *"Dogwood documents this; nobody has measured it on a fabric"* — still
holds. What changed is that there is now a claimed fix to test against. **E1 becomes two
sharper questions:**

- **E1a** — does the Local Engine's linearization claim hold under fan-out? AWS asserts it;
  nobody has tested it independently.
- **E1b** — does request-vs-response event counting actually fix it? Two arms, one variable.
  ★ The stronger experiment, and the one post 3 should carry.

⚠ **Framing discipline in §5 applies with more force, not less.** AWS now documents both the
hole and the fix. The citable framing is *"measuring a documented limitation, and a documented
fix, in a domain its authors did not test."*

**Prior art in the wild:** `yunaremaia/agent-guard` issue #136 (2026-09-21, closed, labelled a
security vulnerability) — non-atomic read-modify-write in `Guard.check()` lets concurrent
threads lose increments and bypass `max_tool_calls`. See
`sources/agent-guard-race-condition.md`. This is the failure mode, observed, with a fix.

### M4's Cedar arm has a Python path, and Dogwood has a sidecar

- **Cedar 4.13.0** (2026-09-15). Python via **`cedarpy` 4.12.1** — community (k9securityio),
  not AWS-supported, but tracks upstream within ~2 months and exposes `is_authorized`,
  `is_authorized_batch`, **`is_authorized_partial`**, template linking.
- ★ **`cedar-policy/cedar-for-agents` is official AWS** — generates Cedar schemas **from MCP
  tool definitions**, so a tool call becomes a typed Cedar request. That is precisely the seam
  this project sits on, and it is the strongest argument for Cedar over asserts as the primary
  arm (open decision §6.5).
- ★ **`dogwood-server`** — sibling crate to the Local Engine, **two Unix sockets** (control
  plane for policy sets, data plane for history and decision events). AWS's blog does not
  mention it; the repo README does. ⇒ a Python sidecar client is roughly 150 lines after one
  read of the Rust. ⚠ Wire format undocumented, and the crate is labelled a *demo*.
- ⚠ **Do not use `dogwood-py`** (0.0.11.dev33) — it binds the *reference interpreter*, the one
  AWS says is not for production.

★ **B3 — add Rego/OPA as a fourth E2 arm.** It sits between code and data differently from
Cedar and is the most widely deployed of the policy languages. ⚠ But note the limit: **OPA
decides, it never reserves.** No atomicity primitive, so any aggregate limit over OPA is
read-only against state something else must update.

### M4's Netpicker arm — the corpus is open, the runner is not

⚠ **Correction to `sources/netpicker-rule-syntax.md`:** the rules are not standalone pytest.
`@low` / `@medium` / `@high` and the injected `configuration` / `commands` / `device` /
`devices` parameters are Netpicker-supplied globals; there is no documented way to run a rule
outside the product. Compliance validation is also capped at **10 devices** free, with
Professional from **$7,500/yr**.

★ **But `netpicker/pytests-for-networking` is public** — CIS benchmarks, CVE examples, the
assert bodies in plain Python. Strip the decorator, supply the fixtures, and they port in an
afternoon. **A free rule library, not a free runner.**

⇒ **BC2 below.** This is the strongest build candidate in the project.

### M5 gains a schema it did not have

The original says "audit stream into Loki, per-agent rates into Prometheus, one Grafana panel"
without naming a wire format. There is now a standard one — see
`sources/otel-genai-semconv.md`. Two things that change M5's design:

- ★ **Set `gen_ai.conversation.id` to a change or incident number** and an entire agent run is
  one retrievable trace. Highest-value instrumentation decision in the project.
- ⚠ **`gen_ai.input.messages` / `output.messages` are Opt-In and must stay off** — device
  configs in prompts would otherwise land in Tempo.

⚠ **Grafana's own agent product is unusable here.** Agent Observability (GA 2026-07-30) is
**Grafana Cloud only** — their docs say *"No self-managed release"* — and sends generation data
to a proprietary endpoint, not OTel. The §1 component `chadell/network-observability-lab`
stands; the vendor product does not.

### Closed out

- **SuzieQ — decided no**, not "held." 0.24.0 was **2025-05-09**, 17 months stale, 114 open
  issues, and its `assert` is a fixed built-in on four tables, **not user-definable**. Remove
  it from consideration rather than leaving it as an open thread.
- **agentgateway** — the original says "read as prior art, don't run." Unchanged and correct.
  Its `AgentgatewayModel` object is now documented (`sources/agentgateway-model-object.md`)
  and raises one genuinely new question: a control plane that *alters* a request is neither
  ALLOW nor DENY, and the caller cannot detect it. **M2's decision set may need a fourth
  outcome.**

### ★ Build candidates — gaps with no good open-source answer

Per the standing position that a gap is a candidate, not a blocker.

| # | Thing | Size | Why |
|---|---|---|---|
| **BC1** | An open-source Nautobot MCP server | 1–3 days | NTC's official one (v1.1.0, 2026-09-08) is behind private Artifactory and commercial; the only community option is 2★ with an ambiguous licence (PyPI says MIT, repo LICENSE is an unfilled Apache template) |
| ★ **BC2** | An open compliance-rule runner in Netpicker's shape | days | The corpus is already open. Lands directly on Q5 with a real rule library to test against |
| **BC3** | A Python client for `dogwood-server` | ~150 lines | Nobody has one; the only Python path today binds the engine AWS says not to run |

### §6 decisions — one answered, four open

All five calls in §6 remain yours and remain unmade. They are the blocker, not the research.

### Versions, 2026-10-05

Nautobot **3.2.6** · Golden Config **3.0.8** · SSoT **4.6.1** · Design Builder **3.1.2** ·
Containerlab **0.79.0** · Batfish **v2026.08.27** / pybatfish **2026.9.17.3748** ·
Cedar **4.13.0** · `dogwood-local-engine` **1.0.0** · OPA **1.21.x** · Grafana **13.2.x** ·
Tempo **2.9+** · Netpicker **2.8**.

⚠ Work Nautobot is **2.3.8 / GC 2.x** — a major behind on both.

---

## ★ §6.1 — DECIDED 2026-10-05

**M0 runs after the MCPA reading is complete.** The reading is the priority; M0 does **not**
start beside it. His words: *"After I complete the reading for the exam. I need to put this as
priority."*

⇒ This overrules §6.1's own recommendation above ("it fits beside MCPA") and supersedes any
line in this file that says all five §6 calls are unmade.

**The ordered path from here:**

```
MCPA reading  →  M0  →  M1  →  M2  →  M3  →  M4  →  M5
   (active)      (next)
```

Still open and still his: **§6.2** is M1 in (if no, park the project) · **§6.3** repo name ·
**§6.4** E2's arms · **§6.5** Cedar vs asserts.

★ `research-log/START-HERE.md` carries this order for a cold session. Update it when any of the
four remaining calls is made.
