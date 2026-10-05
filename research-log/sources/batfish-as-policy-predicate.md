# Batfish — the policy predicate nobody else can copy

- **question:** Q4
- **captured:** MISS — https://batfish.org/ · https://github.com/batfish/batfish
- **read:** 2026-09-28

Network configuration analysis without touching devices: reachability, ACL semantics, and
whether a *planned* change breaks anything. 1.5k★, 284 forks, `pybatfish` Python client.

**Arista EOS is supported** (also Cisco all platforms, Juniper, Cumulus, SONiC, F5, Fortinet,
PAN, Nokia, AWS, Azure; limited: Aruba, Dell Force10, Foundry).

⚠ **Verify maintenance cadence** — last-commit date was not obtainable remotely and the
project has had quiet stretches. One look at the commit graph settles it.

## ★ Why it matters here

Every generic agent-authorization project can do rate limits and allowlists. **None of them
can say "DENY — this change breaks reachability from X to Y."** That predicate requires a
network model, and it is the one thing in this whole survey that a security vendor cannot
copy from us.

Neither OAP nor Dogwood nor Guardian has any notion of **desired state**. Netpicker computes
it from rules. We have intended-config generation already. A policy that says "deny any
change that moves the device away from intended config" is only expressible if you own a
source of truth.

Promoted from growth step 4 to **the differentiator** — milestone M5 in
`PROJECT-preflight-paa-netops.md`, and the basis of LinkedIn post 4.

## Related, held

**SuzieQ** — network state database with time travel and pandas-based asserts. An alternative
oracle to Netpicker. **Skip for now**; it substitutes for something we already have two
candidates for. Name recorded only.


## ★ Update 2026-10-05 — the maintenance question is CLOSED, and it ships an MCP server

The ⚠ above asked for a cadence check. Answered:

- **Batfish `v2026.08.27`**; **pybatfish `2026.9.17.3748`** (2026-09-18); PR #995 merged
  **2026-09-29**. Apache-2.0. **AWS-managed open source** since the Intentionet team joined AWS.
- ⚠ Java-side majors are roughly annual and irregular (2023-12, 2024-07, 2025-07, 2026-08).
  **Do not read that cadence as abandonment** — pybatfish ships far more often.

★★ **It now ships a first-party beta MCP server.** `pip install 'pybatfish[mcp]'` →
`batfish-mcp` (`pybatfish/mcp/server.py`). 36 tools at release, more since: `generate_dataplane`,
`get_lpm_routes`, `get_edges`, `get_server_info`. `run_traceroute` and
`run_bidirectional_traceroute` gained **`trace_format: text | structured | summary`** —
structured JSON traces mean hop extraction with **no regex parsing**.

⚠ Release notes: *"Tool names and parameters may still change."*

**Consequence for M5:** the predicate is not something to build. It exists, it is Apache-2.0,
and `pybatfish/mcp/server.py` is also a working reference implementation of a network-domain
MCP server — worth reading for its own sake. The differentiator is unchanged; the work becomes
integration rather than construction.

★ Also in v2026.08.27: Nokia SR OS (MD-CLI), Broadcom FASTPATH, experimental Azure, AWS Direct
Connect modelled end-to-end, multi-arch Docker. ⚠ **Breaking:** JDK **21** minimum (was 17);
`Next_Hop_IP` / `Next_Hop_Interface` columns **removed** from route queries.

**SuzieQ — now a decided NO, not "held."** 0.24.0 released **2025-05-09**; 17 months stale,
114 open issues, 21 open PRs. Its `assert` is a **fixed built-in on four tables
(Interface/BGP/OSPF/EVPN) and is not user-definable.** Usable as a state source via its query
API; not a rule engine. Close the thread.

