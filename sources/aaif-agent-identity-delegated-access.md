# AAIF — "Agent Identity and Delegated Access in MCP Systems"

- **question:** D4 / MCPA (24%, weakest domain); `preflight` delegation
- **captured:** MISS — https://aaif.io/blog/agent-identity-and-delegated-access-in-mcp-systems
- **read:** 2026-10-01 (published same day)
- **read this when:** writing audio episode 5, studying D4, or designing identity for `preflight`

Steve Kearns, 1 October 2026, on AAIF's own blog.

★ **AAIF runs the MCPA.** That makes this semi-authoritative for exam *framing and vocabulary*
— and worth watching as a recurring D4 study source, since this is the cert body publishing on
the exam's heaviest domain two and a half weeks after launch. **It is not a substitute for the
spec.**

## ★★ RFC 8693 is missing from our D4 chain

Our episode-5 chain: OAuth 2.1 → RFC 9728 (MUST) → RFC 8414 → RFC 9207 (`iss`) → RFC 7591 DCR
→ CIMD → RFC 8707.

**Add RFC 8693 token exchange. It is the delegation mechanism.** The authorization server issues
a *new* token scoped down for the downstream resource — "the delegated credential can be limited
to the resource and scopes required for that work." This is precisely the piece OAP v1.0 admits
it does not formalize (`sources/oap-open-agent-passport.md`, limitation 1).

## ★★ The `act` claim — exam-trap shaped

RFC 8693's **`act`** claim tracks the **current actor**; nested claims preserve the delegation
chain. But:

> "access-control decisions use the **current actor** and the token's **top-level claims**"
> — delegation history is **informational only**

**The chain is for audit, not authorization.** The intuitive reading — that the whole chain
constrains what is permitted — is wrong.

## ★★ The token boundary

> "An MCP server **must validate tokens issued specifically for it** and **must not accept or
> transit other tokens**."

That is the confused-deputy rule, and RFC 8707 resource indicators are what make it work.
⚠ **The article never names RFC 8707** even though its own delegation pattern requires it. Our
existing chain is more complete than the article on this point.

## ★★★ Mid-execution revalidation — a dimension nothing else has

> "Long-running tasks require **mid-execution authorization checks**; approvals from task
> initiation **don't automatically remain valid**."

Compare: OAP evaluates per call. Dogwood adds temporal windows over call history. OpenAI's
`require_approval` is per tool name. **This says the approval itself expires.**

⇒ The gate currently answers *may this call proceed*. Add: **is the thing that authorized it
still true?** On a network a change window is the canonical case, not an edge case.

## ★★ Spec fact — D3/D4

**The 2026-07-28 MCP specification standardizes W3C Trace Context propagation —
`traceparent` / `tracestate` — in `_meta`**, with OpenTelemetry context correlating requests
across system boundaries.

⚠ **The article calls 2026-07-28 a "release candidate." VERIFY AGAINST THE FINAL SPEC** before
relying on it for the exam — this is exactly the kind of detail that can move between RC and
release.

Trace context is also the mechanism behind their sixth question (reconstructing the path), and
therefore behind `preflight`'s audit story.

## ⚠ Source conflict — keep it visible, don't average it

**Their named anti-pattern:** shared service accounts — "collapse multiple identities into one
broadly privileged credential, losing audit context and authorization granularity."

**That is what `sources/netmiko-guardian-and-netmiko-mcp.md` records Kirk Byers recommending:**
pair netmiko_mcp with AAA/TACACS+ and a read-only **service account** for independent
authorization and audit.

Both are defensible — a service account with independent AAA beats a tool with no external check
at all — but this is a real disagreement between two sources in this KB and it should stay
visible.

**Their counter-pattern:** preserve user identity across calls using **corporate identity tokens
that downstream services already validate**, rather than forwarding MCP access tokens.
(Work-relevant: ServiceNow, Nautobot.)

## The six-question framework

Before agent access is granted:

1. Who initiated the request?
2. Which agent is acting?
3. Which authority reaches downstream services?
4. What happens during delegation?
5. What happens when access changes **mid-task**?
6. Can the **complete path be reconstructed**?

Good skeleton for `preflight`'s identity design. Q6 is our audit guarantee.

## ⚠ Limits

A blog post, not the spec — and the exam tests the spec. No code. No SPIFFE or workload-identity
detail. Does not name RFC 8707.

## Action

**Audio episode 5 (owed, D4) needs RFC 8693 added, plus the mid-execution-validity rule.**

