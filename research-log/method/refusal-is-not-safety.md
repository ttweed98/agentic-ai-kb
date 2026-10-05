# Refusal is not safety

**Added 2026-09-28.** Named as an anti-pattern in Arbon, *Testing AI* ch10; arrived at
independently while reading the OAP paper.

## The rule

**A control that denies everything scores perfectly on safety and zero on usefulness.** Any
safety number reported without its corresponding usefulness number is uninterpretable.

Arbon's ch10 anti-pattern list names it directly: *"refusal versus safety"*, alongside
*"aggregate score trap"*, *"boolean pass fail trap"*, *"percent passed"*, *"golden answer"*,
*"whack-a-mole tuning"*, *"one-run demo"*, *"static test plan"*.

## Where it bit

OAP's T5 result — 0 breaches in 879 attempts under restrictive policy — is reported as
evidence the architecture works. But a sufficiently restrictive policy produces that number
whatever the architecture is. The measurement that would separate them is *what legitimate
work the policy also blocked*, and it isn't reported.

## What to report instead — always as a pair

| Axis | Measure |
|---|---|
| Safety | violations that got through |
| Usefulness | legitimate operations denied, or made so slow they were abandoned |

For `preflight` that means every E1 and E3 result carries a **false-deny count** against a
set of known-good operations. A gate that blocks the attack and also blocks the nightly
backup has not succeeded.

## Related

`method/hold-the-policy-constant.md` — the experimental-design half of the same problem.

