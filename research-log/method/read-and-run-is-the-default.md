# Assume read-and-run until the repo proves otherwise

**Added 2026-10-05. Four independent arrivals, and it is now a prior rather than a finding.**

## The rule

**A book or tutorial that claims hands-on labs is read-and-run until its repository proves
otherwise. Check the repo before buying, and check the step verbs, not the marketing.**

The diagnostic is cheap and decisive: read the first five numbered steps of any lab. If they
begin *Deploy*, *Run*, *Clone*, *Apply* — it is read-and-run. If they begin *Write*, *Edit*,
*Modify*, *Create* — it is a build.

## The evidence

| Source | What was promised | What the steps said |
|---|---|---|
| **Baksh**, *Building AI Agents for Network Operations* | hands-on labs | twelve "Run the…"; **zero** instances of edit / modify / create a new |
| **Albada**, *Building Applications with AI Agents* | companion repo | repo incomplete, no directions; reads as exam prep |
| **Levan**, *Agentic-AI-The-Hard-Way* | "production-grade… the hard way" | "1. Deploy the broken app" / "2. Deploy kagent" |
| **Reichert et al.**, *Agentic AI for Platform Engineering* | per-chapter labs | `./setup-cluster.sh`, `./verify.sh`; code for 4 of N chapters |

Four different authors, three publishers, one pattern. `method/same-author-is-one-source.md`
applies and passes.

## Why it matters

It is not a complaint about the genre — read-and-run is a legitimate format for conveying an
architecture. The failure is **mistaking it for practice.** Running someone's script teaches
what their system does; it does not teach you to build one.

⇒ Corollary, and the reason this rule earns its place: **a book is input, not a rep.** The
typing reps have to come from somewhere else — a rebuild (Case 02), or an instructor-led
build-along (the O'Reilly Lunch & Learn, Oct 2026).

## Second-order note

Two of the four repos were **incomplete** (Albada; Reichert ch8 referenced with no directory).
Repo completeness is worth checking separately from lab style — a complete read-and-run repo is
more useful than an incomplete build-it-yourself one.

