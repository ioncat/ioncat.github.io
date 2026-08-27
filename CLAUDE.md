# Claude Code Guidelines — Portfolio (index.html)

**Project:** Single-page portfolio (`index.html`) linking out to individual project repos.
**Follows global strategy:** `E:\My files\0 My_Dev\my_prj\my_claude\` (INTERACTION_RULES.md, DOCUMENTATION_STRATEGY.md)

**Full findings archive:** [`docs/discovery/card-content-inventory.md`](docs/discovery/card-content-inventory.md) — the complete ranked Stack/Method inventory for every card (not just the ~6+6 shown on the card), with what got cut and why. Check it before re-researching a project from scratch, and append to it — don't overwrite — whenever a new exhaustive pass is run (step 5 below).

---

## Project card structure (badge + facets)

Each card in `.cards` follows this layout, in this order:

```html
<div class="card">
  <span class="badge" data-i18n="...">Task type</span>
  <div class="card-title">Project Name</div>
  <div class="card-problem" data-i18n="...">Problem sentence</div>
  <div class="card-body" data-i18n="...">What was built, in prose</div>
  <div class="facets">
    <div class="facet">
      <div class="facet-label" data-i18n="facet.stack">Stack</div>
      <div class="facet-row">
        <span class="stack-chip">Tech</span>
        ...
      </div>
    </div>
    <div class="facet">
      <div class="facet-label" data-i18n="facet.method">Method</div>
      <div class="facet-row">
        <span class="tag">Approach</span>
        ...
      </div>
    </div>
  </div>
  <div class="card-footer">
    <div class="card-links">...</div>
  </div>
</div>
```

- **Badge** — one task type per card (Classification, Summarization, Product case, Automation...). Purpose: a visitor scanning the page top-to-bottom sees at a glance what kind of problem each project solves, and can spot projects that solve the *same kind* of problem (e.g. both summarizer projects carry the same badge). **The badge names the architecture, not the product.** A project's own name can say "Agent" and still not be agentic — check for an actual autonomous decision loop / tool-calling control flow before using an "agent" badge. A deterministic pipeline where code owns control flow and the LLM is just called as a pure function (check for an explicit ADR/doc saying so, e.g. "code runs the model, not the model the code") is Automation, not an agent, no matter what the repo is called.
- **Badge color-coding** — each task-type category has its own accent color (added 2026-08-27 so same-type badges visually group when scanning, not all one indistinguishable violet). Current categories and their modifier class: `badge-case` (violet, the original/default `--badge-*` tokens), `badge-summarization` (teal), `badge-classification` (amber), `badge-automation` (blue). Each is a full token triplet (`--badge-{name}-bg/-text/-dot`) defined in all four theme blocks (`:root`, dark media query, `[data-theme="dark"]`, `[data-theme="light"]`) — same pattern as `--stack-*`/`--tag-*`. **Adding a genuinely new badge category** (not reusing Classification/Summarization/Product case/Automation): pick a hue distinct from the existing four, derive light+dark values the same way (light: soft tint background, saturated text; dark: desaturated dark background, brighter text/dot — copy the pattern from an existing category rather than inventing a new derivation), add the triplet to all four theme blocks, add a `.badge-{name}` + `.badge-{name}::before` CSS rule, and apply both `badge` and `badge-{name}` classes on the span. Don't add a fifth+ category lightly — check first whether the project actually fits one of the four existing ones.
- **Stack / Method chip count** — no fixed cap. Current practice after the first few cards: ~6 Stack + ~6 Method, following the two-pass process in the next section (exhaustive list, then rank and cut with the user). Let actual findings drive the count rather than forcing a number.
- **A real, currently-used dependency can still be wrong for Stack if it's being sunset.** Before adding a dependency to Stack, check whether the repo itself says it's being removed/replaced (a "Phase D removes X", a changelog entry, a README migration note). Shipped-but-scheduled-for-removal is not the same as shipped — same instinct as excluding roadmap items, just the opposite direction in time. Don't just check "does it exist and get imported" — check "does the project's own docs say this is going away."

## MANDATORY: Adding a new project card or updating an existing one

**Do not summarize marketing copy from the README into facets.** Every fact in Stack and Method must be traceable to something you actually read in the target repo during *this* session — re-verify even if a memory or an earlier card already claims it, since repos change.

1. **Read the target repo's README.md** (root, and any linked docs/discovery overview) for the problem statement and what the project claims to do.
2. **Read every dependency manifest for every subproject** — `requirements.txt`/`pyproject.toml`, `package.json`, `pubspec.yaml`, `manifest.json` (Chrome extensions), etc. A subproject can split its dependencies across more than one manifest (e.g. a `requirements.txt` for the always-on pipeline plus a separate `requirements-ml.txt` for a heavier, occasionally-run piece) — list the directory (`ls`/`find` for `requirements*`, `*.toml`, `package*.json`) before concluding you've found them all, don't stop at the first file named `requirements.txt`. This is the source of truth for Stack — README prose lies by omission (a built Web UI, a whole notification channel, or a whole ML sub-pipeline can go unmentioned).
3. **Verify status by reading the source, not just a status marker.** A backlog checkmark or `⏳` is a hint, not proof — open the actual file(s) behind a claimed method before deciding where it belongs. A file that exists and contains a real, runnable implementation (proper training loop, real CLI args, real metrics — not a stub) is shipped work, even if a table elsewhere marks it "in progress" because the *final output* isn't done yet (e.g. a training script is complete and correct, but hasn't been run to convergence with enough data). Reserve the body's "Roadmap: ..." clause strictly for things with **zero code** behind them — a table row and nothing else.
4. **Don't confuse an artifact of the work with the architecture.** E.g. "compared 7 LLM models" is a benchmarking method (→ Method: "LLM evaluation"), not the tech stack (Stack still just lists the one runtime, e.g. "Ollama") — the system is model-agnostic, the number of models tested is a one-time evaluation fact, not a standing dependency.
5. **Surface everything before cutting anything.** Do the full extraction pass first: list every real stack technology and every real method/technique found in the repo, with nothing pre-filtered — more here is recoverable, missing something is not. Only after that full list exists, do a second pass to prioritize:
   - **Recognizability** — how strongly a name signals "modern, current approach" to someone scanning fast (a well-known framework/library beats a boilerplate one; a named technique beats a vague one).
   - **Depth** — how much real sophistication sits behind it (e.g. class-weighted loss + stratified split + a stated metric target reads as deeper than "trained a model").
   - Rank the full list on both axes, propose a cut line, but **show the whole ranked list to the user first** — don't silently ship a pre-trimmed 4–5-chip set. The user decides the final cut; your job is to make that decision easy, not to make it for them unseen.
6. **Third filter, applied on top of the rank: does the chip's name convey value to someone evaluating this as PM/builder work, even if the specific technology is unfamiliar to them?** This is not about whether the name is recognizable — it's about whether the *domain* is guessable from the name alone. `TanStack Query` is unfamiliar to most non-engineers but still reads as "something about data" — passes. `declarativeNetRequest` reads as arbitrary internal API jargon with zero guessable meaning — fails, even to a technical reader without extra context. A chip that names a narrow bugfix or an implementation trick rather than a real capability or method (e.g. "fixed a regex that glued two strings together") also fails this filter regardless of how real the code is — real isn't sufficient, the chip also has to be worth a stranger's attention span. When a chip fails this filter, don't just cut it silently: name it, say why, and let the user decide — same as the ranking cut in step 5. Three ways a chip can fail: (a) pure jargon with no guessable domain → drop, no replacement needed; (b) real capability but the *label* is protocol/implementation-level instead of outcome-level → try renaming to what the user actually experiences before cutting (e.g. "NDJSON parsing" → "Streaming responses") — only cut outright if even the reworded version still adds nothing; (c) a **Stack** chip is a bare library name standing in for a real, distinctive feature that isn't described anywhere else on the card (e.g. "markmap" as the only trace of a mind-map-generation feature) → don't cut it, **move it to Method** under an outcome-level name (here: "Mind map generation") — the feature deserves visibility, the library name just isn't how to give it. Also applies to deployment/infra detail that isn't an architectural choice — e.g. "Docker" alone (as opposed to something distinctive like a documented multi-stage/non-root/healthcheck setup) is just "how it ships," not signal about the product work — cut unless the deployment setup itself is unusually deliberate.
7. If research surfaces a real, shipped feature that the current card body doesn't mention (e.g. a Web UI, a second notification channel), **flag it and propose adding it to the body** — don't silently rewrite prose beyond the facets task without calling it out.

## Translation convention (i18n)

**Localization is currently dormant.** The `<div class="controls">` lang-switcher renders only the `EN` button — `ua`/`ru` have no UI entry point, so those two blocks in the `i18n` object are unreachable right now (as of 2026-08-27, per explicit user direction: not using localization for the time being). Practical effect: when editing card content (body text, new facet chips), **only the `en` block and the inline HTML default need to match** — don't spend effort re-syncing `ua`/`ru` prose to every content edit. The `ua`/`ru` blocks are allowed to drift stale until localization is reactivated; that's expected, not a bug to proactively fix. If the user asks to bring back the language switcher, resync all three blocks against the current `en`/HTML content first (they had already drifted apart before this note was written).

Three languages exist in the `i18n` object in the `<script>` block: `en` (default, live), `ua`, `ru` (defined, currently unreachable via UI).

- **Structural/plain labels** (facet labels, generic category words like "Product case", "Classification", "Automation", "Decision records") — translate via a `t.*` or `facet.*` key, added to all three language blocks.
- **Technical jargon and stack names** (framework/library names, method names like "Prompt engineering", "LLM evaluation", "ETL pipeline", "MVP") — hardcode in English, same string in the HTML regardless of active language, **no `data-i18n` attribute**. This matches existing precedent in the file (`Claude API`, `Full-stack`, `Local LLM`, `MVP` are already English-only across all three languages).
- A new key still needs a placeholder in all three blocks (a missing key silently falls back to showing the key name) — just don't feel obligated to hand-translate `ua`/`ru` prose precisely while localization is dormant.
- Periodically check for orphaned keys (defined in the dict, no matching `data-i18n` in the HTML) — they accumulate whenever a chip/tag is removed or reworded. Delete them; don't leave dead translation entries around "just in case."

## Theme tokens

Card facets use dedicated CSS variables (`--stack-bg/text/border`, `--badge-bg/text/dot`) defined in all four theme blocks (`:root`, `@media prefers-color-scheme: dark`, `[data-theme="dark"]`, `[data-theme="light"]`) alongside the existing `--tag-*` tokens. Reuse these tokens for any new facet-like component — don't introduce a new hardcoded color.

---

**Last updated:** 2026-08-27
