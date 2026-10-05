# OAP — Open Agent Passport (APort)

- **question:** Q4 (adversarial / control), secondary Q1
- **captured:** MISS — https://arxiv.org/html/2603.20953v1
- **read:** 2026-09-28

⚠ **Provenance correction.** This was first flagged in conversation as "the Netmiko Guardian
paper." It is not. No connection to Kirk Byers or Flock9. Same *pattern*, different vendor.

"Before the Tool Call: Deterministic Pre-Action Authorization for Autonomous AI Agents."
Uchi Uchibeke, APort Technologies Inc., Toronto. March 2026. arXiv 2603.20953v1.

## The architecture

Three parts:

1. **Agent passport** — signed credential binding identity to capabilities and limits.
   Fields: agent id, status (active/suspended/revoked), capability list (`web.fetch`,
   `payments.charge`), limit constraints, assurance level (L0–L4FIN), Ed25519 signature.
2. **Policy packs** — declarative, versioned rule sets. JSON Schema for required context,
   condition → deny_code rules, minimum assurance level. 21 packs; one of them is for MCP.
3. **`before_tool_call` hook** — blocking, synchronous, **at the framework layer**, therefore
   outside model reasoning and resistant to prompt injection.

★ That hook name is the same artifact as Soares module 04 `tests/test_pre_tool_hook.py`.
The rig for testing this is already cloned.

## Formal model

`authorize(T, P, Π) → (D, L)` — tool call, passport, policy pack(s) → decision ∈
{ALLOW, DENY, ESCALATE} and a signed audit entry.

Five properties, which are a usable spec for our own gate:

| Property | Demand |
|---|---|
| Determinism | same inputs → same decision; no sampling, no temperature |
| Completeness | every valid input receives a decision |
| Fail-closed | invalid/expired passport or unavailable policy → **DENY** |
| Non-bypassability | tool cannot execute unless ALLOW; enforced at platform level |
| Auditability | every tuple produces a signed log entry |

Policy evaluation is a **decidable fragment** — comparisons and set membership, no loops or
recursion — so termination is guaranteed, O(n) over typically 3–8 rules.

**Against our build:** the `audited` context manager is property 5. The allowlist gate is
properties 1 and 3. Four-fifths arrived at independently. Missing: non-bypassability as a
*stated* guarantee, and ESCALATE.

## Numbers

- 1,151 sessions, 4,437 decisions, 2,419 denied (54.5%). Most common denial
  `oap.unknown_capability` (1,453).
- T1 permissive policy: 74.6% attack success (588/788). T5 restrictive: **0% of 879**.
- Latency p50: 53ms cloud API, 174ms local. Signing ~10ms, audit write ~9ms async.
- Covers 8 of 10 OWASP Agentic Top 10 (full on Identity & Privilege Abuse).

Apache-2.0: `aporthq/aport-spec`, `aport-policies`, `aport-agent-guardrails`. Spec DOI
10.5281/zenodo.18901596. Live CTF at vault.aport.io.

## ⚠ Why the headline number does not support the headline claim

**T1 and T5 differ in the policy, not in whether enforcement exists.** At T1, 99% of what
reached policy evaluation was allowed, because the policy was permissive. So 74.6% → 0%
measures *permissive vs restrictive*, not *architecture vs no architecture*. "Restrictive
policies deny more" is a tautology.

The paper is honest about the rest: self-selected CTF participants who favour social
engineering over protocol attacks, observational not randomised, banking domain only, and
**attackers never saw the policy spec** — so it says nothing about an informed adversary.

See `method/hold-the-policy-constant.md` and `method/refusal-is-not-safety.md`.

## Admitted limitations — two of them are ours to test

- **L5 composability / structuring:** "each tool call evaluated independently; sequences of
  individually-permitted actions could exceed aggregate limits." Sliding-window aggregates
  are a v1.1 *draft*. ⇒ superseded in part by Dogwood, see `sources/cedar-dogwood-temporal.md`.
- **L1 delegation:** multi-agent delegation with narrowed permissions is not formalised.
- L6 **ESCALATE is specified but not implemented** in the reference; deployments use
  application-layer human-in-the-loop.
- L2 policy expressiveness (JSON/YAML vs PCAS's Datalog); L3 static policies; L4 tool-call
  boundary only; L7 benchmarks at ~1,000 concurrent, not 10,000+/s; L8 assumes the framework
  runtime is uncompromised.

## Positioning

Four-layer "full safety stack": model alignment (probabilistic, training-time) → **pre-action
authorization** (deterministic, runtime) → sandboxed execution (containment) → post-hoc
evaluation (retrospective). Distinguished from sandboxes (NemoClaw, E2B, Modal), model-based
screening (Google ADK, Safiron), post-hoc eval (Promptfoo, Galileo), and PCAS (closest
academic work; Datalog vs JSON, compiler reference monitor vs cloud hook).

Proposes a new standards category: **Pre-Action Authorization (PAA)** — policy definition,
platform-level hook, verifiable credentials, audit log, fail-closed defaults. Maps to NIST AI
RMF and SP 800-53 (AC, AU, IA, SC, SI).

