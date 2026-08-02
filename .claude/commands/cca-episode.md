---
description: Turn a Peace Of Code CCA-F YouTube transcript into a verified study guide + flashcard deck in the Obsidian vault
argument-hint: <path-to-transcript.md>
---

# CCA-F Episode → Study Guide + Flashcards

Build a study guide and a flashcard deck from the lecture transcript at **$1**, place them in the matching episode folder in the CCA-F vault, and reconcile every exam-critical claim against official Anthropic docs.

**`<vault>` below means the directory containing `AGENTS.md`** — either the current directory or its `CCA-F/` subdirectory, depending on where the session was started. Resolve it once, up front; every path in this command is relative to it.

---

## Step 0 — Orient

1. Resolve `<vault>`, then read the transcript at `$1`. If that path is relative, try it against both the cwd and `<vault>`.
2. Read `<vault>/AGENTS.md` for current vault conventions. **It is authoritative** — if anything below conflicts with it, AGENTS.md wins.
3. Derive the episode number and title from the transcript's YAML frontmatter (`title:`) and its containing folder name.
4. Find the target folder under `<vault>/06 - Youtube Course/`. Folders are pre-created and named `EP<NN> - <Short Title>`. Match on episode number, **not** exact title text — the folder titles are shortened.
   - If no folder matches, create one following the existing naming pattern.
5. Note the episode's domain mapping (D1–D5) from the transcript's description/tags, for cross-linking.

**Do not ask clarifying questions.** Level, format, and structure are all fixed below.

---

## Step 1 — Write the study guide

**File:** `<vault>/06 - Youtube Course/EP<NN> - <Title>/EP<NN> - <Title>.md`
(Note the doubled name: the note filename repeats the folder name, so `[[wikilinks]]` resolve.)

### Calibration — fixed, do not re-derive

| Setting | Value |
|---|---|
| Level | College / professional-certification |
| Exam format | MCQ-heavy → question mix **40 recall / 40 comprehension / 20 application** |
| Question count | <500 words: 5–8 · 500–5000: 10–15 · >5000 or multi-topic: 15–20 |
| Answer key | Inline `<details>` per question |
| Diagrams | Inline Mermaid, minimum 1 |

### Structure — 7 sections, in order

Use the `lecture-to-study-guide` skill's 7-section skeleton, **rendered in vault conventions**:

1. **Outline** — TOC with anchor links. Keep emoji out of H2/H3 headings so anchors resolve.
2. **Key Terms** — table: Term | Definition | Source (use `[MM:SS]` timestamps).
3. **Concept Summaries** — one `### N.M` subsection per topic group, SQ3R framed: italic *Question:* opener, 2–5 paragraphs, bold **In your own words:** closer, `*See PQ n, n.*` cross-ref.
4. **Diagrams** — Mermaid, each with a one-line caption below it.
5. **Worked Examples** — numbered steps with an italic *(why)* note per step and a bold **Answer:**. Use LaTeX (`$...$`, `$$...$$`) for anything quantitative — never unicode operators.
6. **Practice Questions** — per the count/mix above, each with a `*(§ref / Term)*` citation.
7. **Cheat Sheet** — Cornell two-column Cue | Note table, **Top 5 terms**, a `[!WARNING]` anti-pattern block, and a `> **Synthesis:**` blockquote. ≤500 words.

Then append, per vault convention:
- `## ✅ Practice Checklist` — checkbox list of self-test criteria
- `*Next: [[EP<NN+1> - <Title>]]*` — link the next episode's folder name even if it doesn't exist yet

### Vault conventions — mandatory

- **Frontmatter:** `tags` (include `CCA-F`, `domain-<n>`, `youtube-course`), `date` (today), `status: not-started`, `domain: "N of 5"`, `source:` (channel + episode).
- **Header block:** `[!NOTE] Exam Coverage` callout naming the domain + exam weight + task statements, then a line with `**Back to:** [[CCA-F Study Roadmap]] · **Domain note:** [[D<N> - ...]] · **Deck:** [[EP<NN> - Flashcards]]`, then source URL / level / question-mix, then `**Previous:** [[EP<NN-1> - ...]]`.
- **Callouts:** `[!NOTE]` `[!TIP]` `[!WARNING]` (anti-patterns) `[!IMPORTANT]` (must-know) `[!EXAMPLE]`. Use ❌ / ✅ for wrong-vs-right.
- **Links:** `[[wikilinks]]` only for internal notes. Never `[text](path)`.
- **Code-format** every exam term: API fields (`stop_reason`), tool names (`Task`), SDK objects (`AgentDefinition`), config files (`CLAUDE.md`), CLI flags (`--resume`), env vars (`ANTHROPIC_API_KEY`).

---

## Step 2 — Verify against official docs (do not skip)

The transcripts are a third-party YouTube course and **contain real errors**. Verify every exam-critical claim before writing it as fact.

**Method:**
1. Invoke the `claude-api` skill for Claude API surface (models, `stop_reason`, tool use, batching, caching, structured output).
2. `WebFetch` official docs for anything the skill doesn't cover — it does **not** cover the Claude Agent SDK:
   - Agent SDK (`AgentDefinition`, subagents, hooks, sessions, tools): `https://code.claude.com/docs/en/agent-sdk/<page>.md`
   - Claude Code config/CLI: `https://code.claude.com/docs/en/<page>.md`
   - Messages API: `https://platform.claude.com/docs/en/api/<page>` — note `docs.anthropic.com` **301-redirects** here; follow the redirect.
   - Doc index: `https://code.claude.com/docs/llms.txt`
3. **Cross-check the vault's own `D1`–`D5` notes.** They are verified against cited sources and frequently more current than the video — but verified *at a point in time*, so official docs still outrank them.
   - **Video vs vault note** → the vault wins. Flag it in the episode note; **do not** edit `01`–`05` to match a video.
   - **Vault note vs official docs** → **docs win.** Fix the note, cite the doc URL, and report it prominently in the summary — never fold it in as a footnote.
   - Default writes to `06 - Youtube Course/`. Touching `01`–`05` requires a doc citation.

**Recording verified conflicts:** never silently "fix" the lecture. Show both, in a callout:

```markdown
> [!WARNING] <what the lecture got wrong> — verified against official docs
> The lecture says X. Officially, Y. **Exam answer: <which one>.** Real code: <which one>.
> Source: <url> · consistent with [[D<N> - ...]]
```

Use `[!IMPORTANT]` when the correction is a must-know rather than a trap. Tag pure model elaboration `**(expansion)**`. If something can't be verified, flag it `> [!WARNING] Unverified — confirm against official study guide`.

**Also flag transcription artifacts** in a `[!TIP]` — the auto-transcripts mangle terms (e.g. "enter" for `end_turn`, "Silhouette outputs" for *siloed outputs*). Note them so they don't cause second-guessing during video review.

**Known recurring conflicts** — check these every time they come up:
- `stop_reason` has **seven** values; **three** drive loop control (`end_turn`, `tool_use`, `pause_turn`).
- The subagent spawn tool was renamed **`Task` → `Agent`** in Claude Code v2.1.63. Exam answer: `Task`.
- `AgentDefinition` requires **only** `description` + `prompt`. The inner field is `tools`, not `allowedTools`.
- Subagent nesting is **allowed** by default (3 layers); hub-and-spoke is a design principle, not an SDK constraint.

---

## Step 3 — Write the flashcard deck

**File:** `<vault>/06 - Youtube Course/EP<NN> - <Title>/EP<NN> - Flashcards.md`

1. **Make the deck self-contained — do not dedupe against `04 - Exam Cram/Flashcards.md`.** Overlap with the vault-wide deck is expected and fine. This deck must stand alone as a complete review of the episode, so never omit a card just because it exists elsewhere. Still never *append* to the shared deck.
2. **Apply the quality bar** (AGENTS.md § When writing flashcard notes). Test every card: *could someone who skimmed the note without understanding it still answer this?* If yes, rewrite or cut. Cards must be atomic, self-contained, not guessable from their own phrasing, and have one defensible answer. Prefer *why* / *when* / *what breaks* over *what is it called* — except for exact identifiers the exam checks literally (`stop_reason` values, field names, tool names), which stay plain recall.
3. If a shared-deck card is contradicted by **official docs** (not merely by the video), edit it in place — don't add a competing card — cite the doc URL, and flag it prominently in the summary as a change to verified content. If only the *video* disagrees, leave the card alone.
4. Format per vault convention: `**Q: <question>**` on one line, `A: <answer>` on the next, grouped under `## Domain N — <topic>` headers. Answers 1–3 sentences; a table is fine where it earns its place.
5. Frontmatter mirrors the study guide's but adds the `flashcards` tag.
6. Header: `[!NOTE] How to Use This Deck` callout + `**Related:**` wikilinks.
7. Close with `*Back to: [[EP<NN> - <Title>]]*`.

---

## Step 4 — Validate before reporting

- [ ] All 7 sections present and non-empty
- [ ] Key Terms ≥ 8 rows
- [ ] Practice questions hit the count for the transcript's length, mix ≈ 40/40/20
- [ ] ≥1 Mermaid block, syntactically valid (no unquoted parens/colons in node labels)
- [ ] TOC anchors resolve against actual heading text
- [ ] Cheat sheet ≤500 words
- [ ] Every exam-critical claim either matches official docs or carries a conflict callout with a source URL
- [ ] `[[wikilinks]]` used throughout; prev/next/domain/deck links all present
- [ ] Shared deck card count unchanged unless a stale card was deliberately edited

---

## Report back

Keep it short. Lead with the two file links, then:
- Question/card counts
- **Every conflict found in Step 2** — what the lecture said, what the docs say, which to use for the exam
- Transcription artifacts flagged
- Cards excluded as duplicates, and any shared-deck card edited
- Anything left unverified, stated plainly

## Do not

- Do not append episode cards to `04 - Exam Cram/Flashcards.md`
- Do not create planning/tracking files in the vault — content only
- Do not invent exam content; mark elaboration `(expansion)` and unknowns `[!WARNING] Unverified`
- Do not silently pick a side when the video and the docs conflict — show both
- Do not use `[text](path)` links inside vault notes
