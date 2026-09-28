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

