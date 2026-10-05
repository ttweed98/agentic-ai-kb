# OpenTelemetry GenAI + MCP semantic conventions

- **question:** M5 (the audit stream) · Q4 (what a decision record must carry)
- **captured:** MISS — https://github.com/open-telemetry/semantic-conventions-genai
- **read:** 2026-10-05 — spec pages read; no instrumentation written yet
- **read this when:** designing the decision record, or wiring any agent telemetry

The wire format for agent telemetry. `PROJECT-preflight-paa-netops.md` §3 specifies the fields
a decision record must carry; this is the standard vocabulary for the ones that are not
project-specific.

## ⚠ Status — nothing here is stable

**Every `gen_ai.*` span, metric, event and attribute is status `Development`.** Only borrowed
core attributes (`error.type`, `server.address`) are Stable.

★★ **And there is nothing to pin to.** In semantic-conventions **v1.42.0 (2026-06-12)** all
`gen_ai.*` material was deprecated in the main repo and moved to
**`open-telemetry/semantic-conventions-genai`** — which has **zero releases and zero tags**.
The conventions live on `main` and move underneath you.

⇒ **Pin by commit SHA, and expect breakage.** Renames that already happened:
`gen_ai.system` → `gen_ai.provider.name`; `prompt_tokens`/`completion_tokens` →
`input_tokens`/`output_tokens`; `gen_ai.prompt` and `gen_ai.completion` **removed entirely**.

## The attributes that matter here

`gen_ai.operation.name` ∈ { `chat`, `text_completion`, `embeddings`, **`execute_tool`**,
**`invoke_agent`**, `plan`, `invoke_workflow`, memory ops… }

**Tool call:** `gen_ai.tool.name` · `gen_ai.tool.type` (`function`|`extension`|`datastore`) ·
`gen_ai.tool.description` · `gen_ai.tool.call.id` · `gen_ai.tool.call.arguments` ·
`gen_ai.tool.call.result` · `gen_ai.tool.definitions` (Opt-In)

**Agent:** `gen_ai.agent.name` · `.id` · `.version` · `.description`

**Correlation:** `gen_ai.conversation.id`

Span naming: inference = `{operation} {model}`; agent = `invoke_agent {agent.name}`.
Hierarchy is `invoke_agent` → `chat` → `execute_tool`.

## ★ MCP has its own conventions

`docs/gen-ai/mcp.md`, status Development. Client and server spans named
`{mcp.method.name} {target}`:

- **`mcp.method.name`** (Required) — `tools/call`, `tools/list`, `resources/read`,
  `initialize`, `ping`, notifications…
- **`mcp.resource.uri`** (Conditionally Required)
- **`mcp.protocol.version`** · **`mcp.session.id`** (Recommended)

⇒ Directly relevant to MCPA D3 as well as to the build: this is the protocol's own
observability surface.

## ★ Two design consequences for `preflight`

**Set `gen_ai.conversation.id` to a change or incident number.** The whole agent run then
becomes one retrievable trace, queryable by the identifier operations already use. Highest-
value instrumentation decision available.

⚠ **Keep `gen_ai.input.messages` / `gen_ai.output.messages` OFF.** They are Opt-In and the
spec says instrumentations should not capture them by default — device configuration in
prompts would otherwise land in Tempo. This is a guardrail, not a preference.

## Where it lands

Plain OTel SDK → Alloy → Tempo / Mimir / Loki, queried with TraceQL. Tempo stores `gen_ai.*`
as ordinary OTLP spans; no special support needed. ★ **Tempo 2.9+ also ships its own MCP
server** over TraceQL (experimental) — the agent can query its own traces.

Every agent-observability platform (Langfuse MIT, Phoenix ELv2, OpenLIT Apache-2.0, LangSmith,
Braintrust, Weave) now ingests plain OTLP, so instrumenting bare keeps all of them as drop-in
later options with no re-instrumentation. None is framework-locked any more.

⚠ **Grafana Agent Observability is NOT this.** GA 2026-07-30, **Grafana Cloud only** — their
own docs say *"No self-managed release"* — and generation data goes to a proprietary endpoint
(`AGENTO11Y_ENDPOINT`), not OTel. The 8 "Agent Observability" tools in `grafana/mcp-grafana`
query that Cloud service, not your Tempo. Marketing and mechanism diverge here; the mechanism
is what matters.

