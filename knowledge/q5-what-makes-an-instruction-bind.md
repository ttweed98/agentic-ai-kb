# Q5 — what makes an instruction bind?

**Status: WRITABLE. Three legs, one of them our own. 2026-09-28.**

## The claim

**Prose does not bind. Code binds.** An instruction expressed as narrative guidance changes
output distribution without constraining it; the same instruction expressed as a check either
passes or fails, and the failure is visible.

Corollary, and the sharper open question: **does *data* bind?** Untested — see below.

## The three legs

**1. Our own evidence — 288 runs.** Context files and instruction prose across the DEV AI
evaluation produced **no correctness gain**. The instruction was present, readable, and
ignored. This is the strongest leg because it is first-hand and the runs are logged.

**2. The finance book, ch4** (*"Behind the scenes: issues encountered and how we fixed them"*,
pp. 239–242):

> "if a verdict can be computed by code, it should be, and the LLM should only be asked to
> relay it"

and, separately:

> "Instructions placed after the output they govern are often ignored"

**3. Arbon** (*How AI Tests Software*, oracles chapter):

> "Prefer deterministic oracles; use calibrated semantic or visual judges only where needed."

⚠ **Counts once.** Arbon's *Testing AI* covers the same ground (ch05 judges and disagreement,
ch09 oracles) but is the same author — see `method/same-author-is-one-source.md`. Use it for
depth on judge calibration, not as a fourth leg.

## Independent commercial confirmation

**Netpicker** ships the claim as a product. Its compliance rules are pytest functions:

```python
@medium(
   name='rule_ntp_sync',
   platform=['cisco_ios'],
   commands=dict(show_ntp_status='show ntp status'),
)
def rule_ntp_sync(commands):
    assert ' synchronized' in commands.show_ntp_status
```

Severity is metadata. Scope is declared. The required command is declared so the engine
collects it. **The check is a plain `assert`.** An entire commercial rule library built on
"the checkable criteria belong in a check, not a paragraph."

## The counter-case, which must be kept

**Arbon ch18 says steering prose does real work** — "focus on privacy", "hold destructive
cases", "use only local execution" change a whole QA operation without a configuration
language. That is not a contradiction; it is a boundary.

**Prose steers. Code binds.** Prose can reallocate attention and set priorities. It cannot
make a criterion checkable. The failure mode is expecting prose to do the second job.

And Arbon adds the condition that makes steering safe: **the exclusion stays visible and the
confidence number drops with it.** Prose that silently removes work is the dangerous case;
prose that removes work *and says so in the output* is fine.

## ★ The open question: does data bind?

Code binds because it executes. Prose doesn't because it only influences. **Data sits between
them** and has not been tested.

A Nautobot config context saying `max_devices_per_call: 5` binds **only if** the hook reads it
on every call and fails closed when it is missing or malformed. Otherwise it is prose with a
schema.

That is experiment **E2** in `PROJECT-preflight-paa-netops.md`: same policy semantics, same
attack set, three sources — asserts in the hook, Cedar policies, Nautobot data. Measure deny
correctness when healthy, and behaviour when the source is absent, malformed or mutated
out-of-band.

**Sealed prediction:** the data arm fails open somewhere the code arm does not.

## Consequences already acted on

- **DES-7.** The open half was "the task prompt never states the decision criteria," which
  assumed better prose was the fix. Q5 says otherwise. The checkable criteria go in a check:
  a 7-day plan drawing on only 3 activities; a date in the past; total vs the $5,500 gate.
- **`find_activities` option C.** Returning the whole catalogue with a count makes the gap
  *checkable* rather than merely honest. Arbon's ch18 rule — an omission must be reported as
  an omission and must reduce confidence — is independent convergence on the same choice.

