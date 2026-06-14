# Career Agent — Product Brief

**Version:** June 2026  
**Author:** Oleksii Bondarenko · [LinkedIn](https://www.linkedin.com/in/alexibondarenko/) · [ioncat.github.io](https://ioncat.github.io)

---

## Problem & Market

The PM job market is competitive and getting more so: more candidates chasing the same roles. A typical candidate spends 2–4 hours tailoring a CV before knowing whether they have a real shot. Most tools help write faster. None answer the prior question: *should you apply at all?*

There is also a cognitive trap: candidates read job descriptions emotionally. The first impression anchors their judgment — real barriers get ignored. Real example: a candidate self-assessed at 10/10 → the agent returned 4/10 with specific reasoning. Delta = 6 points, caused by one hidden core requirement.

**Counter-cyclical thesis:** the worse the market for candidates, the higher the value of a tool that raises hit rate. The better the market, the faster the candidate lands. Either way, the tool is needed.

---

## Solution

**Career Agent** is a personal job search counselor for Product Managers and Product Owners — not a CV generator, but a decision-support tool with two layers of value:

**Layer 1 — Should you apply?**  
The agent reads the vacancy more carefully than a candidate can after an emotional first impression anchors their judgment. It extracts the employer's real pain, hidden requirements, and archetype signal (Founder Proxy vs Executor). It scores the vacancy across 8 dimensions (**VScore**) and runs a Fit × VScore matrix to deliver an honest verdict: *apply / take a chance / decline*. `decline` → pipeline stops immediately. No CV wasted.

**Layer 2 — How do you win this one?**  
If the fit is real: the best possible pitch built from the candidate's actual experience. Not a generic CV — a targeted story answering *why this candidate solves this employer's specific problem*. Includes a self-review pass: CV cross-checked against the Adaptation Plan before delivery.

**How it works:**

```
RSS auto-discovery → Telegram notification
→ Phase 1:   Deep JD Analysis (archetype, hidden requirements, barriers, VScore — 8 dims)
→ Phase 2:   Fit Scoring (Fit × VScore → apply / take a chance / decline · Adaptation Plan)
             [decline: pipeline stops]
→ Phase 2.5: Objection Handling — resolve gaps interactively; evidence saved to PROFILE.md
→ Phase 3:   CV Draft (archetype-aware, tailored to Adaptation Plan)
→ Phase 3.5: Self-Review (word frequency, tools gap, tone vs archetype)
→ CV.pdf → Telegram · [user approves]
→ Phase 4:   Cover Letter (2 variants: narrative + bullets) → CoverLetter.pdf → Telegram
```

The user makes exactly two decisions: apply or skip, and approve the CV. Everything else runs automatically.

---

## Who It's For

**Product Managers, Product Owners, Project Managers** in active job search. Passive search is a secondary segment.

The PM specialisation is intentional: fit analysis understands PM archetypes, evaluates PM-specific experience signals (product ownership, discovery vs delivery, team scale), and adapts CV framing to what the role actually needs — not just what the JD says.

---

## Differentiation

| What | How it differs |
|---|---|
| **Decision-first** | Fit verdict (apply / take a chance / decline) before any CV work. No alternative (Jobscan, Teal, Resume.io) gates document generation on a go/no-go assessment |
| **PM archetype-aware** | Fit analysis distinguishes Founder Proxy vs Executor — archetype mismatch is a silent hire killer that generic tools ignore |
| **Self-review loop** | Phase 3.5: the agent reviews its own CV against the Adaptation Plan. The user sees an already-validated version |
| **Honest scoring** | Says "don't apply" when fit is weak. Not optimised for submission rate — optimised for outcomes |

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
| CV generation rate (go-verdict → CV) | Tracked in DB |
| Cache hit rate + cost per vacancy | Tracked in DB (`llm_usage`) |
| Time-to-placement | Planned (self-report via bot prompt) |

Actuals not yet published — next step: `docs/discovery/product-metrics.md` from live SQLite data.

---

## Unit Economics

COGS ~$0.32 per vacancy (full pipeline: fetch → analyze → CV → cover) on Claude Sonnet 4.6 with prompt caching.

| Model | Revenue | COGS | Gross Margin |
|---|---|---|---|
| $0.99 / vacancy | $0.99 | $0.32 | **68%** |
| $4.99 / vacancy | $4.99 | $0.32 | **94%** |
| $19.99 / mo, 50 vacancies | $19.99 | $16.00 | **20%** |

**Optimal model:** pay-per-vacancy ($0.99–$4.99) or subscription capped at ≤ 30 vacancies/month.

---

## What's Built / What's Next

**Working today:**
- Full 8-phase pipeline: VScore scoring, Fit × VScore verdict, Objection Handling (Phase 2.5), CV Draft, Self-Review
- Telegram UI (aiogram 3.x) + Web tracker (FastAPI + HTMX)
- Multi-user architecture (users table, user-scoped vacancies, skill_type routing)
- Prompt caching + Extended Thinking (Claude Sonnet 4.6)
- Microservices: jd-parser (URL → Markdown), pdf-service (Markdown → PDF)
- RSS auto-discovery + webhook push
- 2 skill types: `pm` (archetype-aware) + `generic`
- 291 tests, e2e verified

**Next significant step:**  
Deterministic/Cognitive split — extract deterministic orchestration (file ops, dedup, PDF rendering) into a Python FSM; invoke LLM only at 3 cognitive decision points instead of ~43 mixed steps. Goal: lower latency, lower COGS, predictable output structure.

---

## Open Risks

- **No external user validation** — all discovery is self-reported; no interviews with outside users conducted yet
- **Competitive landscape unmapped** — positioning built on product thesis, not market data
- **Fit scoring calibration is manual** — no automated feedback loop from "verdict → actual outcome"
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
