# Hold the policy constant — vary one thing

**Added 2026-09-28.** Derived from a defect in the OAP paper (arXiv 2603.20953).

## The defect, as a worked example

The paper's headline: with only model alignment defending, social engineering succeeded
**74.6%** of the time (588/788). Under restrictive OAP policy, **0%** across 879 attempts.

Read as an engineer. The two arms differ in **the policy**, not in whether enforcement
exists. At T1, 99% of what reached policy evaluation was **allowed**, because the T1 policy
was permissive. So the comparison is *permissive policy* vs *restrictive policy*, and
"restrictive policies deny more things" is a tautology.

The number cannot separate **"the architecture works"** from **"we said no more often."**

Supporting weaknesses the authors state themselves: self-selected CTF participants who favour
social engineering over protocol attacks; observational, not randomised; banking domain only;
and attackers never had access to the policy specification, so it measures nothing about an
informed adversary.

## The rule

In any experiment about a control layer, **hold the policy constant and vary exactly one
variable.** If the policy itself is the variable, vary it in defined steps against a fixed
attack set — and report the usefulness cost alongside the safety gain
(`method/refusal-is-not-safety.md`).

## Applied

- **E1 (concurrency).** Held constant: one policy — at most N distinct devices per 60s;
  same fabric, tool, credentials. Varied: concurrency C ∈ {1, 2, 5, 10, 20, 40}. Measured:
  violation rate = fraction of runs where realized distinct-device count exceeded N despite
  the policy in force, plus decision latency and the check-to-execute race window.
- **E2 (binding).** Held constant: policy *semantics*, attack set, fabric. Varied: policy
  *source* — asserts / Cedar / Nautobot data. Measured: deny correctness when healthy;
  behaviour when the source is missing, malformed or stale; drift when mutated out-of-band.

Seal predictions before running. Score them after.

## The wider point

A published paper with real numbers can still have an uninterpretable headline. Reading the
experimental design is not optional just because the result agrees with you — this paper's
thesis is one we already hold, which is exactly when the check is most likely to be skipped.

