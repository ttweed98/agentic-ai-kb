# Netmiko Guardian + netmiko_mcp — the OSS/commercial split

- **question:** Q1 (where control lives), Q4
- **captured:** MISS — https://netmiko.io/ · https://github.com/ktbyers/netmiko_mcp ·
  https://flock9.io/
- **read:** 2026-09-26
- **see also:** `/areas/netmiko-mcp-comparison.md` (the earlier deep read of the repo)

## Netmiko Enterprise / Guardian

Kirk Byers + **Flock9** joint venture, announced 2026-09-10. Enterprise Support from
$10,000/year. **Guardian** — "In development," "Early access opening soon" — described as
"the controlled layer between AI clients and your network," pitched as *"deterministic
execution, separated from the AI"* with a *"full audit trail of requests and actions."*

Kirk on the page: *"The Netmiko open-source library itself will largely be unchanged by all
of this."* The only code linked is the MIT library: *"Free, and staying that way."*

## netmiko_mcp (the OSS half)

Apache-2.0, v0.2.0 (2026-06-29), 173 commits, 2★/1 fork. Seven tools:
`send_show_command`, `send_show_command_to_group`, `list_devices`, `list_groups`,
`list_device_outputs`, `read_device_output`, `ping`. stdio + Streamable HTTP. Default-deny
command allowlist, per-request validation, credentials excluded from inventory.

★ **Imports `from mcp.server import MCPServer` ⇒ SDK v2 ⇒ tracks 2026-07-28.** Soares pins
v1 deliberately. **This is the only MCP codebase we own that targets the exam revision** —
a better D3 wire reference than the course repo. Cloned at
`~/projects/KBYERS_Netmiko_MCP/netmiko_mcp`.

## ★ The join

The README states its own limitation: *"Controls in Netmiko-MCP are a best-effort layer and
should not be your only line of defense,"* and recommends pairing with AAA/TACACS+ and a
read-only service account **for independent authorization and audit trails.**

**Guardian is that sentence as a product.** Open core: the free tool names its own gap, the
paid layer fills it. Honest split, but it means the design question has now been answered
commercially.

## ★★ Q1 reframe — better than narrow-vs-broad

The variable is **where control lives**:

| Location | Who |
|---|---|
| In the tool | our build; Anthropic's intent-shaped tools |
| In the server's data | netmiko_mcp's allowlist file |
| In a gateway in front | Guardian; Caldeira's IBAC layer; agentgateway |

Composable, not exclusive. Full five-position version in
`knowledge/q1-where-control-lives.md`.

## Audit

Kirk logs the **invocation**. Our `audited` context manager logs the **outcome on every
path**, including refusals, via `finally`. Guardian's pitch says "requests **and actions**" —
actions means outcomes ⇒ the product is moving toward the stronger guarantee we already have.

## Flock9 context

Flock9 = **Slurp'it** (discovery: devices, interfaces, IPs, VLANs, topology) + **Netpicker**
(validation: running state vs policy, compliance, CVEs). Guardian is the third leg.

Maps 1:1 onto our world: Slurp'it ≈ Nautobot SSoT, Netpicker ≈ Golden Config compliance,
Guardian ≈ the allowlist gate + audit record. Discovery + validation + gated execution is the
triad our build assembles by hand.

## Open

**Is Guardian MCP-based or proprietary?** No MCP mention on netmiko.io. Netpicker's release
notes show its AI as a BYO-key **OpenAI-compatible** assistant (v2.5 Nov 2025, v2.7 May 2026,
v2.7.6 27 Aug 2026) with **zero mentions of MCP or Guardian** across the whole history — the
pre-MCP shape. Rick Donato's 3 Sept 2026 write-up of the assistant describes capability but
discloses no architecture. Evidence leans **not MCP**; inference, not fact.

Possible: netmiko_mcp is the MCP half and Guardian the proprietary half, same author.

