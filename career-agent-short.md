# Career Agent — Product Brief

**Version:** June 2026  
**Author:** Alex Bondarenko · [LinkedIn](https://www.linkedin.com/in/alexibondarenko/) · [GitHub](https://github.com/ioncat/ai-career-agent)

---

AI-powered job search counselor for Product Managers and Product Owners.

## Executive Summary

Career Agent decides whether a vacancy is worth pursuing *before* any application material is generated.

It reads the job description deeper than a candidate under first-impression bias, scores fit against the vacancy, surfaces hidden barriers, and produces a tailored CV only when the opportunity justifies the effort.

Built and validated inside a real job search. ~80 vacancies processed, multi-user, documents generated automatically, cost tracked per vacancy ($0.32). The product runs on real vacancies, not synthetic examples.

---

## Problem

A Product Manager spends 30–90 minutes tailoring a CV before knowing whether they have a realistic shot. In an active search — 30, 70 vacancies — that's weeks of effort with no quality guarantee.

Three issues surfaced repeatedly during my own search:

* **Fit is judged emotionally, not systematically.** First impression anchors the decision. Real example from the system: I self-assessed a vacancy at 10/10 — the agent returned 4/10. A 6-point delta, caused by one hidden requirement I had read past.
* **Hiring barriers stay invisible** until late in the process — usually after effort is already spent.
* **Effort goes to applications that should never have been submitted.**

Most AI CV tools optimize document generation. None of them optimize the decision of whether generating the document is worthwhile.

---

## Key Product Decisions

### 1. Decision before document

Nothing is generated until the system returns a go/no-go.

The agent scores the vacancy across 8 dimensions (vacancy attractiveness) and runs a **Fit × VScore matrix** that outputs one of three recommendations — `apply` · `take a chance` · `decline`. A `decline` stops the pipeline cold: no CV, no cost.

*Why:* effort should follow a recommendation, not precede it.

### 2. Vertical focus: PdM / PO

The product targets Product Managers and Product Owners, not all professions.

Product roles carry archetypes that generic tools miss entirely. The fit analysis distinguishes a **Founder Proxy** from an **Executor** — an archetype mismatch is a silent hire-killer, and a CV tuned to the wrong one reads wrong to the hiring manager no matter how polished it is.

*Why:* a narrow ICP buys fit-assessment accuracy that breadth can't.

### 3. Human approval on irreversible actions

The system never auto-applies. The candidate makes two calls — apply/skip, and approve/reject the CV. Everything else is automated.

*Why:* automation should remove operational work, not replace judgment.

### 4. RSS-first discovery

Vacancies are pushed to the user instead of being searched for manually.

*Why:* searching is repetitive; evaluation is the valuable act. Move the human effort to where it pays.

### 5. Deterministic vs cognitive split

The system is being redesigned to separate deterministic operations (file ops, dedup, PDF rendering) from genuine LLM reasoning — collapsing ~43 mixed steps down to 3 cognitive decision points.

*Why:* most workflow steps need no intelligence. They add latency and cost for nothing.

---

## Solution

The pipeline, end to end:

1. **Vacancy ingestion** — auto via RSS push; Flutter Desktop app shows new vacancies with fit analysis and triggers local notifications
2. **Deep analysis** — employer's real pain, hidden requirements, role archetype, VScore
3. **Fit scoring** — Fit × VScore matrix → `apply` / `take a chance` / `decline`
4. **Barrier identification** — what stands between this candidate and this role
5. **Gap resolution** — barriers resolved interactively, evidence saved to the profile, *before* anything is written
6. **CV generation** — from real experience, tailored to the JD's actual pain
7. **CV self-review** — agent checks its own draft against the adaptation plan before the user ever sees it
8. **Cover letter generation**

The candidate makes two decisions. The system does the rest.

---

## Outcomes & Evidence

* ~80 vacancies analyzed in a real active search
* Multi-user architecture (two live users, two profile types)
* End-to-end pipeline operational — analyze → CV → cover letter
* Cost measured and tracked at execution level
* Prompt caching live
* Run on real vacancies, not synthetic examples

---

## Unit Economics

Cost of service: **~$0.32 per vacancy** — full pipeline, fetch through cover letter, on Claude Sonnet 4.6 with prompt caching.

Cost tracking is built into the platform and recorded per run. That visibility wasn't an afterthought — it changed architectural priorities: it justified prompt caching and put the deterministic/cognitive split on the roadmap.

---

## Key Learnings

### Product focus beats generality

The original vision was broader. Product-specific hiring patterns turned out valuable enough to justify narrowing the ICP to PdM / PO — and the narrowing made the fit assessment measurably sharper.

### Many "AI problems" are actually workflow problems

Several bottlenecks looked like they needed better prompts. In practice they needed better process design — which is what the deterministic/cognitive split is.

### Cost is a product constraint

Tracking token usage and per-run cost changed architectural priorities and roadmap order. $0.32 isn't a vanity metric; it's the number the unit economics live or die on.

### Human judgment stays critical

The highest-value decisions in the workflow — apply/skip, approve/reject — remain human. The system is built to improve decision *quality*, not to remove the decision maker.

---

## Current State & Next Step

**Flutter Desktop app is live** — vacancy list, detail screen with verdict card, CV/cover letter preview. RSS auto-pipeline feeds it without manual input.

**Blocked on LLM access** — Phase 2.5 (objection handling) and full CV generation require the pipeline to be re-enabled. The deterministic/cognitive split is designed and ready; LLM integration is the remaining unlock.

Objective: lower cost, lower latency, more predictable output — without giving up decision quality.
