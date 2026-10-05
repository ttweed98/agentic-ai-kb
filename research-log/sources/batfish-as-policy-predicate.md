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

