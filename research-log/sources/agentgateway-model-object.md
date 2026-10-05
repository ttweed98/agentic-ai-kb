# agentgateway — the AgentgatewayModel object

- **question:** Q1 (where control lives) · Q4 (adversarial)
- **captured:** MISS — https://www.cloudnativedeepdive.com/how-to-implement-the-agentgatewaymodel-object-in-agentgateway/
- **read:** 2026-10-05 — article read, product docs NOT read
- **read this when:** designing the control point for `preflight`, or comparing policy languages

⚠ **SAME AUTHOR AS `sources/agentic-ai-the-hard-way.md`** — Michael Levan. Per
`method/same-author-is-one-source.md` these two count as **ONE source** between them. May be
cited for depth, never as independent corroboration.

Published 2026-10-04. This is the **Kubernetes CRD flavor** of agentgateway (Gateway API,
`parentRefs`). It does not undo the earlier correction — agentgateway still ships as a
standalone binary with flat YAML — but everything below is the k8s object model.

## What the object does

`AgentgatewayModel` collapses **routing, authentication and policy into one object** sitting at
the LLM boundary, attached directly to a gateway with no HTTPRoute in between. Also supports
public model discovery, and virtual models with weighted splits and priority-based failover.

Contrast: `AgentgatewayBackend` handles non-LLM backends and needs path-based routing with
separate policy objects. The model object embeds its policy.

## Four things that matter

**1. CEL is doing more than RBAC.** `knowledge/q1-where-control-lives.md` files CEL as
"agentgateway's RBAC engine." It also runs **request transformation** at the model edge:

```yaml
transformations:
- field: max_tokens
  expression: '256'
```

★ A control plane silently rewriting the caller's request. **New question: what else can be
rewritten, and does the caller know?** That is a trust question nothing else in the KB covers.

**2. The authorization example is weaker than it reads.**

```
'request.headers["x-team"] in ["platform", "data-science"]'
```

A header is attacker-controllable **unless the gateway establishes it from authenticated
identity** rather than trusting what arrived. This is the delegation problem from
`sources/aaif-agent-identity-delegated-access.md` landing in a concrete config.

⇒ **CHECK BEFORE COPYING THE PATTERN:** how does agentgateway populate that header?

**3. Prompt guards are regex.** Blocking SSNs and card numbers; masking emails in responses.
Deterministic, so it binds (`knowledge/q5-what-makes-an-instruction-bind.md`) — but regex
against sensitive data has a large false-negative surface, and that surface is **measurable**.
⇒ experiment candidate, see `research-log/build-queue.md`.

**4. Still request-scoped.** No temporal or aggregate constructs. **Confirms Q1 rather than
challenging it:** everyone can express "is this call allowed," almost nobody can express
"given what already happened." Dogwood remains the only exception.

## Why it matters now

This is a deployable answer to two of the four questions any security owner has to answer —
who may call the model, and what gets blocked on the way through. Position 3 with a config file
attached.

