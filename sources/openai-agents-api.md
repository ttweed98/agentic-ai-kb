# OpenAI Agents API — and `require_approval`

- **question:** Q4 (ESCALATE, shipped); ★Q1 (`allowed_tools`)
- **captured:** MISS — https://openai.com/index/introducing-the-agents-api/ ·
  https://developers.openai.com/api/docs/guides/tools-connectors-mcp
- **read:** 2026-09-28

**10 September 2026, public beta.** A managed service running the **Codex harness**:
orchestration, long-running sessions, tool use. Stateful sessions with automatic context
compaction as the limit approaches, webhooks, parallel subagent delegation, sandboxed code
execution (OpenAI-hosted or partner), programmatic tool calling with parallel execution, and
tool search that loads tool definitions on demand.

> "There are no additional fees for using the Agents API – you simply pay for the tokens and
> tools your agents use."

★ **Strategic read: the harness is now free.** Three vendors give away what Königstein's
book, Lopopolo's repo and Soares module 00 all teach. What isn't free is the policy layer.
That is evidence *for* the `preflight` bet, and for harness engineering commoditising — see
`/areas/harness-engineering.md`.

## MCP as a tool type

Configurable fields: `server_label`, `server_url`, `tunnel_id` (Secure MCP Tunnel, for
reaching a **local** server), `allowed_tools`, `authorization` (OAuth access token),
`server_description`, `connector_id` (**deprecated after 1 Sept 2026**).

```
require_approval: "never" | "always" | { "never": { "tool_names": [...] } }
```

When approval is required the API emits an **`mcp_approval_request`** output item
(`{type, id: "mcpr_…", name, arguments, server_label}`); you answer with an
**`mcp_approval_response`** input item, chained via `previous_response_id`.

## ★★ This is ESCALATE, shipped, with a wire format

OAP specifies ESCALATE in its algorithm and admits its reference implementation lacks it
(limitation 6). Dogwood has no approvals at all. OpenAI has the request/response pair defined
and in production.

⇒ **Revise `preflight` M2:** un-descope ESCALATE. There is a format to model, and a
three-state gate is what makes the project interesting to operators rather than only to
security researchers. A gate that can only say no is not something anyone in NetOps wants.

## ★★ And the gap, which is our territory

`require_approval` is keyed on **tool names only**. It cannot express:

- approve if this call touches more than 5 devices
- approve if the command is a config verb rather than a show
- approve if this is the fourth `show run` in sixty seconds
- approve if Batfish says reachability changes

**Approval granularity is per-tool; a network needs per-argument and per-aggregate.**

Combined with Dogwood's concurrency hole, one coherent claim: **the shipped control layers
are coarse in exactly the dimensions a network cares about.** That is a better thesis than
either gap alone.

## Q1

`allowed_tools` is **tool filtering by the client**. Cedar does it by partial evaluation,
OpenAI by an array. Two major vendors now treat tool filtering as a configuration and
authorization act rather than a retrieval problem ⇒ "narrow vs broad tools" is confirmed as
the wrong axis. See `knowledge/q1-where-control-lives.md`.

## ⚠ Spec-version catch — exam relevant

The MCP connector documentation references **the 2025-03-26 version of the MCP spec** and
permits **HTTP/SSE**, a transport deprecated in March 2025.

⚠ **Caveat:** that page appears shared with the Responses API MCP tool, so the pin cannot be
cleanly attributed to the Agents API. **Verify before quoting.**

The pattern is the point, and it is the same lesson as Soares pinning `mcp==1.30.0`: **the
exam tests 2026-07-28; the market is running revisions behind.** Best available answer to
"why does the Inspector negotiate protocol eras at all."

## Unresolved

Whether the Agents API deprecates the Assistants API and/or the Responses API — not stated.

## ⚠ Rule extension to consider (work, not lab — Tony's call)

`tunnel_id` / Secure MCP Tunnel reaches a **local** MCP server from OpenAI's hosted agent.
Same shape as the standing GitMCP rule (third-party hosted service reaching inside) but
higher stakes: a network MCP server holds **device access**, not read access to code. The
current rule names GitMCP and code; it may want to name the category.

