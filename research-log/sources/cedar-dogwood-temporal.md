# Cedar, and Dogwood (temporal policy over tool-call sequences)

- **question:** Q4 primary; ★Q1 (tool filtering as authorization)
- **captured:** MISS — https://www.infoq.com/news/2026/08/aws-dogwood-agent-policy/ ·
  https://aws.amazon.com/blogs/security/why-policy-in-amazon-bedrock-agentcore-chose-cedar-for-securing-agentic-workflows/ ·
  https://cedarpolicy.com/
- **read:** 2026-09-28

## Cedar

AWS's policy language, used by Amazon Bedrock AgentCore Policy. Chosen for three stated
properties:

- **Analyzability** — policies encode as mathematical formulas. Cedar Analysis detects
  policies that allow nothing, policies that allow everything, conflicts between policies in
  a set, and the precise impact of a change.
- **Readability** — "policies read like structured natural language," reviewable by security
  and compliance people who aren't the author.
- **Determinism** — identical requests, identical decisions.

No loops, no stateful operations ⇒ decidable, O(n) in typical cases.

Two framings worth reusing verbatim:

> "the LLM can't affect the external world directly: it has to go through an orchestrator
> that invokes tools"

> treat "the LLM as an untrusted actor from a security point of view"

★★ **Q1 finding.** Cedar's **partial evaluation filters tools before the agent sees them**,
"preventing the LLM from attempting invocations it doesn't know exist." That is **tool
filtering as an authorization function, not a relevance function** — a fifth position on Q1,
reached from the security side rather than token economics. See
`knowledge/q1-where-control-lives.md`.

## Dogwood

**AWS, August 2026, Apache-2.0, `github.com/dogwood-policy/dogwood`.** Cedar extended with
temporal reasoning over the agent's prior tool calls.

Cedar evaluates one request in isolation. Dogwood adds `when temporal` clauses that read
event history, implemented as standard-library macros over **Metric First-Order Temporal
Logic**, compiled down into Cedar context fields populated from event history before Cedar
decides.

| Operator | Question |
|---|---|
| `formerly` | did this occur within a time window |
| `count_within` | how often |
| `count_distinct_within` | how many distinct values |
| `sum_within` | running total |

Plus `bind` to name an aggregate and compare the current request against it.

**⇒ `count_distinct_within` is "how many distinct devices in the last 60 seconds."** OAP's
limitation 5 is now expressible.

## ⚠ Dogwood's own admitted limitations — the new frontier is here

- ★★ **"Policies reading response events can be defeated by parallel tool calls."**
- Temporal policies **lose Cedar's formal analysis** entirely.
- Requires trusted timestamps, authenticated events, consistent naming, durable storage,
  comprehensive logging, retention.
- The published reference interpreter is **"for exploring and testing," not production
  authorization.**

**Why the first one is ours.** On a network, parallel is the normal mode — you fan out to 40
devices concurrently because serial takes ten minutes. The condition that defeats Dogwood's
temporal policies is not an exotic attack on a fabric, it is the default operating pattern.
A banking testbed will not surface it cleanly.

**Revised thesis:** aggregate limits are *expressible* as of Aug 2026; whether they *survive
concurrency on a real fan-out* is untested, and a network is the natural place to test it.
This is experiment E1 in `PROJECT-preflight-paa-netops.md`.


## ★ Update 2026-10-05 — Dogwood went production, and gained a sidecar

The note above ends on *"the published reference interpreter is for exploring and testing, not
production authorization."* **Still true of that component — but it is no longer the only one.**

★★ **`dogwood-local-engine` 1.0.0, published 2026-09-29** (AWS OSS blog 2026-09-30),
Apache-2.0, Rust. The production-oriented engine:

- **Storage:** per-clause in-memory summaries backed by an embedded KV store (**redb**) on
  disk. **Each append is fsynced before the event is evaluated** — *"an event that has a
  verdict is an event the log holds."*
- **Eviction:** periodic durable snapshot, then pruning of log records already captured.
  Recovery = load snapshot + replay subsequent records.
- ★ **Concurrency:** *"uses a lock that admits one submission at a time"* to linearize
  concurrent events. Policy updates are linearized into the same log.
- ⚠ **It issues verdicts; it does not enforce them.** The embedding application must
  authenticate events, enforce verdicts, and keep its own audit log — **the engine's log is
  pruned.** Directly relevant: `preflight`'s signed record cannot be the engine's log.

★ **`dogwood-server`** — sibling crate in the same workspace, described in the repo README as
a demo service exposing **two Unix sockets: a control plane for policy-set modifications and a
data plane for history and decision events.** **AWS's blog does not mention this.**
`event_to_json` / `event_from_json` in the engine's public API suggest JSON framing.
⇒ a Python sidecar client is ~150 lines after one read of the Rust. ⚠ Wire format undocumented;
the crate is labelled a demo, so this may mean maintaining a fork. **BC3.**

⚠ **Do NOT use `dogwood-py`** (0.0.11.dev33, 2026-09-12) — unofficial, one maintainer, and it
binds the **reference interpreter**, not the Local Engine. In-memory, no eviction, lost on
restart.

### What this does to E1

AWS's launch post **works the aggregate-limit-under-concurrency scenario directly**: an agent
capped at $5,000/hour issues concurrent $2,000 transfers, each passes individually, the
aggregate does not. Their named fix: **count request events including in-flight ones, not
response events.**

⇒ The original thesis holds — nobody has measured it on a fabric — but there is now a claimed
fix to test. **E1a:** does the linearization claim hold under fan-out? **E1b:** does
request-vs-response counting actually fix it? See the 2026-10-05 update in
`PROJECT-preflight-paa-netops.md`.

★ Other Dogwood constraints found: default **`max_window` = 24h**, adjustable per schema, and
policies exceeding the cap are **rejected at validation**. Window size dominates latency — a
24h window is ~300× slower than 15 minutes at 12h sessions. The default event schema pins
`callerPrincipal`, so slicing is **per-principal, not over a global trace**. ⚠ AWS is **not
accepting contributions**.

### Cedar, current

**4.13.0 (2026-09-15)**, ~6–8 week cadence. Python via **`cedarpy` 4.12.1** (k9securityio,
PyO3) — explicitly *not* AWS-supported, but tracks upstream within ~2 months and exposes
`is_authorized`, `is_authorized_batch`, **`is_authorized_partial`**, template linking.

★★ **`cedar-policy/cedar-for-agents` is official AWS** — generates Cedar schemas **from MCP
tool definitions**, so a tool call becomes a typed Cedar request. Ships
`cedar-policy-mcp-schema-generator-python` on PyPI and a `cedar-analysis-mcp-server`.
**This is the strongest argument for Cedar over asserts as M4's primary arm** (open decision
§6.5) — the schema generation is the seam this project sits on.

Also: **Strands Agents SDK ships an official Cedar authorization intervention** in Python —
Cedar evaluated before each tool call, user → principal, tool name → action, args → context,
**fail-closed**. Exposes `context.session.call_count`, a bare counter rather than a history
query. Prior art worth reading even if not adopted.

**Formal analysis** is real but Rust-only: `cedar-policy-symcc` 0.7.0 (symbolic compiler
implemented in **Lean**, machine-checked soundness/completeness proofs, discharges to CVC5),
plus Cedar Analysis CLI. **No Python binding** — so the analyzability argument for Cedar comes
with a tooling caveat.

