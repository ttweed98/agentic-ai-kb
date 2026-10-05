# Build queue — things found while studying, owed to the build

**Opened 2026-10-05. These start AFTER MCPA.** Nothing here is in progress.

The build is `preflight` (`PROJECT-preflight-paa-netops.md`). This file holds what later
sources added to it, so the findings are not lost between now and then.

---

## From `sources/agentgateway-model-object.md` (2026-10-04)

**B1 — Settle how identity reaches a policy decision.**
agentgateway's documented authorization example matches on `request.headers["x-team"]`. A
header is attacker-controllable unless the gateway derives it from authenticated identity.
**Find out which agentgateway does before copying the pattern**, and make the answer explicit
in M1 — this is the same question RFC 8693's `act` claim answers for token exchange
(`sources/aaif-agent-identity-delegated-access.md`).

**B2 — Decide whether the control plane may rewrite requests, and whether the caller is told.**
agentgateway can force `max_tokens: 256` regardless of what the caller asked. Silent rewriting
is a new category: not deny, not allow, but *altered*. `preflight`'s decision set is currently
ALLOW / DENY / ESCALATE. **Does it need a fourth outcome, and must the record show it?**
Arbon's rule applies — an alteration that is not reported is the dangerous case.

**★ E5 — How leaky is a regex guard?** (new experiment)
Prompt guards block SSNs and card numbers by regex. Deterministic, so it binds — but the
false-negative surface is measurable and nobody publishes it. Hold the guard constant, vary the
obfuscation of the sensitive string (spacing, unicode digits, spelled-out, split across turns),
measure the escape rate. Report blocked-rate AND false-positive rate together per
`method/refusal-is-not-safety.md`.
**Sealed prediction:** escape rate exceeds 20% under trivial obfuscation.

---

## From `sources/reichert-platform-engineering.md` (2026-10-05)

**B3 — Add OPA / Rego to the policy comparison.**
See the Q1 append of 2026-10-05. Rego is the most widely deployed of the policy languages and
is absent from the current table. It is also a live candidate for **E2's third arm** (does data
bind?) — Rego sits between code and data in a way Cedar does not.

**B4 — Read ch9's memory-graph-over-MCP as a design input to Case 03** before Case 03 is built.

---

## Standing debts carried forward (unchanged from INDEX)

- `method/experiment-metadata-ablation.md` — 15 minutes, still unrun, still the cheapest
  evidence in the KB, still blocking Q1.
- `method/unit-of-evidence-five-runs.md` — amendment pending the power analysis.
- Audio episode 5 (D4) — needs RFC 8693 and the mid-execution-validity rule.
  ★ **Highest-leverage unfinished item**: D4 is 24% of MCPA and episode 5 converts car time
  into the heaviest-weighted domain.
- Capture debt — every source note still carries `captured: MISS`.
