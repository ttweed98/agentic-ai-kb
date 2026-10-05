# Netpicker — DES-7 as a shipped product

- **question:** Q5 (what makes an instruction bind) / DES-7
- **captured:** MISS — https://netpicker.io/knowledge-base/test-rule-syntax/ ·
  https://github.com/netpicker/pytests-for-networking · https://netpicker.io/nautobot-plugin/
- **read:** 2026-09-26

Network automation platform from Flock9: config backup, security/compliance testing, job
automation. Docker Compose + Helm + TextFSM templates. `netpicker/netpicker` 279 commits,
37★. Rules live in `netpicker/pytests-for-networking`.

## The rule syntax — verbatim

```python
@medium(
   name='rule_ntp_sync',
   platform=['cisco_ios'],
   commands=dict(show_ntp_status='show ntp status'),
)
def rule_ntp_sync(commands):
    assert ' synchronized' in commands.show_ntp_status
```

```python
@low(name='rule_banner_check', platform=['cisco_ios'])
def rule_banner_check(configuration):
    assert 'Authorized access only' in configuration
```

## ★ Why this is the DES-7 finding, shipped

- **Severity is metadata** (`@low` / `@medium` / `@high`), not prose.
- **Platform scope is declared**, so the engine knows where the rule applies.
- **The command the rule needs is declared**, so the engine collects it before the check runs.
- **The check itself is a plain `assert`.**

DES-7's finding was "the checkable criteria belong in a check, not a paragraph." Netpicker's
entire rule library is that sentence in code, with severity and data-dependency as
declarations. Independent commercial confirmation.

## Fixtures

`configuration` (the backup, with `.lines`), `commands` (declared output, dot-accessed),
`device` (name, ip_address, platform, tags, and `.cli()` for **live** execution), `devices`
(tag-scoped collection), and **`netbox`** — rules can read the source of truth.
`device_tags` scopes which devices a rule runs against.

★ Platform strings are **Netmiko platform names** (`cisco_ios`, `juniper`, and `arista_eos`
applies to our cEOS fabric). The rule engine is already Netmiko-shaped — Flock9 × Netmiko
Enterprise is not a coincidence.

## ⚠ Two integration gotchas

- The Netpicker **Nautobot plugin** exists (PyPI; surfaces backups, CVEs, config verification
  and job execution on Nautobot device pages; no separate inventory sync) but **requires
  Nautobot 3.x+**. Staging GC world is 2.3.8. **Verify the localhost stack version before
  planning around it**; if 2.x, use the API directly.
- The `netbox` fixture is **pynetbox**, configured via `NETBOX_API` / `NETBOX_TOKEN` — that
  is NetBox, **not Nautobot**. The API diverged at Nautobot 2.0. **Unverified against
  Nautobot; do not plan on it.** Plugin and fixture are two different paths and only one is
  Nautobot-aware.

## Verdict

**Read `pytests-for-networking` for the ergonomics; running the platform is optional.**
Reading probably gets 80% of the lesson for 5% of the effort. Growth step 3 in
`PROJECT-preflight-paa-netops.md`.

Adjacent: Packet Coders has an intro to Netpicker, so it sits in Sif's orbit.


## ★ Update 2026-10-05 — the corpus is open, the runner is not

Netpicker **2.8** (2026-10-05). Two corrections and one unlock.

⚠ **CORRECTION — these are not standalone pytest functions.** The `@low` / `@medium` /
`@high` decorators and the injected `configuration` / `commands` / `device` / `devices`
parameters are **Netpicker-supplied globals**. No import lines appear anywhere in their docs or
examples. There is **no documented way to run a rule outside the product**. Treat the earlier
framing of these as plain pytest as wrong.

⚠ **CORRECTION — the commercial wall is hard.** Foundation is free for unlimited devices for
*backup/search/automation*, but **Professional features including compliance validation are
capped at 10 devices.** Professional starts at **$7,500/yr**, Enterprise at **$33,500/yr**.
"Running the platform is optional" was right; it is now also effectively impossible.

★★ **THE UNLOCK: `netpicker/pytests-for-networking` is a public repo** — `CIS/`,
`CVEasy_examples/`, `Integrations/`, `tests/`, `EXAMPLES.md`. The assert bodies are plain
Python. Strip the decorator, supply the fixtures yourself, and they port in an afternoon.

⇒ **A free rule library, not a free runner.** This is **BC2** in
`PROJECT-preflight-paa-netops.md` — the strongest build candidate in the project, because the
corpus already exists and the missing piece is small.

`netpicker/netpicker-cli` is **MIT** and is the sanctioned external entry point
(`policy test-rule`, `policy execute-rules`, `compliance log`), configured via
`NETPICKER_BASE_URL` / `NETPICKER_TENANT` / `NETPICKER_TOKEN` — but it is an API client; the
engine stays proprietary. Its README states it also ships an MCP server.

**On the Nautobot-version gotcha above:** upstream Nautobot is now **3.2.6**, so the plugin's
3.x requirement is met upstream — ⚠ but the work Staging stack is still **2.3.8 / GC 2.x**.
The `netbox` fixture remains **pynetbox**, so the NetBox-vs-Nautobot divergence is unchanged
and still unverified.

**What was evaluated as an open replacement and rejected:** Golden Config compliance (prefix
line-matching only — no conditionals, no numeric comparison, no negation, no cross-device
relationships) · SuzieQ (stale, asserts not user-definable) · NUTS (dormant since 2024) ·
netlint (dead since 2021, GPL-3.0) · pyATS/Genie (healthy, Apache-2.0, but Linux/macOS wheels
only and ~25% closed-source Cython) · NAPALM `compliance_report()` (works, but YAML rules).
★ **The shape that works: plain pytest asserts over `ciscoconfparse2` / `hier_config` for
text, and pybatfish for semantics.**

