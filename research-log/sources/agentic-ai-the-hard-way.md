# AdminTurnedDevOps / Agentic-AI-The-Hard-Way

- **question:** Q4 (adversarial) · agent runtime
- **captured:** MISS — https://github.com/AdminTurnedDevOps/Agentic-AI-The-Hard-Way
- **read:** 2026-10-04 — **front page and file listing only; lab files NOT read**
- **read this when:** the Kubernetes block comes up (placed there 2026-10-04)

⚠ **SAME AUTHOR AS `sources/agentgateway-model-object.md`** — Michael Levan. One source
between them.

Work in progress. Started January 2026, 56★, 13 forks, no releases. **71.4% HCL / 28.6%
Python** — this is a Terraform and Kubernetes repo with agents on top.

```
autonomous-k8s-engineer   platform-engineering-assistant
the-observer              isolated-agent
the-broken-apps           k8s-terraform
workstation-setup         prerequisites.md
```

## Provenance note — two reads disagreed

A first read of the page characterized the labs as build-it-yourself. A second read, pulling
actual step text, found the Platform Engineering Assistant lab opening **"1. Deploy the broken
app"** / **"2. Deploy kagent."** The second is trusted: quoted steps beat a characterization.
⇒ read-and-run, see `method/read-and-run-is-the-default.md`.

## What it has that we don't

**The runtime half** — sandboxing, AI gateway, scalability, security. The "day 2" territory
missing from every book so far. `isolated-agent` and the gateway material sit on Q1 position 3
and on `preflight` M1.

`the-broken-apps` is a fault-injection set — structurally what `fault-scenarios` is for the
cEOS fabric. Worth a design comparison.

## Placement

**Kubernetes block, next year.** The Kubernetes tax that makes it expensive now is zero once
Poulton and the Kubernetes book are done; at that point it stops teaching pods and starts
teaching agent runtime.

⚠ **Before committing time next year, check the commit history** — nine months old and WIP, so
it will either have matured or gone stale.

