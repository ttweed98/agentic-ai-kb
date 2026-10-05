# agent-guard #136 — the E1 failure mode, observed

- **question:** Q4 · E1 (does an aggregate limit survive concurrency)
- **captured:** MISS — https://github.com/yunaremaia/agent-guard/issues/136
- **read:** 2026-10-05 — issue metadata read, **source not read**
- **read this when:** designing E1, or writing post 3

**2026-09-21. Closed. Labelled a security vulnerability.**

> *"Guard.check() race condition allows max_tool_calls bypass under concurrent load."*

`Guard.check()` performs a non-atomic read-modify-write (`self._tool_calls += 1`). Concurrent
threads read the same value and increments are lost, so the counter stays below
`max_tool_calls` while actual executions exceed it. Fix: `threading.Lock()`.

Sibling issues worth noting:
- **#92** — the counter increments on *denied* calls, so enforcement fires early. A second,
  opposite failure in the same component: one undercounts, one overcounts.
- **#62** — a request for sliding-window rate limiting, i.e. the feature Dogwood exists to
  provide.

## ★ Why this belongs in the KB

E1 has been an argument from Dogwood's documented limitation. **This is the same failure in a
shipped Python agent guard, found by a user, triaged as a vulnerability, and fixed** — the
cheapest, most concrete evidence available that the class is real rather than theoretical.

It is also the *simplest* instance: no distributed system, no event log, no temporal logic.
One process, one counter, no lock. ⇒ **If the bug exists at that complexity, the interesting
question is not whether aggregate limits fail under concurrency — it is whether the
implementations that claim to handle it actually do.** Which is exactly E1a.

★ **Useful for post 3's framing.** It gives a citable, non-AWS instance, so the post is not
"I tested AWS" but "here is a failure class, here is it happening in the wild, here is what
the serious implementations claim, and here is whether the claim holds on a fabric."

## ⚠ Unverified

Repository health, project adoption, and whether the fix is correct were **not** checked —
only the issue, its label and its closure. Before citing it publicly, read the fix commit and
confirm the project is real enough to be worth naming. A thin repo weakens the point rather
than making it.
