# Career Agent — Brief

**Version:** July 2026  
**Author:** Alex Bondarenko · [LinkedIn](https://www.linkedin.com/in/alexibondarenko/) · [GitHub](https://github.com/ioncat/ai-career-agent)

---

AI Career Agent helps you get more interview invitations by automatically creating tailored CVs that reflect the strongest match between your experience and each job's requirements.


## Executive Summary

### Problem - Climbing the wrong wall

A Product Manager may spend a minimum of 30–90 minutes on manual CV tailoring before knowing whether they have a realistic shot. 
In an active search, at dozens of vacancies a day, that's weeks to months of effort with no result guarantee.
This may take up to several months to understand where the gap between expectations and reality is.

Three issues surfaced repeatedly in real-world use:

* **Fit is judged emotionally, not systematically.** First impression anchors the decision. Real example: a vacancy self-assessed at 10/10 — the system returned 4/10. A 6-point delta, caused by one hidden requirement missed on first read.
* **Hiring barriers stay invisible** until late in the process — usually after effort is already spent.
* **Effort goes to applications that should never have been submitted.**
* **Self-perception and CV signal don't match.** How candidates see their own skills and fit for a role isn't what the CV actually tells a recruiter. That gap is why strong candidates get passed over without ever knowing why.

Most AI CV tools optimize document generation — not the decision of whether generating the document is worthwhile in the first place.


---


### Solution - A CV doesn't get you hired. It gets you invited.

* Instantly analyzes vacancies and delivers a clear go/no-go signal.
* Writes a CV that sends the employer signals of the candidate's fit to the vacancy's requirements.  
* Saves 90% of the user's time and effort.  
* Scoped to one vertical: Product Managers and Product Owners.

![Career Agent — Flutter Desktop interface](asset/Flutter%20Main%20Screen.png)

---

## Key Decisions

### 1. Decision before document

Nothing is generated until the system returns a go/no-go.

The system scores two separate things: how well the candidate fits the role, and how good the vacancy itself is on its own merits — company tier, market scope, remote policy, and five other dimensions. Combining both produces one of three recommendations — `apply` · `take a chance` · `decline`. A `decline` stops the pipeline cold: no CV, no cost.

*Why:* effort should follow a recommendation, not precede it.

### 2. Vertical focus: PdM / PO

It's scoped to Product Managers and Product Owners, not all professions.

Product roles carry archetypes that generic tools often miss. The fit analysis scores against a taxonomy of ten role archetypes (Founder Proxy, Executor, Discovery-heavy, Platform/Systems PM, Growth PM, and others, combinable in pairs). An archetype mismatch is a silent hire-killer: a CV tuned to the wrong one reads wrong to the hiring manager no matter how polished it is.

*Why:* a narrow target user buys fit-assessment accuracy that breadth can't.

### 3. Human approval on irreversible actions

The system never auto-applies. The candidate makes two calls — apply/skip, and approve/reject the CV. Everything else is automated.

*Why:* automation should remove operational work, not replace judgment — and unattended, uncontrolled API calls are a real cost risk this design deliberately avoids, not just a philosophical stance.

### 4. RSS-first discovery

Vacancies are pushed to the user instead of being searched for manually. RSS integration is deliberately scoped to a couple of popular job boards, not broad job-board coverage — anything else goes in via manual paste (flat text or markdown), since wider integration isn't the current focus.

*Why:* searching is repetitive; evaluation is the valuable act. Move the human effort to where it pays.

### 5. Cost-gating pre-filter before any real reasoning

A cheap check runs before the vacancy ever reaches the deep-analysis step: deterministic title/domain matching, with an optional LLM pass for less obvious cases. A vacancy that can't possibly be a fit never gets a real LLM call.

*Why:* cost discipline has to start before the expensive step, not after it.

### 6. Deterministic vs cognitive split

The pipeline separates deterministic operations (file ops, dedup, PDF rendering) from genuine LLM reasoning — shipped: ~43 originally mixed steps collapsed down to a handful of real cognitive decision points.

*Why:* most workflow steps need no intelligence. They add latency and cost for nothing.

### 7. A second editorial pass, gated by outcome not by default

A dedicated editorial-review pass runs only when a vacancy already cleared the fit bar (`apply`, high score) — checking for AI-tell writing patterns, credibility, and accidental echoes of the job posting's own language. Verified empirically that an LLM auditing its own just-written text in the same context is measurably more lenient than a fresh, isolated pass on identical text — so the audit runs isolated from the drafting step, not chained onto it.

*Why:* thorough checking should be reserved for vacancies worth the effort. Running this same expensive audit on every CV, including ones for jobs that were never a real fit, would make the whole system too expensive to run.

---

## How It Works

The pipeline, end to end:

1. **Vacancy ingestion** — auto via RSS, saved straight to the database and surfaced in the Flutter inbox; a Telegram push is just a heads-up, not the destination. Manual URL/paste goes straight to the inbox the same way
2. **Critical Blocker pre-filter** — cheap deterministic + optional LLM check; an obvious non-fit is flagged before real analysis spend
3. **Deep analysis** — employer's real pain, hidden requirements, role archetype, vacancy attractiveness
4. **Fit scoring** — candidate fit combined with vacancy attractiveness → `apply` / `take a chance` / `decline`
5. **Barrier identification** — what stands between this candidate and this role
6. **Gap resolution** — barriers resolved interactively, evidence saved to the profile, *before* anything is written
7. **CV generation** — from real experience, tailored to the JD's actual pain
8. **CV self-review** — a dedicated pass checks the draft against the adaptation plan before the user ever sees it
9. **Cover letter generation** — two variants, user picks
10. **Editorial audit** *(opt-in)* — a second LLM pass, run only for genuinely strong matches, checks the CV and cover for naturalness, credibility, and phrasing that accidentally echoes the job posting's own wording back at the employer

The candidate approves/declines and reviews the CV. Both documents are downloaded straight from the Flutter app — Telegram is used only for the initial "new vacancy" push, not document delivery.

---

## Outcomes & Evidence

* 700+ vacancies ingested, ~100 fully analyzed, in a real active search
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

### Narrow focus beats generality

The original vision was broader. Product-specific hiring patterns turned out valuable enough to justify narrowing the target user to PdM / PO — and the narrowing made the fit assessment measurably sharper.

### Many "AI problems" are actually workflow problems

Several bottlenecks looked like they needed better prompts. In practice they needed better process design — which is what the deterministic/cognitive split is.

### Cost is a real constraint

Tracking token usage and per-run cost changed architectural priorities and roadmap order. $0.32 isn't a vanity metric; it's the number the unit economics live or die on.

### Human judgment stays critical

The highest-value decisions in the workflow (apply/skip, approve/reject) remain human. The system is built to improve decision *quality*, not to remove the decision maker.

---

## Interface

**Flutter Desktop is the primary interface** — a dedicated workspace where auto-analyzed vacancies arrive in an inbox with fit verdict, barrier breakdown, and CV/cover preview built in. The candidate makes decisions from one screen, not a step-by-step chat flow; Telegram is now push-notification-only.
