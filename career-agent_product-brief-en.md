# Career Agent — Product Brief

**Version:** June 2026  
**Author:** Oleksii Bondarenko · [LinkedIn](https://www.linkedin.com/in/alexibondarenko/) · [ioncat.github.io](https://ioncat.github.io)

---

## Problem & Market

Career Agent — job search counselor for PdM / PO.

The PM job market is competitive and getting more so. A typical candidate spends 30–90 minutes tailoring a CV before knowing whether they have a real shot. In an active search — 30, 70 vacancies — that's weeks of effort with no quality guarantee.

Two problems compound each other. First, candidates read job descriptions (JD) emotionally: the first impression anchors judgment, real barriers get missed. Real example: a candidate self-assessed at 10/10 — the agent returned 4/10. Delta = 6 points, caused by one hidden requirement. Second, even when the decision to apply is right, making a genuinely tailored CV manually takes time most candidates don't spend — they reuse the same document and wonder why response rates are low.

**Primary ICP:** PdM (Product Manager) · PO (Product Owner) in active job search.  
**Extended ICP:** PM (Project Manager) · BA (Business Analyst) · other non-technical roles (via generic skill type).

---

## Product Vision

Career Agent builds a picture of the candidate — through structured onboarding, LinkedIn/CV data, and evidence surfaced during the pipeline. That knowledge compounds with every session.

When fit is real: a targeted CV from actual experience, cross-checked against the vacancy's requirements, delivered as PDF. No fabricated claims. The user approves — or requests edits. Everything else is automated.

Candidates spend time on decisions, not on writing.

**Problems we solve:**

- Candidate doesn't know real odds before submitting CV
- 30–90 min per application × 30–70 vacancies = weeks of effort, no quality guarantee
- JD read emotionally — first impression anchors judgment, real barriers missed
- Self-assessment of fit systematically inflated (real example: 10/10 → 4/10)
- Same CV reused across applications — low response rate, cause unknown
- CV doesn't address employer's actual pain buried in the JD
- No honest go/no-go before CV generation
- Generic tools miss PM archetypes (Founder Proxy vs Executor, Discovery vs Delivery)
- Gaps not addressed before writing starts
- CV generators fabricate — no verified candidate profile, no accumulated evidence across sessions

---

## Solution

**Career Agent** is an AI pipeline powered by Claude API (Anthropic) — built for the hardest part of job search: evaluating fit before you invest in the application. Not a CV generator — a pipeline that answers two questions, in order.

**Should you apply?** The agent reads the JD deeper than a candidate under emotional first impression. It extracts the employer's real pain, hidden requirements, and role archetype (Founder Proxy vs Executor). It scores the vacancy across 8 dimensions (**VScore** — vacancy attractiveness) and runs a Fit × VScore matrix. Recommendation: `apply` · `take a chance` · `decline`. `decline` stops the pipeline — no effort wasted.

**How do you win this one?** If fit is real, gaps come first: barriers surfaced in Fit Scoring are resolved interactively (Phase 2.5) and saved to PROFILE.md before any writing starts. Then: a targeted CV from actual experience, self-reviewed against the Adaptation Plan — the user sees an already-validated version.

**How it works:**

1. **JD Discovery & Extraction** — automatic via RSS push, or manual (URL / JD paste in Telegram).
2. **Deep Analysis (Phase 1)** — employer's real pain, hidden requirements, role archetype, **VScore** (vacancy attractiveness, 8 dims).
3. **Fit Scoring (Phase 2)** — Fit × VScore matrix → recommendation: `apply` · `take a chance` · `decline`. Key Barriers + Adaptation Plan. `decline` → pipeline stops, no CV wasted.
4. **Objection Handling (Phase 2.5)** — if barriers exist: resolve gaps interactively before writing anything. Resolved evidence saved to PROFILE.md.
5. **CV Draft (Phase 3)** — tailored to JD pain and Adaptation Plan.
6. **Self-Review (Phase 3.5)** — word frequency check, tools gap, tone vs archetype. First time user sees the CV.
7. **Approval → CV.pdf → Telegram**
8. **Cover Letter (Phase 4) → CoverLetter.pdf → Telegram**

The user makes two decisions: apply or skip, and approve the CV. Everything else runs automatically.

> **PoC note:** at the current stage, Telegram is the primary interface for all user interactions with the service.

---

## Differentiation

| What | How it differs |
|---|---|
| **Decision-first** | Fit recommendation (apply / take a chance / decline) before any CV work. No alternative (Jobscan, Teal, Resume.io) gates document generation on a go/no-go assessment |
| **PM archetype-aware** | Fit analysis distinguishes Founder Proxy vs Executor — archetype mismatch is a silent hire killer that generic tools ignore |
| **Self-review loop** | Phase 3.5: the agent reviews its own CV against the Adaptation Plan. The user sees an already-validated version |
| **Honest scoring** | Says "don't apply" when fit is weak. Not optimised for submission rate — optimised for outcomes |
| **Claude API + Extended Thinking** | Core analysis uses Extended Thinking for non-obvious barrier detection; prompt caching cuts per-vacancy cost |

---

## Traction & Evidence

- **~80 vacancies processed** by one user in real active job search (data in SQLite)
- **2 users** with different profiles (skill type: `pm` and `generic`)
- **e2e verified:** full pipeline (analyze → CV → cover) run end-to-end, cost logged ($0.10, 2026-06-02)
- **~32 hours of development** (from effort-log.md, lower bound from git history)
- Product is used by the author in current active job search — real data, not synthetic

---

## Metrics

| Metric | Status |
|---|---|
| Vacancies analyzed / user / month | Tracked in DB |
| Apply rate (applied vs skipped after analysis) | Tracked in DB |
| CV generation rate (go-recommendation → CV) | Tracked in DB |
| Cache hit rate + cost per vacancy | Tracked in DB (`llm_usage`) |
| Time-to-placement | Planned (self-report via bot prompt) |

Actuals not yet published — next step: `docs/discovery/product-metrics.md` from live SQLite data.

---

## Unit Economics

Cost of Service ~$0.32 per vacancy (full pipeline: fetch → analyze → CV → cover) on Claude Sonnet 4.6 with prompt caching.

| Model | Revenue | Cost of Service | Gross Margin |
|---|---|---|---|
| $0.99 / vacancy | $0.99 | $0.32 | **68%** |
| $4.99 / vacancy | $4.99 | $0.32 | **94%** |
| $19.99 / mo, 50 vacancies | $19.99 | $16.00 | **20%** |

**Optimal model:** pay-per-vacancy ($0.99–$4.99) or subscription capped at ≤ 30 vacancies/month.

---

## What's Built / What's Next

**Working today:**
- Full 8-phase pipeline: VScore scoring, Fit × VScore recommendation, Objection Handling (Phase 2.5), CV Draft, Self-Review
- Telegram UI (aiogram 3.x) + Web tracker (FastAPI + HTMX)
- Multi-user architecture (users table, user-scoped vacancies, skill_type routing)
- Prompt caching + Extended Thinking (Claude Sonnet 4.6)
- Microservices: jd-parser (URL → Markdown), pdf-service (Markdown → PDF)
- RSS auto-discovery + webhook push
- 2 skill types: `pm` (archetype-aware) + `generic`
- 291 tests, e2e verified

**Next significant step:**  
Deterministic/Cognitive split — extract deterministic orchestration (file ops, dedup, PDF rendering) into a Python FSM; invoke LLM only at 3 cognitive decision points instead of ~43 mixed steps. Goal: lower latency, lower Cost of Service, predictable output structure.

---

## Open Risks

- **No external user validation** — all discovery is self-reported; no interviews with outside users conducted yet
- **Competitive landscape unmapped** — positioning built on product thesis, not market data
- **Fit scoring calibration is manual** — no automated feedback loop from "recommendation → actual outcome"
- **Onboarding not complete** — AI Interview System (key differentiator, deep profile generation) is 🔴 DESIGN REQUIRED

---

## PM Work Done (hiring context)

| Artifact | What it demonstrates |
|---|---|
| Product thesis + two-sided pain analysis | Problem discovery, market thinking |
| ADR-01: Pivot from generic agent to focused vertical service | Strategic decision-making, product identity |
| EPIC-21: Deterministic vs Cognitive split | Technical PM thinking, unit economics impact |
| 21 Epics with blocker-ordered tasks + acceptance criteria | Requirements management, delivery execution |
| Tokenomics.md: 6 verified real runs, formula exact to $0.00 | Unit economics, data-driven decisions |
| PIVOT-PLAN.md: phases with dependencies + success criteria | Structured planning, execution tracking |
| Hypotheses framework (H-001, H-002, H-003) | Experimental mindset, structured product thinking |
