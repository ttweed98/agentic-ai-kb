# Q1 — narrow tools vs capability layer

**Status: REFRAMED, with evidence. Last updated 2026-10-02.**
**read this when:** anything touches tool count, tool design, tool filtering, or where a control
point should sit.

## The question was asked wrong

Q1 was framed as *narrow tools vs a capability layer* — a question about **count and
granularity**. Two reframes have landed, and the second supersedes the first.

**Reframe 1 (Anthropic, production blog):** the axis is **intent-shaped vs endpoint-shaped**,
not count. A tool that mirrors an API endpoint forces the model to assemble intent from parts;
a tool shaped like the job does not.

**★ Reframe 2:** the real variable is **where control lives**. Once you ask that, "how many
tools" stops being interesting, because every position combines with every other.

## The five positions

| # | Control lives | Who | Mechanism |
|---|---|---|---|
| 1 | **In the tool** | our build; Anthropic intent-shaped tools | narrow named tools, typed contracts, structured returns — no generic runner to abuse |
| 2 | **In the server's data** | `ktbyers/netmiko_mcp` | one generic `send_show_command` + a default-deny allowlist file, validated at startup |
| 3 | **In a gateway in front** | ★**agentgateway** (LF, 5.1k★); Netmiko Guardian; Caldeira's IBAC layer | a control plane owns access, credentials and audit; the server only executes |
| 4 | **In a hook inside the server** | `preflight` (proposed) | `before_tool_call` → deterministic policy → ALLOW / DENY / ESCALATE + signed record |
| 5 | **★ In tool visibility itself** | Cedar partial evaluation; OpenAI `allowed_tools` | the catalogue the model sees is filtered **by policy** before it can choose |

**They compose.** Caldeira's two-layer design is gateway-then-relevance. Our allowlist gate is
position 1 doing a little of position 3.

★ **Position 3 is no longer theoretical.** `sources/agentgateway.md` — Rust, Apache-2.0, Linux
Foundation, 5.1k stars, 2,804 commits, standalone binary. It does most of what position 4
proposed.

## ★ Position 5 is the finding

Two major vendors filter the tool catalogue as an **authorization act**:

- **Cedar** — partial evaluation "prevents the LLM from attempting invocations it doesn't know
  exist."
- **OpenAI Agents API** — `allowed_tools`, an array on the MCP tool definition.

The retrieval literature (Caldeira's BFCL experiment) treats tool filtering as a **relevance**
problem. These two treat the same mechanism as **policy**. Same lever, two rationales — and the
security rationale subsumes much of the token-economics one, because a tool filtered for policy
reasons is also absent from the context.

⇒ **"Narrow vs broad" is confirmed as the wrong axis.** The live questions are *where does
control sit* and *is the catalogue a policy surface*.

## ★ Four policy languages, one job

| Language | Owner | Character |
|---|---|---|
| **Cedar** | AWS | formally analyzable — detects allow-nothing, allow-everything, inter-policy conflicts, exact change impact. No loops, no stateful ops, O(n). |
| **Dogwood** | AWS | Cedar + temporal operators over call history (MFOTL): `formerly`, `count_within`, `count_distinct_within`, `sum_within`, `bind` |
| **OAP policy packs** | APort | declarative JSON/YAML, condition → `deny_code`, JSON Schema for required context |
| **CEL** | Google | expression language; the one Kubernetes uses for admission policy; agentgateway's RBAC engine |

All four are **request-scoped except Dogwood.** That is the shape of the field: everyone can
express "is this call allowed," almost nobody can express "given what already happened."

## Evidence ledger

| Source | Contribution | Weight |
|---|---|---|
| Cloudflare Code Mode (Sept 2025) | competence argument; priority over Anthropic's post | argument, no numbers |
| Anthropic code execution | 98.7% token reduction (~150k → ~2k) | one example |
| Notion | "up to 91%" | no baseline given |
| Anthropic programmatic tool calling | **+11% on BrowseComp / DeepSearchQA; ~38% token cut with no accuracy loss on a 75-tool agent; ~8% MORE costly for sequential single calls** | first real accuracy numbers |
| Caldeira BFCL (443 tools, 200 queries) | 8B+retrieval **92%** beat 70B full-catalogue **79%**; ★name accuracy improves hugely, **AST/argument accuracy stays ~46–61%** | strongest single experiment |
| Anthropic production blog | intent-shaped vs endpoint-shaped | reframe |
| Cedar / OpenAI `allowed_tools` | filtering as authorization | ★position 5 |
| **LangChain via Alake** | **52.8% → 66.5%, +13.7 pts, 89 tasks, model held fixed** — harness changes alone | ★★the cleanest held-constant result available |

★ **Caldeira's metric split is the one to carry forward: retrieval fixes *selection*, not
*argument quality*.** Any experiment we run must report **name accuracy and argument accuracy
separately**, or it will overstate the benefit.

★ **Stanford's Meta-Harness** (via `sources/alake-harness-engineering-course.md`) searches
harness designs over a fixed model — "scaffolding is now a first-class optimisation target."
Paper not yet located. This is the automated form of the LangChain result and the strongest
future input to Q1.

## Still owed

`method/experiment-metadata-ablation.md` — 15 minutes, two hosted endpoints, only metadata
differs, no key needed. **Still unrun. Still the cheapest evidence in the KB.** Borrow
Caldeira's design: hold the model constant, change only what it sees, split the metric into
name accuracy vs argument accuracy.

## Superseded

An earlier note filed `makenotion/notion-mcp-server` (22 tools, up from 19) as Q1 evidence.
**That repo is no longer actively maintained**; the hosted server (~29 tools, OAuth-only) is the
live product. The earlier note is corrected, not deleted.


## ★ Append 2026-10-05 — a fifth policy language, and policy at the model edge

**OPA / Rego belongs in the table above and was missing.** Surfaced by
`sources/reichert-platform-engineering.md`, where OPA is part of the platform stack.

| Language | Owner | Character |
|---|---|---|
| **Rego (OPA)** | CNCF | general-purpose policy; datalog-derived; by far the most widely deployed of the five. Request-scoped. Sits between code and data in a way Cedar does not — which makes it a live candidate for **E2's third arm**. |

⇒ The field is now **five languages, four of them request-scoped.** Dogwood remains the only
one that can express "given what already happened." That shape is unchanged and now better
evidenced.

**Position 3 gained a concrete config.** `sources/agentgateway-model-object.md` — the
`AgentgatewayModel` object collapses routing, auth and policy into one object at the LLM
boundary. Two findings that change how position 3 should be read:

- **CEL is not only authorization there.** It also performs request *transformation*
  (`max_tokens` forced at the gateway). A control plane that can silently rewrite the caller's
  request is a capability none of the other positions have, and the KB has no category for it.
  ⇒ `research-log/build-queue.md` B2.
- **The published authorization example matches on a request header**, which is
  attacker-controllable unless the gateway derives it from authenticated identity. ⇒ B1.

★ **Open, and new:** every position in the table assumes control is ALLOW or DENY.
*Alteration* is a third outcome, and it is the one a caller cannot detect.
