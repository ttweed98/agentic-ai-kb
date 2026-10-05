# START HERE — paste this first in any new chat

**Last updated 2026-10-05.** This is the state file: what is active, what is decided, what is
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
