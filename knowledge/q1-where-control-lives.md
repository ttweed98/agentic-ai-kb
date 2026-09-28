# Q1 — narrow tools vs capability layer

**Status: REFRAMED, with evidence. 2026-09-28.**

## The question was asked wrong

Q1 was framed as *narrow tools vs a capability layer* — a question about **count and
granularity**. Two reframes have now landed, and the second supersedes the first.

**Reframe 1 (Anthropic, production blog):** the axis is **intent-shaped vs endpoint-shaped**,
not count. A tool that mirrors an API endpoint forces the model to assemble intent from
parts; a tool shaped like the job does not.

**★ Reframe 2 (this session):** the real variable is **where control lives**. Once you ask
that, "how many tools" stops being the interesting question, because every position on the
axis can be combined with every other.

## The five positions

| # | Control lives | Who does it | Mechanism |
|---|---|---|---|
| 1 | **In the tool** | our build; Anthropic intent-shaped tools | narrow named tools, typed contracts, structured returns — no generic runner to abuse |
| 2 | **In the server's data** | `ktbyers/netmiko_mcp` | one generic `send_show_command` + a default-deny allowlist file, validated at startup |
| 3 | **In a gateway in front** | Netmiko Guardian; `agentgateway`; Caldeira's IBAC layer | a control plane owns access, credentials and audit; the server only executes |
| 4 | **In a hook inside the server** | `preflight` (proposed) | `before_tool_call` → deterministic policy → ALLOW/DENY/ESCALATE + signed record |
| 5 | **★ In tool visibility itself** | Cedar partial evaluation; OpenAI `allowed_tools` | the catalogue the model sees is filtered *by policy* before it can choose |

**They compose.** Caldeira's two-layer design is gateway-then-relevance. Our allowlist gate is
position 1 doing a little of position 3.

## ★ Position 5 is the finding

Two major vendors now filter the tool catalogue as an **authorization act**:

- **Cedar** — partial evaluation "prevents the LLM from attempting invocations it doesn't
  know exist."
- **OpenAI Agents API** — `allowed_tools`, an array on the MCP tool definition.

The retrieval literature (Caldeira's BFCL experiment) treats tool filtering as a **relevance**
problem: fewer, better-matched tools improve selection. These two treat the same mechanism as
**policy**. Same lever, two rationales — and the security rationale subsumes much of the
token-economics one, because a tool filtered for policy reasons is also absent from the
context.

⇒ **"Narrow vs broad" is confirmed as the wrong axis.** The live questions are *where does
control sit* and *is the catalogue a policy surface*.

## Evidence ledger

| Source | Contribution | Weight |
|---|---|---|
| Cloudflare Code Mode (Sept 2025) | competence argument; priority over Anthropic's post | argument, no numbers |
| Anthropic code execution | 98.7% token reduction (~150k → ~2k) | one example |
| Notion | "up to 91%" | no baseline given |
| Anthropic programmatic tool calling | **+11% on BrowseComp / DeepSearchQA; ~38% token cut with no accuracy loss on a 75-tool agent; ~8% MORE costly for sequential single calls** | first real accuracy numbers |
| Caldeira BFCL (443 tools, 200 queries) | 8B+retrieval **92%** beat 70B full-catalogue **79%**; ★name accuracy improves hugely, **AST/argument accuracy stays ~46–61%** | the strongest single experiment |
| Anthropic production blog | intent-shaped vs endpoint-shaped | reframe |
| Cedar / OpenAI `allowed_tools` | filtering as authorization | ★position 5 |

★ **Caldeira's metric split is the one to carry forward: retrieval fixes *selection*, not
*argument quality*.** Any experiment we run must report name accuracy and argument accuracy
separately, or it will overstate the benefit.

## Still owed

`method/experiment-metadata-ablation.md` — 15 minutes, two hosted endpoints, only metadata
differs, no key needed. **Still unrun. Still the cheapest evidence in the KB.** Borrow
Caldeira's design: hold the model constant, change only what it sees, split the metric into
name accuracy vs argument accuracy.

## Superseded

An earlier note filed `makenotion/notion-mcp-server` (22 tools, up from 19) as Q1 evidence.
**That repo is no longer actively maintained**; the hosted server (~29 tools, OAuth-only) is
the live product. The earlier note is corrected, not deleted.

