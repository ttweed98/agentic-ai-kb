# START HERE — paste this first in any new chat

**Last updated 2026-10-10 — read the "CURRENT STATE 2026-10-10" section near the end; it supersedes this top section where they differ.** Written 2026-10-05. This is the state file: what is active, what is decided, what is
waiting. `INDEX.md` is the router for *what each file says*; this is the router for *what to do
next*. Read this first, then ask for two or three files from `INDEX.md`.

---

## The one active thread

★ **MCPA reading. Nothing else is in progress.**

Standing order, stated flatly and not up for renegotiation: *"MCPA is first. Nothing will reset
it."* The Koch AI push of 2026-10-01 raised the urgency of finishing it; it did not change the
order.

Weighting if time is short: **D3 base protocol 26%, D4 authorization 24%** — half the exam
between them. D1 16%, D5 20%, D2 14%.

⚠ Do **not** propose reordering the plan around a Claude certification, a new book, a new
course, or a new repo while MCPA is open. That question reopens when MCPA is done, or when his
Koch email successfully registers at the Anthropic Partner Academy — whichever comes first.

---

## What happens after the reading

**M0 of `preflight`** — decided 2026-10-05. See `PROJECT-preflight-paa-netops.md` §6.1.

M0 = the SAFE-MCP coverage matrix. 14 tactics, 80+ techniques scored against the *existing*
Case 01 build: technique ID × {blocked, detected, out-of-scope, unknown}. No build, ~1 day.
Done when every technique has a verdict and a one-line reason, and **every "blocked" cites the
code path that blocks it.**

It produces LinkedIn post 1 and the baseline M3's experiment is measured against.

---

## Decisions — made and open

### Made
| # | Decision | When |
|---|---|---|
| §6.1 | **M0 runs after the MCPA reading**, not beside it | 2026-10-05 |
| — | Levan's `Agentic-AI-The-Hard-Way` → the Kubernetes block, next year | 2026-10-04 |
| — | Reichert's *Agentic AI for Platform Engineering* → after the Kubernetes study | 2026-10-05 |
| — | SuzieQ → **decided no**, not "held" (stale, asserts not user-definable) | 2026-10-05 |

### ★ Still open — these are his calls, do not make them for him
| # | Decision |
|---|---|
| §6.2 | **Is M1 in?** If no, park the whole project — without it `preflight` competes with the exam instead of serving it |
| §6.3 | Repo name — `preflight`, or his |
| §6.4 | E2's three arms from the start, or code-only in v0 |
| §6.5 | Cedar or asserts as M4's primary arm. ⚠ New input 2026-10-05: `cedar-policy/cedar-for-agents` generates Cedar schemas **from MCP tool definitions**, which is the strongest argument yet for Cedar |
| — | Case 03's graph — stays separate, or folds into `preflight` as a blast-radius tool |

---

## What already exists (and gets forgotten)

**Case 01 is complete end to end** — built, measured (20 scored runs, four conditions),
published (`github.com/ttweed98/agentic-ai-labs`, MIT), posted, and demoed live to senior devs
on 2026-08-25. Five narrow typed tools on his own MCP server: `list_devices`,
`get_device_status`, `check_interfaces`, `check_routes`, `check_bgp_neighbors`. 20 tests green.
eAPI, not SSH. Two-spine/two-leaf/two-host cEOS Containerlab fabric, four deliberate fault
states.

★★ **Three things `preflight` needs are already on disk:**

1. **M1's reference implementation** — `ktbyers/netmiko_mcp`, cloned at
   `~/projects/KBYERS_Netmiko_MCP/netmiko_mcp`. It imports `from mcp.server import MCPServer`
   ⇒ SDK v2 ⇒ **tracks the 2026-07-28 revision the exam tests.** The only MCP codebase he owns
   that does. A better D3 wire reference than the course repo.
2. **M2's test rig** — Soares module 04 `tests/test_pre_tool_hook.py`. Same artifact name as
   OAP's `before_tool_call` hook. Already cloned.
3. **Four of OAP's five properties** — the `audited` context manager is auditability; the
   allowlist gate is determinism and fail-closed. ⚠ Missing: **non-bypassability as a stated
   guarantee**, and ESCALATE (deliberately out of scope in M2 v0).

⇒ **M1 and M2 start from working code, not a blank page.**

---

## Running in parallel (not blocking)

- **O'Reilly "Lunch & Learn: Claude Edition"**, Kesha Williams — **2026-10-07 to 11-03**,
  weekdays 12pm ET, 45 min, 20 sessions. Build-along to a GitHub Action that reviews PRs.
  ⚠ **Public repo only — it ingests a codebase.** Suggested target: `agentic-ai-book-labs`.
- **Koch's AI push** opened 2026-10-01, led by the security manager. Skilling up is top
  priority at work. See `/areas/koch-ai-initiative.md` in memory.

---

## Standing constraints — non-negotiable

- ★ **No Koch data, ever**, in anything public. Book and course labs are public and must be
  reproducible by anyone. Koch capstones are internal only.
- ★ **Attack work (`security-lab/`, E1, E3) is NUC only.** Never the work laptop.
- ★ **Never point third-party hosted tooling (GitMCP etc.) at Koch internal code.**
- ★ **No dumps, no exam item pools, ever.** Self-tests are open recall, not multiple choice.
  *"That is a violation and will not be tolerated."*
- Personal API keys stay personal.
- Claims in posts carry their run counts. n=1 is an anecdote and gets labelled one.

## How he works

- **File uploads to Claude arrive empty.** Paste terminal output inline as plain text.
- **Deliverables come as a bundle**, split in the repo:
  ```bash
  cd /home/ttweed98/projects/agentic-ai-kb
  awk '/^===== FILE: /{f=$3; next} f{print > f}' /mnt/c/Users/tonyt/Downloads/<bundle>.md
  ```
  ⚠ Check `/mnt/d/Downloads/` too — downloads have landed there.
- **Append, never rewrite.** A whole-file rewrite of `q1` on 2026-10-02 silently dropped 57
  lines; cross-references don't read as content. Existing files get an appended dated section.
- **Mentor mode applies to STUDY only, not work.** On work tasks, write the files and hand him
  things that run.
- **For Python: Claude writes the code in sections, he types them, then they review together.**
  He is not yet composing Python unaided. Do not ask him to.

---

## Where things are

| | |
|---|---|
| KB repo | `~/projects/agentic-ai-kb` (private, `ttweed98/agentic-ai-kb`) · research in `research-log/` |
| Case 01 | `github.com/ttweed98/agentic-ai-labs` (public, MIT) |
| Case 02+ | `github.com/ttweed98/agentic-ai-book-labs` (public) |
| Build plan | `research-log/PROJECT-preflight-paa-netops.md` |
| Toolset state | `research-log/TOOLSET-2026-10-05.md` |
| Findings owed to the build | `research-log/build-queue.md` |
| File router | `research-log/INDEX.md` |

---

## Open debts, in order of leverage

1. ⚠ **Capture debt** — every source note carries `captured: MISS`. Usually cosmetic; **not
   for the OTel GenAI conventions, which have no tagged release and will move.** Capture that
   one and the Dogwood docs before M4/M5.
2. **`questions.md` status board** — `STATUS-BOARD-UPDATE-2026-09-28.md` may never have been
   merged. Check.
3. **`method/experiment-metadata-ablation.md`** — 15 minutes, still unrun, still blocking Q1,
   still the cheapest evidence in the KB.
4. **`method/unit-of-evidence-five-runs.md`** — amendment pending a power analysis. Read
   *Testing AI* ch03–04 before designing E1's cell count.
5. **SAFE-MCP ↔ Arbon ch13 vocabulary map** — one extra column in M0's matrix, one hour,
   and nobody else writes it.
6. **Demo artifacts from 2026-08-25** (`case-01-topology.md/.svg`, `agent-architecture.md/.svg`)
   were made but may still be uncommitted. Check.

---

# ADDENDUM 2026-10-05 — what the first version left out

Appended, not rewritten. Where this section and the text above disagree, **this section wins**.

**Two lines above are superseded:**

- *"MCPA reading. Nothing else is in progress."* → MCPA is still the one active thread, but it
  is **five workstreams, not one**. See "MCPA — the five workstreams" below.
- *"Self-tests are open recall, not multiple choice."* → true for the **audio episodes** only.
  The practice bank is multiple choice and is in use. The no-dumps rule is unchanged: no real
  exam items, ever, and the sealed mocks are taken once, unseen.

---

## MCPA — the five workstreams

Exam bought 2026-09-21 (Linux Foundation). **90 minutes.** 2026-07-28 spec. 17 competencies.
Eligibility runs 12 months from purchase, one retake. He wants it **quickly** — the Koch AI
push raised the urgency. Time available: **2 hours every weekday at the keyboard**; driving
hours are audio only.

### 1. Reading — the official documents
- Source of truth: `modelcontextprotocol.io/llms.txt`. ⚠ Every URL must contain
  `/2026-07-28/` — the site serves six versions with identical page titles. Append `.md` to
  any page for clean Markdown.
- Local clone of the spec repo, pulled daily.
- Done so far: the McCaffrey AAIF post, the intro page, **Build a server** and **Build a
  client** (both built and run — see workstream 5).
- Supplementary, not primary: `rohitg00/ai-engineering-from-scratch` MCPA track at
  `~/projects/MCP_Course/ai-engineering-from-scratch`. ⚠ He finds its lesson prose unreadable.
  Use it as a **coverage checklist**, not as reading.
- Stratis, *AI Agents with MCP* (O'Reilly) — on the plan, **placement not decided, his call.**
  Chapter 3 checked: written to 2026-07-28.
- ⚠ **Where he is in the reading is not recorded here.** Ask him; do not assume.

### 2. Audio — the driving series
- Eight episodes, 0 through 7. Episode 0 = what changed in 2026-07-28. Episodes 1–6 = the five
  domains. Episode 7 = the cram, 63 recall questions.
- Piper, voice `en_GB-alba-medium`. Lives in the `ai-information` repo under `audio/`.
- He has been through the set several times. **Episodes 4 and 5 are on repeat.**
- Episode rules: official wording and a plainer wording both kept, the official one labelled;
  every example must be an actual instance of the concept; each episode closes with a recap by
  domain and open-recall questions.

### 3. Practice tests
- Practice page: https://claude.ai/artifact/J2FBPWBouyCvfiKAAB8SJV — **362 questions**.
  Modes: quiz, flashcard, sealed mock, review, progress. Filters: domain, source, level, pool.
- Sources in the bank: purchased set 1 (`MCPA-v1.0.pdf`, 101 questions), the rohitg00 lesson
  quizzes (204), purchased set 2 (VXExam, 60 — de-duplicated and independently keyed).
- **Sealed:** the rohitg00 diagnostic and three 60-question mocks. Taken once, unseen, timed.
  Answers only after submitting.
- ★ **Standing rule:** anything written to an older protocol revision is **updated to
  2026-07-28 if it can be, removed if it cannot.**
- Levels: Foundation / Applied / Exam-depth. Easy first to build confidence. ⚠ The level score
  is **kept out of the readiness score** — confidence is not depth.
- Review tab: a wrong or flagged question links to the official page for that topic.
- ★ **Booking gate — four conditions, not a percentage:** 90%+ on two consecutive fresh sets ·
  no competency below 70% · every lab done unassisted · one full sitting inside 90 minutes.
  ⚠ These questions cannot be calibrated to the real exam; a high score alone predicts nothing.

### 4. The Soares MCP Bootcamp — the redo
- O'Reilly live course, Lucas Soares, 2026-09-22/23. Attended. **Could not code along live.**
- The redo: re-run every demo **on the NUC with the recording paused**. Personal keys are fine
  there; the security lab is fine there. Capture the "why did he do that?" questions.
- Repo: `~/projects/MCP_Course/mcp-course` (public: `github.com/EnkrateiaLucca/mcp-course`).
  Pull before starting. No licence visible ⇒ study and run, do not copy files into his repos.
- ⚠ The repo pins `mcp==1.30.0` — the v1 SDK, which speaks **2025-11-25**. Use it for the
  shape of the code; the spec decides what is on the wire.

| # | Module | What to do |
|---|---|---|
| 00 | Agents are loops | compare his loop with one written by hand |
| 01 | First MCP server | run it; inspect with the Inspector CLI |
| 02 | Agent SDK is an MCP host | note what the SDK does that a hand loop does not |
| 03 | Skills vs MCP | read `mcp-builder-skill/reference/mcp_best_practices.md` |
| 04 | Production shape | ★ `evals.py`, `tests/test_pre_tool_hook.py` — also M2's test rig |
| 05 | Deploy remote | the stateless-leaning demo, closest to 2026-07-28 |
| 06 | Defend and scale | ★ `security-lab/` — **NUC ONLY** |

### 5. The MCP servers built for learning
On record so far. **This list is his to extend** — add a row when a new one is agreed.

| # | Server | State |
|---|---|---|
| 1 | Official tutorial — weather server (Python) | ✅ built, debugged, tested in the Inspector. `~/projects/MCP_Course/official-tutorials/` |
| 2 | Official tutorial — client | ✅ built, ran against the Claude API |
| 3 | His own minimal server + his own minimal client, verified with the Inspector CLI | owed — the Academy-stage deliverable |
| 4 | Soares module servers (01, 04, 05, 06) | owed — workstream 4 |
| 5 | Lab Block 0 — bring the Case 01 server to 2026-07-28 (repo still pins `mcp 2.0`) | owed |
| 6 | Lab block — schemas and primitives: **resources and prompts**, not only tools | owed — ⚠ his known hole |
| 7 | Lab block — execution, errors, **elicitation** (D3, 26%) | owed |
| 8 | Lab block — security and authorization (D4, 24%) | owed |
| 9 | Lab block — observability and ecosystem | owed |
| — | After the exam: `preflight` M1 — the OAuth 2.1 chain in front of the server | queued behind MCPA |

- Blocks 5–9 are built on **his existing server and cEOS fabric**, not a throwaway lab.
- ★ **MCP goes into every lab** (his rule, 2026-09-21, revisable).
- ⚠ Inspector defaults to the **legacy** protocol. Set it to "modern" for 2026-07-28.
- Python method for all of these: Claude writes a section, he types it, they review it together.

### The agreed order
Claude Academy foundations (*AI Capabilities and Limitations* → *Building with the Claude API*
→ *Introduction to MCP*) → Soares redo → deferred Academy courses (*MCP: Advanced Topics*,
*Introduction to Agent Skills*, *Introduction to Subagents*) → MCPA lab blocks. Official
documents run alongside the whole way. ⚠ *MCP: Advanced Topics* teaches sampling and roots,
both deprecated in 2026-07-28 — read it as history.

Roadmap document: https://claude.ai/code/artifact/8143b7b1-a07e-4fe4-865a-3aac566dd538
(nine weeks, 35 two-hour sessions, original target sitting the week of 2026-11-23).

### ⚠ One question still open — his call
*"M0 runs after the MCPA reading"* does not say whether M0 comes **before or after the lab
blocks and the exam itself.** Earlier notes say the build resumes after MCPA. Ask; do not pick.

---

## O'Reilly — courses booked (company subscription)

Courses are **input, not reps.** None of these reorders MCPA.

| Date | Course | Instructor | State |
|---|---|---|---|
| 2026-09-22/23 | MCP Bootcamp: Building AI Agents with MCP | Lucas Soares | attended · **redo owed** (workstream 4) |
| 2026-09-23 → | AI Agents Bootcamp | Nicole Koenigstein | registered, expected to be recorded · ⚠ confirm whether watched |
| 2026-09-29 | Harness Engineering for AI Agents | Richmond Alake | booked · ⚠ confirm whether attended |
| date not recorded | Advanced Harness Engineering | Richmond Alake | booked · ⚠ confirm date |
| **2026-10-06** | Claude Certified Architect – Foundations (CCAR-F) crash course | Lucas Soares | booked — **tomorrow** |
| **2026-10-07 → 11-03** | Lunch & Learn: Claude Edition | Kesha Williams | signed up · weekdays 12pm ET, 45 min, 20 sessions |
| 2026-10-12/13 | AI Engineering Bootcamp | Ammar Mohanna | booked |
| self-paced | Python Functions and Classes | Deza and Gift | started |

Notes that travel with the table:

- **Koenigstein** — MCP alignment is thin; not exam prep. Value is tool governance and threat
  modeling (Q4) and evaluation (Q2). Her book *AI Agents: The Definitive Guide* is on the
  subscription: ch6 and ch12 for Q4, ch8 and ch9 for the eval thread.
- **Alake** — companion repo `RichmondAlake/agent_harness_course`. Its premise (memory
  allocation comes before every other harness decision) cuts against his memory-less
  five-tool build. Arrive skeptical.
- **CCAR-F crash course** — worth taking as a course. ⚠ It does **not** reopen the
  certification order; the Claude certs stay behind MCPA. Its practice-question pool collides
  with the no-item-pool rule ⇒ use any mock **once**, as measurement.
- **Lunch & Learn** — needs a personal Anthropic API key with spending limits, Python 3, a
  GitHub PAT, Claude Code by day 11, `jq` by day 12. ⚠ **Public repo only.** Suggested target:
  `agentic-ai-book-labs`.
- **Mohanna** — two days distilling Chip Huyen's *AI Engineering*, which is on the subscription.

### Books on the subscription, queued
- Ma and Wang, *Multi-Agent AI Engineering* (Packt, published 2026-09-24) — parallel reading
  channel, Q3. Check the platform before buying anywhere.
- Soares, *Skills for AI Agents* — saved, planned for early 2027.
- Stratis, *AI Agents with MCP* — see workstream 1; placement open.

### Update 2026-10-05 (later)
- **Reading position:** on `docs/2026-07-28/learn/server-concepts`, following along with each page.
- **Alake harness courses (both):** saved, to be watched **after MCPA**. Not attended live.

### Update 2026-10-08
- **Anthropic Academy: Introduction to MCP — completed** (Skilljar). cli_project typed on the NUC, tools tested in the Inspector (legacy protocol, mcp 1.26.0).
- Next: back to modelcontextprotocol.io at server-concepts, 2026-07-28 pages only.

---

## 2026-10-09 — Official MCP Authorization tutorial COMPLETED (Python) + a gap found in it

**What was done.** The official tutorial "Understanding Authorization in MCP"
(https://modelcontextprotocol.io/docs/2026-07-28/tutorials/security/authorization, Python tab)
was typed by hand and run end to end. MCPA Domain 4 (Security, 24%).
Folder (NUC, WSL): `~/projects/MCP_Course/official-tutorials/mcp-auth/`
Files: `mcp_server/config.py`, `server.py`, `token_verifier.py`, `__init__.py` (empty), `pyproject.toml`.
SDK installed by `uv sync`: `mcp 2.3.0`. Auth server: Keycloak 26.8.0 in Docker.
Verified: VS Code -> 401 -> metadata -> Keycloak login + consent -> tools listed -> `add_numbers` called.

### THE FINDING — the tutorial is missing a Keycloak step (as of 2026-10-09)
- Symptom: VS Code shows `Error 401 ... invalid_token` after a successful login.
  Server log: introspection returns `200 OK` but the body says `active: false`.
- Cause, from Keycloak's own log (`INTROSPECT_TOKEN_ERROR`):
  `reason="Client 'test-client' is not in the token audience"`.
  Keycloak 26.8.0 only answers introspection for a client that is named in the token's audience.
- Fix: on client scope `mcp:tools`, add a SECOND Audience mapper:
  name `test-client-audience`, Included Client Audience = `test-client`, custom audience empty,
  Add to access token ON, Add to token introspection ON.
- The page does not mention this. The clue was in its own code: the comment in
  `token_verifier.py` lists the audience as `["test-client", "http://localhost:3000", "account"]`.

### Second trap — one mapper carries ONE audience
An Audience mapper with BOTH "Included Client Audience" and "Included Custom Audience" filled in
only emits the client. Observed: `aud=test-client`, server address missing, our verifier refused it.
Working state on `mcp:tools`:

| Mapper | Client audience | Custom audience |
|---|---|---|
| `audience-config` | (empty) | `http://localhost:3000` |
| `test-client-audience` | `test-client` | (empty) |

In the client picker, `test-client` is on page 2 — search for it. Check the mapper actually saved with it.

### How it was found (the method, reusable)
`verify_token` returns `None` silently on every failure (fails closed). Three `logger.warning`
lines were added, one above each `return None` (HTTP status / not active / audience mismatch with
both values). They are still in the file, on purpose: generic error to the client, detailed reason
in the server log. Then Keycloak's reason:
`docker logs --since 15m keycloak 2>&1 | grep -o 'reason="[^"]*"' | tail -3`

### Operating notes for this lab
- Keycloak container is named `keycloak`. Start: `docker start keycloak`. Stop: `docker stop keycloak`.
  NEVER `docker run` it again — that makes an empty one. It exited (129) when its terminal was closed.
- Start the server: `cd` to `mcp-auth`, then `set -a; source .env; set +a`, then `uv run mcp-simple-auth-rs`.
  `os.getenv` does not read `.env`; without the first line the secret is silently empty.
- `.env` needs only `OAUTH_CLIENT_SECRET`. Client ID defaults to `test-client` in `config.py`.
- VS Code: use `MCP: List Servers` -> `mcp-auth-lab` -> Start Server / Sign Out / Show Output.
  The `mcp.json` "file was not found" tab is a display problem; close it, do not click Create File.
- Each failed attempt made VS Code register a NEW client (Dynamic Client Registration). Several
  "Visual Studio Code" clients now exist in Keycloak. Harmless in the lab; delete extras if wanted.
- The server prints `MCPDeprecationWarning: AuthSettings.validate_token_resource is not set` twice.
  Harmless: our `token_verifier.py` checks the audience itself.
- System Python on the NUC is 3.10: no `tomllib`. Let `uv sync` validate `pyproject.toml`.

### Concepts this made concrete (MCPA D4)
- Fail closed: anything that is not a clear yes returns "not valid" -> 401.
- Audience is checked on BOTH sides: Keycloak (may this client inspect the token?) and our server
  (was this token issued for me?).
- Two consent points: Keycloak's "Grant Access" screen (scope), VS Code's "Allow" on each tool call.
- Introspection runs on EVERY request: always current, one extra network call each time.
- The tools contain no security code; `MCPServer` + `AuthSettings` does the guarding.

### Python lessons from this file set
- "syntax ok" is not "correct": `Host` vs `HOST` and `.iosformat()` both passed the syntax check
  and would only fail when that line ran.
- A white dot on a VS Code tab = not saved. Two files existed only in the editor. Auto Save is now on.
- Python lines go in the file; shell commands go in the terminal.

### Open / unverified
- Server log showed `Created new transport with session ID` and VS Code logged an `initialize`
  request. Earlier notes say 2026-07-28 Streamable HTTP is stateless and starts with
  `server/discover`. NOT verified which is in play — check the 2026-07-28 transports page.
- Lunch & Learn day-1 mystery CLOSED 2026-10-08: at `max_tokens=60` the only block returned was
  type `thinking`. Thinking counts against `max_tokens`, so the budget ran out before any text.

---

## CURRENT STATE 2026-10-10 — supersedes the top section where they differ

Read order for a new chat: the top section, then THIS section, then `HANDOVER-2026-10-08.md`.
Everything in the top section still stands unless listed here.

### What is active now
- **MCPA is still first. Nothing resets it.** Exam bought 2026-09-21, 90 minutes, 2026-07-28 spec.
- **Lunch & Learn: Claude Edition** runs alongside, to 2026-11-03. Not MCPA work.
- **Work learning** (Koch's Claude + Teams program) ranks UNDER MCPA. See `HANDOVER-2026-10-08.md` §4.
- So the top section's "Nothing else is in progress" is no longer true.

### Where the MCPA reading is
- Done: the four official Learn pages (10-06); Anthropic Academy "Introduction to MCP" (10-08);
  the official Authorization tutorial, typed and run end to end (10-09, entry above).
- **Now on:** Security Best Practices,
  https://modelcontextprotocol.io/docs/2026-07-28/tutorials/security/security_best_practices
  — in "Attacks and Mitigations". Confused Deputy is covered; ten attack topics remain.
- Then: the 2026-07-28 transports page.
- The order of the remaining workstreams (book, practice exams, class redo, labs) was stated
  2026-10-09 and is explicitly NOT fixed. Ask him; do not assume.
- Booking gate, unchanged: 90%+ on two consecutive fresh sets; no competency below 70%;
  every lab done unassisted; one full sitting inside 90 minutes.

### Corrections to the top section
- **"Self-tests are open recall, not multiple choice" is superseded.** There is a multiple-choice
  practice bank (purchased topic sets plus course quizzes). Its rules: no dumps and no real exam
  items, ever; old-protocol questions are updated to 2026-07-28 or removed; sealed mocks are taken
  once. Practice exams run live in chat, one question at a time, fresh each sitting.
- **Lunch & Learn repo** is his own `ttweed98/claude-lunch-and-learn`, not `agentic-ai-book-labs`.
  The class repo is `keshawillz/oreilly-claude-pr-review-helper`, one tag per session
  (`day-01` ...). Done on the work laptop. Dedicated API key there; revoke by 2026-11-03.
- **netmiko_mcp is no longer the only 2026-07-28 codebase he owns.**
  `~/projects/MCP_Course/official-tutorials/mcp-auth/` is a working OAuth-protected MCP server on
  `mcp 2.3.0`, and it is the OAuth chain `preflight` M1 would build on.

### Claude certifications — the question reopened and was decided
- Trigger fired 2026-10-07: his Koch email reached checkout at the Anthropic Partner Academy.
- Decided 2026-10-08: **Developer – Foundations (CCDV-F) first after MCPA, then
  Architect – Professional (CCAR-P).** Architect – Foundations is undecided.
- Do not reopen the order. Do not propose booking anything before MCPA is passed.
  Detail: `HANDOVER-2026-10-08.md` §5.

### `preflight`
- Unchanged: the build resumes after MCPA. Decisions §6.2 to §6.5 are still open and still his.
- Still unsettled: whether M0 runs before or after the exam-prep lab work. Ask.

### Verified since the top section was written
- Security Best Practices (2026-07-28) states MCP is stateless with no protocol-level sessions;
  server-assigned session IDs belong to 2025-11-25 and earlier. The transports-page check is
  still owed.
- Keycloak 26.8.0 needs the introspecting client in the token audience (entry above).

### Seen, not placed
- `anthropics/prompt-eng-interactive-tutorial` — Anthropic's prompt engineering course, 9 chapters
  of notebooks, written for Claude 3 Haiku, has a Bedrock version. Not MCPA material. Suggested
  slot: after MCPA, with CCDV-F prep. His call; not yet made.

### Where things are — additions
| | |
|---|---|
| Authorization lab | `~/projects/MCP_Course/official-tutorials/mcp-auth/` (NUC) |
| Anthropic MCP course | `~/MCPA/anthropic-mcp-course/cli_project` (NUC) |
| Lunch & Learn | `~/MCPA/oreilly-claude-pr-review-helper` (work laptop) |
| Latest handover | `research-log/HANDOVER-2026-10-08.md` |
| Job postings tally | `research-log/job-postings-tally.md` |
| Microsoft training mapped to the Koch agent design | `research-log/Microsoft training for Koch agents.md` |
