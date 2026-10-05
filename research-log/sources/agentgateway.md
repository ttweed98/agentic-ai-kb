# agentgateway — the gateway position, taken seriously

- **question:** Q1 (where control lives, position 3); Q4; `preflight` strategy
- **captured:** MISS — https://github.com/agentgateway/agentgateway
- **read:** 2026-10-01
- **read this when:** deciding what `preflight` should build vs reuse, or reasoning about the
  gateway position on Q1

## ★★ Supersedes the 2026-09-26 verdict, which was wrong on the facts

The earlier call was *"read as prior art, don't adopt — the k8s dependency makes it wrong for
a NUC lab."* **The k8s claim is false.** It ships as a **standalone binary with flat YAML
config**; Kubernetes is one of three deployment options (binary / k8s with its own controller
and Gateway API support / Docker), not the shape of the thing.

That earlier verdict was reached from a search-result description rather than the repo. Logged
as a provenance failure in its own right.

## What it is

Open-source proxy giving "drop-in security, observability, and governance for agent-to-LLM,
agent-to-tool, and agent-to-agent communication."

**Rust. Apache-2.0. Linux Foundation governance** — the same home as SAFE-MCP.
**5.1k stars, 891 forks, 2,804 commits**, 203 open issues, 86 PRs. Not a side project.

**Three gateways:**

| Gateway | What it does |
|---|---|
| **LLM** | routes to OpenAI, Anthropic, Gemini, Bedrock behind one OpenAI-compatible API |
| **MCP** | **tool federation**; stdio + HTTP + SSE + Streamable HTTP transports |
| **A2A** | agent-to-agent protocol |

Plus inference routing to self-hosted models via Kubernetes Inference Gateway extensions.

**Security:** JWT, API keys, OAuth · **RBAC with a CEL policy engine** · rate limiting · TLS.
**Guardrails:** regex, OpenAI moderation, AWS Bedrock Guardrails, Google Model Armor.
**Observability:** OpenTelemetry metrics, logs and tracing, plus a built-in UI.

## ★ Q1 — a fourth policy language

| Language | Owner | Character |
|---|---|---|
| Cedar | AWS | formally analyzable; detects allow-nothing / allow-everything / conflicts |
| Dogwood | AWS | Cedar + temporal operators over call history |
| OAP policy packs | APort | declarative JSON/YAML, condition → deny_code |
| **CEL** | Google | expression language; the one Kubernetes uses for admission policy |

Four vendors, four languages, one job. ⇒ update `knowledge/q1-where-control-lives.md`:
**position 3 (gateway in front) now has a serious, Linux-Foundation-governed instance.**

## ★★ Strategic impact on `preflight` — stated plainly

**This already does most of what M2 proposed, better resourced.** A hook that gates tool calls
against a policy engine and emits audit through OpenTelemetry is their product. Building that
from scratch to prove a point we cannot claim priority on is the wrong use of the time.

**What it cannot do — and none of it is incidental:**

1. ★ **CEL is request-scoped.** Like Cedar, it evaluates one request in isolation — no
   `count_distinct_within`, no temporal operators. That is the gap Dogwood exists to fill, and
   **a gateway is *more* exposed than a server, because the gateway is the fan-out point.**
   Forty parallel calls through one proxy is the canonical case. **E1 still stands and is now
   better targeted.**
2. ★ **No desired-state predicate.** regex / moderation / Model Armor are **content-level**
   filters. *"DENY: this breaks reachability from X to Y"* needs a network model. Still only
   ours — see `sources/batfish-as-policy-predicate.md`.
3. **No mid-execution revalidation** — see `sources/aaif-agent-identity-delegated-access.md`.

## ★★ Revised project shape

**Don't rebuild the gateway. Put network-semantic policy where the gateway can't reach it.**

Better on three counts: it extends a Linux Foundation project rather than a solo repo; the
concurrency experiment runs against something people actually deploy; and *"I added
reachability-aware policy to agentgateway"* is a far stronger post than *"I built a hook."*

⇒ **M2's deliverable shifts from THE HOOK to THE POLICY SEMANTICS.**

## ⚠ Check before building on it

**No MCP spec version is stated in the README.** Same open question as Netmiko Guardian and the
OpenAI connector docs. **Find out what revision the MCP gateway actually speaks** — that decides
whether it is a D3 study object or a legacy-era artifact.

## MCPA value

Tool federation across stdio, HTTP, SSE and Streamable HTTP in one artifact is a live D2/D3
object for transports and protocol eras.

