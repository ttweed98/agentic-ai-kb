# Reichert, Duarte & Nagase — Agentic AI for Platform Engineering

- **question:** Q1 (policy languages — OPA) · platform/portal shape
- **captured:** MISS — https://github.com/tiagoReichert/ai-driven-platform-engineering-book
- **read:** 2026-10-05 — **repo listing only. Book not read, TOC not obtained**
  (Amazon robots-blocked; Packt product page did not surface)
- **read this when:** after the Kubernetes study (placed there 2026-10-05), or sooner if the
  Koch work turns toward a platform/portal story

*Agentic AI for Platform Engineering: Rethinking Developer Platforms with Agentic AI, RAG, and
Autonomous Operations* — Packt, ISBN 9781806386659.

⚠ **The repo is under the author's own account at a DIFFERENT TITLE** —
`tiagoReichert/ai-driven-platform-engineering-book`. Retitled in production. Searching
`PacktPublishing/Agentic-AI-for-Platform-Engineering` finds nothing.

## Repo state

0 stars, 0 forks, 0 watchers. Code for **chapters 4, 5, 7 and 9 only**; chapter 8 is referenced
in the docs with **no directory** — the same incomplete-repo pattern as Albada.

```
00-cluster-setup/  chapter-04/  chapter-05/  chapter-07/  chapter-09/
```

- **ch4** — RAG pipeline (Postgres + pgvector) and an MCP server
- **ch5** — Backstage + GitOps with an AI chat assistant and Kubernetes actions
- **ch7** — production-grade operational agent
- **ch9** — ★ living memory graph with an MCP server

Shared kind cluster, `./setup-cluster.sh`, `./verify.sh` ⇒ read-and-run.

Stack: kind / EKS, ArgoCD, Backstage, **OPA**, Postgres+pgvector, Bedrock (Claude) or Anthropic
API, Docker, shell.

## ★ Two things worth lifting out before the book itself

**OPA is in the stack, and Rego is missing from the Q1 policy-language table.** See the Q1
append of 2026-10-05.

**Chapter 9's memory graph over MCP is structurally what Case 03 wants to be** — Nautobot data
as a graph, exposed to an agent. Worth reading as a design comparison before Case 03 is built,
independent of the rest of the book.

## Placement

**After the Kubernetes study.** ⚠ But the subject — platform engineering, agents, policy, an
internal developer portal — is the closest of any source to the Koch pivot of 2026-10-01. If
that work turns toward a platform/portal story this moves up, and a Packt review copy is
probably available for the asking.

