# Hermes (Nous Research) — the self-authoring skills question

- **question:** Q5 (does a self-written skill bind?); Agent Skills / MCPA Stage 3
- **captured:** MISS — https://hermes-agent.nousresearch.com/ ·
  https://github.com/NousResearch/hermes-agent
- **read:** 2026-09-29 — ⚠ **marketing page only, repo fetch blocked**
- **read this when:** thinking about skill authorship, or about accumulating-skill agents

MIT licensed, Nous Research. Appears in Alake's harness landscape table as
"personal · self-improving · open source · message-driven", under **model freedom** and
**memory & continuity** in his choose-by-constraint slide.

## What the site claims

Persistent memory that "learns your projects, **auto-generates skills**, and never forgets how
it solved a problem." Messaging gateway across Telegram, Discord, Slack, WhatsApp, Signal,
Email and CLI. Subagent delegation with **isolated subagents having their own conversations,
terminals and Python RPC scripts**. Sandboxing across **local, Docker, SSH, Singularity and
Modal**, with container hardening and namespace isolation.

⚠ **Unverified:** the skill-generation mechanism, MCP support, benchmarks, stars, commits,
release date. The repo fetch was blocked. Paste the README for a real read.

## ★★ Self-created skills is a sharper form of Q5

Q5's open sub-question is *does **data** bind?* This asks something harder:

**Does a skill the agent wrote for itself bind?**

A self-generated skill is prose the agent authored, stored, and later retrieves as instruction.
If human-written prose doesn't bind, does self-authored prose? And does own-authorship make it
more likely to be followed — or just more likely to be self-consistently wrong?

That last clause connects to `method/separate-the-validator.md`: an agent writing the rule it
will later be judged against is the same circularity in a different place.

## ★ Agent Skills from the other side

MCPA Stage 3 covers skill authoring by humans. Here authorship is automated. **How it decides
what deserves to become a skill** would teach more about descriptions and triggering than
another hand-written example.

## ⚠ The security question, straight from Alake's deck

**Hermes and OpenClaw are the same category** — open-source, self-hosted, personal, with skills
that accumulate. Alake used OpenClaw as the cautionary tale: 135,000+ control planes on the
public internet, ClawHavoc seeding **1,000+ malicious skills** that harvested SSH keys and cloud
credentials on first run, and a named missing strap of **skill signing**
(`sources/alake-harness-engineering-course.md`).

**Auto-generation means no human in the authoring loop at all.**

⇒ **Check first:** does Hermes sign, sandbox or review generated skills before they execute,
and where do they live? If they are just files the agent writes and later reads, it has
OpenClaw's exposure plus a generator attached.

Not a prediction — the thing to check.

## Verdict

**Look at two things, don't adopt.** The skill-generation mechanism (novel, and a Q5 question),
and the five sandbox backends with namespace isolation (more serious than most open-source
harnesses bother with).

Otherwise out of scope: personal-assistant shape, messaging-first, nothing pointing at network
operations.

