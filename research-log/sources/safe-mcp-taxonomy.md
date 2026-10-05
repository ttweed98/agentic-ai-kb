# SAFE-MCP — the attack taxonomy

- **question:** Q4 (adversarial pass) — this is the thing that unblocks it
- **captured:** MISS — https://www.safemcp.org/ · https://github.com/SAFE-MCP/safe-mcp
- **read:** 2026-09-28

**Security Analysis Framework for Evaluation of MCP and AI Agents.** Hosted by the **Linux
Foundation**, supported by the **OpenID Foundation**, initiated by Astha.ai. Apache-2.0 and
CC-BY-4.0.

MITRE-ATT&CK-shaped: **14 tactics, 80+ techniques.** Tactics include Initial Access,
Execution, Persistence, Privilege Escalation, Defense Evasion, Credential Access, Discovery,
Lateral Movement, Collection, Exfiltration, Impact, Command & Control, Resource Development,
Reconnaissance. Each technique carries mitigation guidance, detection guidance, and MITRE
ATT&CK mappings where applicable.

Technique IDs are citable:

- **SAFE-T1001** — Tool Poisoning (High). "Adversaries may poison or manipulate MCP tool
  definitions to execute unauthorized actions."
- **SAFE-T1102** — Prompt Injection (Critical).
- **SAFE-T1201** — MCP Rug Pull Attack (High).

## Why this matters more than its size suggests

**Q4 has been named four times and never run, partly because "what attacks?" was unanswered.**
This answers it, and it converts Q4 from an anecdote into a **coverage matrix**: technique ID
× {blocked, detected, out-of-scope, unknown}, with the specific code path cited for every
"blocked."

It also repairs the exact methodological weakness found in the OAP paper. Their attackers
were self-selected people who preferred social engineering; a technique catalogue gives
*systematic* coverage rather than whatever a crowd felt like trying. See
`method/hold-the-policy-constant.md`.

**Cost: about a day of reading and writing, no build.** It is the cheapest publishable
artifact currently available, and it fits beside MCPA study rather than displacing it.
This is milestone M0 in `PROJECT-preflight-paa-netops.md`.

## Cross-reference

Arbon's *Testing AI* ch13 (`sources/arbon-testing-ai-skills-repo.md`) names MCP security,
OWASP LLM Top 10, tool permissions and provenance — the same ground from a testing rather
than a security angle. Two vocabularies for one problem; worth mapping once.

