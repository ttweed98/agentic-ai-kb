# Alake — "Harness Engineering for AI Agents" (O'Reilly live)

- **question:** Q5 primary (the LangChain held-constant result); Q2, Q4
- **captured:** local — course PDF, 85 slides, 8 parts
- **read:** 2026-09-29 (parts 1–3 read closely; parts 5–8 UNREAD)
- **read this when:** you need the harness anatomy, the "scaffolding is where the points are"
  evidence, or the 2025–26 incident case studies

Richmond Alake — Director of AI Developer Experience at Oracle; ex-MongoDB AI/ML DevRel;
coined "Memory Engineering"; creator of MemoRizz. Course taken live 2026-09-29.

## Core frame

**agent = model + harness.** The harness is "the software layer surrounding a language model
that turns raw capability into reliable, autonomous action."

Three dictionary senses, mapped: **control the animal** (the model) · **keep the person safe**
(the user) · **harvest the power** (capability). Three eras: physical straps → the software
*test harness* → agent engineering.

## ★★★ The LangChain result — the citation `preflight` needed

LangChain took a coding agent from **Top 30 to Top 5 on Terminal-Bench 2.0** —
**52.8% → 66.5%, +13.7 points, 89 tasks** — with **GPT-5.2-Codex held fixed**, changing only
system prompt, tools and middleware. Failure modes were found in LangSmith traces.

**That is `method/hold-the-policy-constant.md` done properly, on the harness axis.** Model
pinned, one thing varied, measured.

## ★★★ Their top failure mode

> "the agent wrote a fix, **re-read its own code, decided it looked fine, and stopped**"

Self-validation, in production, with a benchmark delta attached. That makes **four independent
arrivals** — see `method/separate-the-validator.md`. This is no longer a converging finding.

## ★★ Seven building blocks ("different harness, same skeleton")

01 system prompt & policy · 02 tools & tool execution · 03 sandbox / execution env ·
04 context & memory · 05 control loop & orchestration · 06 verification & guardrails ·
07 observability & feedback

> "Brands differ on **emphasis and packaging**, not on anatomy."

⇒ **Third harness anatomy** to hold against the finance book's six building blocks and Arbon
ch17–18. Three authors; find what's in all three vs what's one author's taste.

⚠ **The deck is internally inconsistent.** The content overview says *six runtime components*
(observation interface, context manager, action interface, state & artifacts, verify &
guardrails, control loop) plus long-horizon layers (planning, memory, tool use, feedback
control). The landscape section gives the seven above. **Ask which is canonical.**

## ★★ The harness as a search space

A modular harness turns tuning into **search** — give each component a set of configurations
and improving the agent becomes finding the best point. "A well-posed optimisation problem,
not guesswork."

★ **Stanford's Meta-Harness searches harness designs over a fixed model — "scaffolding is now
a first-class optimisation target."** CHASE THE PAPER. It is the automated form of the
LangChain result and a direct input to Q1.

## ★★ Incident case studies — each maps to an absent component

**July 2026, the eval escape.** Autonomous agents escaped test environments and reached real
systems. OpenAI's agents chained vulnerabilities to reach Hugging Face. Anthropic's review of
**141,006 runs** found Claude reached the open internet through a misconfigured partner
environment (Irregular), then compromised three real organisations.

> "closer to a **harness and operational failure** than a model alignment failure."
> — Anthropic, on the Claude incidents

★ And the detail that is ours: **"the eval harness had no live tripwire — monitoring caught it
after the fact."** That is the real-time vs after-the-fact gap E1 targets, stated by a lab
about its own incident.

**March 2026, Meta.** An engineer asked Meta's internal agent for help; the fix it proposed
exposed sensitive company and user data to unauthorised staff. ~2 hours exposed, Sev 1.

> **"The model never executed anything. Its instructions did the damage."**
> "advice is an action too — a confidently wrong instruction is a failure mode the harness has
> to catch"

⚠ **This is a hole in `preflight`'s threat model.** The gate covers tool calls, not
recommendations. An agent that tells a NetOps engineer to run a command causes the change
without ever calling a tool. **Decide: in scope, or explicitly out.**

**January 2026, OpenClaw.** Open-source personal agent goes viral — your machine, your
messages, your credentials, plus a community skills marketplace. Maximal capability, minimal
harness. Early-2026 scans found **135,000+ control planes reachable on the public internet**
(the localhost trust assumption collapsed behind reverse proxies). The **ClawHavoc** campaign
seeded **1,000+ malicious skills**, harvesting SSH keys, cloud credentials and browser cookies
on first run. One crafted email, calendar invite or webpage could steer the agent into leaking
secrets.

> "every missing strap became an incident within weeks"

Missing straps named: **sandbox, egress policy, skill signing, approvals.**

**April 2025, GPT-4o sycophancy.** A thumbs-up/down reward signal outweighed the signal holding
sycophancy in check.

> "Offline evals and A/B tests looked good. Expert testers felt it was 'off' — but there was
> **no deployment eval for sycophancy**, so the vibe check lost to the metrics."

Fix: behaviour issues made launch-blocking. ⇒ **"a behaviour you don't measure is a behaviour
you can't hold in check."**

## ★ Fable 5 / Mythos 5 — access control as harness engineering

One underlying model, two harnesses. Fable 5: generally available, with classifiers routing
risky cyber and bio queries to a weaker model. Mythos 5: the same model with safeguards lifted,
restricted to vetted partners.

> **"when a bypass surfaced, the model stayed — the harness was upgraded"**

Also: 2019 GPT-2's staged release — "the first famous harness was the release process itself."

## Models converging

Artificial Analysis Intelligence Index v4.11: Kimi K3 (57) trails only Fable 5 (60) and
GPT-5.6 Sol (59). SWE-Bench Verified went ~60% → near-100% in a year. Stanford AI Index: the
lead keeps trading hands, ~2.7% as of early 2026.

> "when the model stops being the differentiator, **the system around it becomes the place
> performance is won**"

⇒ Supports the read in `sources/openai-agents-api.md`: the harness is free, the policy layer
isn't.

## Other frames worth keeping

**Agentic spectrum:** chat interface → RAG agent → agentic RAG → LLM workflow → autonomous
agents. Human-directed fixed path → model-directed open goal. "The further right, the thicker
the runtime."

**Four categorisation axes**, each resolving to a harness decision: position on the spectrum
(who owns control flow) · functional role (which tools, checks, verifiers) · environment (the
observation and action interfaces, the sandbox) · architectural pattern (orchestration,
communication, shared state).

**Harness landscape table** by task domain / access / surface: Claude Code, Codex, Devin,
OpenHands, Deepagents, AutoGPT, Manus, OpenClaw, Hermes, Pi, MemoRizz. Choose by **your single
biggest constraint** — control & data / delegation / integration / model freedom / memory &
continuity / breadth — not by "best."

**Reference architecture** includes MCP servers in the tool registry, human-in-the-loop with
"approvals · interrupts · guardrails · hard limits", sandbox + bash tool, a memory layer
(working / episodic / semantic / procedural), a semantic layer, and filesystem / git / database
durable storage.

## Still unread

Parts 5–8: application mode thinking, the memory-first harness, assistant mode implementation,
shipping to production (Next.js on Vercel + Oracle AI Database on OCI + LangSmith). The
memory-first section is the part most likely to touch Case 03.

## Questions to put to him

1. Six components or seven — which is canonical, and why the difference?
2. Stanford Meta-Harness — which paper?
3. How does a harness gate **advice** rather than actions (the Meta incident)?

