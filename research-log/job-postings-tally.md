# Job postings tally — study-direction check

**Purpose (Tony, 2026-10-09):** postings are not applications. They are used to check that the study
direction is right, to see where further training is needed, and to find gaps in the learning.

**How to use this file**
- One entry per posting, appended at the bottom. Never rewrite old entries.
- After each new posting, update the tally table: add 1 to every skill the posting asks for.
- Status key: ✅ have it · 🟡 in progress / partial · ❌ gap · 📅 on the plan, not started.
- A skill that keeps climbing the tally with status ❌ is a gap the plan must answer.
- Compare against the plan in `research-log/START-HERE.md` and the latest `HANDOVER-*.md`.

---

## Tally (update after every posting)

| Skill / area | Count | Status | Where the plan covers it |
|---|---|---|---|
| Python — strong software engineering (composing, not transcribing) | 1 | 🟡 | Lunch & Learn, MCP labs, Python Functions & Classes (O'Reilly) |
| LLM apps / AI APIs | 1 | 🟡 | Lunch & Learn, Anthropic Academy, CCDV-F |
| Agent frameworks (LangChain / LangGraph / LlamaIndex) | 1 | 📅 | LangGraph build + LangChain cert, after MCPA |
| API services (FastAPI or similar) | 1 | ❌ | not on the plan by name — natural next step after the MCP auth tutorial |
| Cloud (AWS / GCP / Azure) | 1 | ✅ | AWS in daily work; Bedrock gap → AWS GenAI Developer cert |
| Agents shipped to production | 1 | 🟡 | Koch agentic program (Claude + Teams) |
| RAG | 1 | ❌ | no slot yet — decide after MCPA |
| Tool calling | 1 | 🟡 | MCPA, Case 01 MCP tools |
| Model / agent evaluation | 1 | ✅ | Case 01 scored runs, DEV AI kit evaluation, eval-harness thread |
| Prompt engineering / orchestration | 1 | 🟡 | Lunch & Learn, CCDV-F |
| Open-source / personal AI projects | 1 | ✅ | public lab repos, Case 01/02 |

---

## Postings

### 1. Palantir — Forward Deployed Engineer (shared 2026-10-05)
- Shared as a market signal only. Details were reviewed in an earlier chat and not captured here.
- ⚠ If he re-shares it, add its skills to the tally.

### 2. micro1 — AI/ML Engineer, Internal Platforms (shared 2026-10-09)
- **Link:** jobs.micro1.ai (via LinkedIn) · full-time · remote
- **Pay:** base $130K–$160K + equity/bonus (header says $220K–$300K — likely total comp; the notice is the reliable figure)
- **What the job is:** product engineering on micro1's own AI recruiting agent — agent features,
  Python backend/APIs, prompts, RAG, orchestration, quality evaluation, production deploys.
  An "agent as a product" role, not infrastructure.
- **Required:** strong Python SWE · LLM apps / AI APIs · LangChain, LangGraph or LlamaIndex · FastAPI or
  similar · AWS/GCP/Azure · problem-solving, communication
- **Preferred:** agents in production · RAG, tool calling, model evaluation, prompt engineering ·
  open-source or personal AI projects · fast-paced AI-first teams
- **Read against the plan:**
  - Confirms the order: evaluation, tool calling and production agents are what MCPA, Lunch & Learn
    and the Koch program build.
  - Biggest gap: **Python composition** — the core skill they hire on.
  - Strongest match: **evaluation** (Case 01, DEV AI kit eval).
  - LangChain/LangGraph appears again → supports its slot after MCPA.
  - **RAG** has no slot in the plan — flagged.
  - **FastAPI** — small gap, natural after the MCP auth tutorial.
