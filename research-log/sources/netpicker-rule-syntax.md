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

