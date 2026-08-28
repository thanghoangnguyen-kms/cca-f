# AGENTS.md — CCA-F Study Vault

> This file instructs AI agents (Copilot, Claude Code, etc.) on how to work inside this Obsidian vault.
> It supplements `.github/copilot-instructions.md` with project-specific conventions for the CCA-F exam prep context.

## 📚 Which instruction file owns what

Three files exist. Read them in this order, and don't duplicate rules between them — duplication is how they drift apart.

| File | Role | Edit it when |
|------|------|--------------|
| **`AGENTS.md`** (this file) | **Authoritative spec.** Structure, frontmatter, callouts, content rules, sources, constraints. | Any convention changes |
| `CLAUDE.md` | Short orientation for Claude Code, which auto-loads it. Points here for detail; carries only the always-in-context essentials. | Structure changes, or a rule that must apply without reading this file |
| `.github/copilot-instructions.md` | GitHub Copilot's equivalent. **Managed separately — do not modify.** | Never |

If `CLAUDE.md` and this file disagree, **this file wins** — then fix `CLAUDE.md`.

---

## 📌 Project Identity

- **Vault name:** `CCA-F`
- **Purpose:** Exam preparation for the **Claude Certified Architect – Foundations** certification — exam code **CCAR-F**, an Anthropic credential delivered by **Pearson VUE**
- **Format:** Obsidian vault — all notes are `.md` files with Obsidian-flavored Markdown

---

## 🗂️ Vault Structure

Content is organized into numbered folders by study order. **`00 - START HERE.md` (vault root) is the entry point / map of content.**

```
CCA-F/
├── 00 - START HERE.md              ← entry point (MOC); check this first
├── 01 - Roadmap/
│   ├── Official Exam Blueprint.md  ← AUTHORITATIVE exam spec (weights, 30 task statements,
│   │                                  6 scenarios, in/out of scope, logistics)
│   └── CCA-F Study Roadmap.md      ← 6-week plan + progress tracker
├── 02 - Courses/                   ← one lesson-doc per required course
│   ├── C1 - Claude 101.md
│   ├── C2 - Claude Platform 101.md
│   ├── C3 - Introduction to MCP.md
│   ├── C4 - Claude Code 101.md
│   ├── C5 - Introduction to Subagents.md
│   └── C6 - Building with the Claude API.md
├── 03 - Domains/
│   ├── 00 - Claude Model Family & API Fundamentals.md
│   └── D1 … D5 - <Topic>.md         ← the 5 exam-domain deep dives
├── 04 - Exam Cram/
│   ├── Flashcards.md                ← vault-wide deck, grouped by domain
│   ├── Critical Terms Glossary.md
│   └── Handbook/                    ← scenario-based cram (00–09, plain MD links)
├── 05 - Practice/                   ← one folder per exam set, `<source> - <set>`; notes at the root
│   ├── README.md                    ← index; overlap matrix for the six sets
│   ├── Weak Areas Deep Dive.md      ← personal mistake log
│   ├── Exam Guide - Sample Questions/    ← OFFICIAL (guide § 9); highest-authority key
│   │   └── README.md · Questions.md · Answer Key.md
│   ├── Vault-authored - Claude Code Scenario Drills/  ← official scenarios 2 & 5; written
│   │   └── README.md · Questions.md · Answer Key/     for this vault, NOT from a bank
│   ├── CyberSkill CCAF - New Mock Exam/   ← questions + matched key
│   │   ├── README.md · Questions.md
│   │   └── Answer Key/<domain>.md   ← 4 scenario domains
│   ├── CyberSkill CCAF - Timed Mock 2026-08-24/  ← 3rd sitting; stems only (no options),
│   │   └── README.md · Questions.md             all 60 answers grader-authoritative
│   ├── CyberSkill CCAF - Mock Exam/     ← key only; 2nd sitting of the SAME bank (42/60
│   │   └── README.md · <domain>.md        overlap) — kept separate, do NOT merge
│   ├── CCA-F Question Bank/         ← certificationpractice.com; questions only, no key
│   │   └── README.md · CCA-F-practice-exam-questions.md
│   └── CyberSkill CCAF - Unified Bank/  ← DERIVED: the 3 sittings above deduplicated;
│       ├── README.md                      80 distinct items (U1–U80) + the crosswalk
│       ├── Questions.md                   U1–U60 full MCQ · U61–U80 open-response
│       └── Answer Key/unified-<domain>.md  ← prefixed, so NOT part of the collision
│   # the THREE original answer-key folders share the same 4 domain filenames, so they
│   # link with relative MD, not [[wikilinks]] — from anywhere, not just inside a set
│   # folder (Answer Patterns Index sits at the root and does the same). Keep intact.
│   # The Unified Bank prefixes its four `unified-` precisely to stay out of that
│   # collision, so its keys CAN be wikilinked: [[unified-research_pipeline]]
├── 06 - Youtube Course/             ← per-episode notes from the Peace Of Code course
│   └── EP<NN> - <Title>/            ← one folder per episode (pre-created)
│       ├── EP<NN> - <Title>.md      ← study guide (filename repeats folder name)
│       └── EP<NN> - Flashcards.md   ← episode-scoped deck
├── youtube-transcript/              ← SOURCE MATERIAL — read-only input, not study content
│   └── peace-of-code/<episode-slug>/transcript.md
├── AGENTS.md · CLAUDE.md · README.md
├── skills-lock.json                 ← pinned external skill (baoyu-youtube-transcript)
├── .claude/                         ← ALL agent tooling, version-controlled
│   ├── commands/cca-episode.md      ← /cca-episode — transcript → guide + deck
│   └── skills/                      ← lecture-to-study-guide, mermaid-diagrams, claude-md-improver
├── .agents/skills/                  ← parallel copies for non-Claude agents (untracked, not auto-synced)
└── .github/copilot-instructions.md
```

> **Start sessions from inside `CCA-F/`.** Claude Code resolves `.claude/commands/` and `.claude/skills/` against its start directory. Launched from the parent Obsidian Vault folder, none of this tooling is discovered.

Domain notes are named `D<N> - <Topic>.md`; course notes `C<N> - <Course>.md`; episode notes `EP<NN> - <Title>.md`. Because Obsidian resolves `[[wikilinks]]` by **filename**, notes can move between folders without breaking links — but the `Handbook/` files use relative `[text](file.md)` links, so keep that folder intact as a unit.

> **`youtube-transcript/` is input, not output.** Transcripts are third-party auto-generated captions with known errors. Never treat them as authoritative, never edit them, and never link to them from study notes.

---

## 📝 Obsidian Markdown Conventions

### Always use

| Convention | Syntax | Example |
|-----------|--------|---------|
| Internal links | `[[Note Name]]` | `[[D1 - Agentic Architecture & Orchestration]]` |
| Callouts | `> [!TYPE] Title` | `> [!WARNING] Anti-Pattern` |
| Frontmatter | YAML between `---` delimiters | See template below |
| Tags | `tags:` in frontmatter | `tags: [CCA-F, domain-1]` |

### Callout types in use

| Type | Purpose |
|------|---------|
| `[!NOTE]` | General information, exam context |
| `[!TIP]` | Study tips, memory aids |
| `[!WARNING]` | Anti-patterns, common exam traps |
| `[!IMPORTANT]` | Critical exam facts, must-know distinctions |
| `[!EXAMPLE]` | Code examples, scenarios |

### Never use

- Standard Markdown links `[text](path)` for internal vault links — use `[[wikilinks]]` instead
- Hardcoded absolute file paths in note content

---

## 📄 Note Frontmatter Template

```yaml
---
tags:
  - CCA-F
  - <domain-tag>        # e.g. domain-1, domain-2, mcp, prompt-engineering
date: YYYY-MM-DD
status: not-started     # not-started | in-progress | done | needs-review
domain: "N of 5"        # e.g. "1 of 5" — omit for notes that span domains
---
```

Additional keys by note type:

| Key | Used by | Value |
|-----|---------|-------|
| `source` | `06 - Youtube Course/` notes | Channel + episode, e.g. `"Peace Of Code — Claude Certified Architect Ep 01"` |
| `flashcards` (a *tag*, not a key) | any deck | Add to `tags` so decks are filterable across folders |

---

## ✍️ Content Rules

### When writing domain notes (D1–D5)

1. **Open with** a `[!NOTE]` callout summarizing exam coverage for that domain
2. **Include a "Back to" link** at the top: `**Back to:** [[CCA-F Study Roadmap]]`
3. **Structure subdomains** as `## N.M — Subdomain Title` (e.g., `## 1.3 — Subagent Invocation`)
4. **Use comparison tables** for concepts with trade-offs (e.g., hooks vs prompt instructions)
5. **Mark anti-patterns** with `[!WARNING]` callouts and ❌ / ✅ symbols
6. **Mark exam-critical facts** with `[!IMPORTANT]` callouts
7. **End every domain note** with:
   - A `## ✅ Practice Checklist` section (checkboxes)
   - A `## 🃏 Quick-Reference Flash Cards` section (Q&A pairs)
   - A `*Next: [[D<N+1> - ...]]* ` link

### When writing YouTube course notes (`06 - Youtube Course/`)

Generated by the **`/cca-episode <transcript-path>`** command, which owns the full procedure. The conventions it enforces:

1. **One folder per episode**, `EP<NN> - <Short Title>/`, containing exactly two notes: the study guide (**filename repeats the folder name**, so `[[wikilinks]]` resolve) and `EP<NN> - Flashcards.md`.
2. **Seven sections** in the guide: Outline · Key Terms · Concept Summaries · Diagrams · Worked Examples · Practice Questions · Cheat Sheet — then a Practice Checklist and a `*Next: [[EP<NN+1> - ...]]*` link.
3. **Keep emoji out of `##`/`###` headings** so the Outline's anchor links resolve. Emoji in the `#` title only.
4. **Cite timestamps** as `[MM:SS]` in the Key Terms `Source` column.
5. **Verify every exam-critical claim against official docs before writing it as fact** — see *Research & Content Sources* below. The transcripts contain real errors.
6. **Never silently correct the lecture.** Show both sides in a callout, and name which to use for the exam:
   ```markdown
   > [!WARNING] <what the lecture got wrong> — verified against official docs
   > The lecture says X. Officially, Y. **Exam answer: <which>.** Real code: <which>.
   > Source: <url> · consistent with [[D<N> - ...]]
   ```
7. **Flag transcription artifacts** in a `[!TIP]` — auto-captions mangle terms (e.g. "enter" for `end_turn`, "Silhouette outputs" for *siloed outputs*).
8. Cross-check the vault's own `D1`–`D5` notes; they are often more current than the video. If they disagree with a transcript, the vault usually wins — say so explicitly.
9. **Link the finished episode into the indexes** — `00 - START HERE.md` (flip `⬜` → `✅`, wikilink the name) and `CCA-F Study Roadmap.md` (wikilink the name in its *Companion episodes* line). An episode note that no index links to is unreachable. The wikilink must match the note's **filename** — the shortened folder name, not the transcript's `title:`. `README.md`, `CLAUDE.md`, and this file carry no per-episode list and need no update.

### When writing flashcard notes

**Format:**

- `**Q: <question>**` followed by `A: <answer>` on the next line
- Group by domain with `## Domain N` headers
- Keep answers to 1–3 sentences max (a small table is fine where it earns its place)

**The quality bar — a card must be answerable only by someone who actually understands the material.**

Apply this test to every card before keeping it:

> *Could someone who skimmed the note without understanding it still answer this?*
> If yes, the card is testing recognition, not knowledge. Rewrite or cut it.

What that means in practice:

| Rule | Why |
|------|-----|
| **Atomic** — one fact per card | A card testing three things can't tell you *which* one you failed |
| **Self-contained** — no "the three things above", no pronouns pointing at the note | Cards get reviewed out of order, months later, with no surrounding context |
| **Answer not guessable from the question's phrasing** | If the wording gives it away, you're rehearsing a sentence, not recalling a fact |
| **Prefer *why* / *when* / *what breaks* over *what is it called*** — for anything with a trap | Naming things is recognition; explaining a failure mode is understanding. The exam tests the latter |
| **One defensible answer** | If two answers are arguable, you'll mark yourself right when you were wrong |

❌ *"What is context isolation?"* — the term explains itself; the question hands over the answer
✅ *"A subagent returns duplicated, off-target output. What is the root cause?"* — only answerable if you know the failure mode

Cards that *are* worth keeping as plain recall: exact identifiers and named sets the exam checks literally — `stop_reason` values, `AgentDefinition` fields, the four coordinator responsibilities. Don't dress those up. **Pair them** with a card that tests the same material at depth, so the deck covers both what the exam asks and whether you actually understand it.

**Two tiers of deck:**

| Deck | Scope | Rule |
|------|-------|------|
| `04 - Exam Cram/Flashcards.md` | Vault-wide, grouped by domain | **Never append episode cards, and never edit a card to match a video.** Edit in place only when *official docs* contradict it — then cite the URL and call it out prominently in the summary |
| `06 - Youtube Course/EP<NN> - .../EP<NN> - Flashcards.md` | One episode | **Self-contained. Do not dedupe against the vault-wide deck** — overlap is expected and fine. An episode deck must stand alone as a complete review of that episode, so omitting a card because it exists elsewhere leaves a hole when studying that episode by itself |

### When referencing exam-specific terms

Always use **code formatting** for:
- CLI flags: `--resume`, `--print`, `--output-format json`
- API fields: `stop_reason`, `tool_choice`, `custom_id`, `isError`
- Hook names: `PreToolUse`, `PostToolUse`, `SubagentStart`
- Config files: `CLAUDE.md`, `.mcp.json`, `~/.claude.json`
- Commands: `/memory`, `/compact`
- SDK objects: `AgentDefinition`, `ResultMessage`, `ClaudeSDKClient`

---

## 🔎 Research & Content Sources

When generating or expanding content, prioritize these authoritative sources:

| Source | Use For |
|--------|---------|
| https://code.claude.com/docs/en/overview | Claude Code — CLI, config, hooks, skills |
| https://code.claude.com/docs/en/agent-sdk/ | **Agent SDK** — `AgentDefinition`, subagents, sessions, tools |
| https://code.claude.com/docs/llms.txt | Full doc index — find specific pages |
| https://platform.claude.com/docs/en/api/overview | Claude API, models, `stop_reason`, tool use |
| https://anthropic.skilljar.com | Official Anthropic courses |
| **Official CCA-F Exam Guide** (Anthropic Partner Academy PDF, v1.0 · July 2026) | **The blueprint itself** — domain weights, all 30 task statements, the 6 scenarios, in-scope/out-of-scope lists, 12 sample questions. Transcribed into [[Official Exam Blueprint]] |
| https://www.certsafari.com/anthropic/claude-certified-architect | Practice questions (614). **Not** authoritative for exam structure — the official guide is |
| https://github.com/FlorianBruniaux/claude-code-ultimate-guide | Deep architecture, security, workflows |
| https://github.com/anthropics/claude-cookbooks | Hands-on code examples |

**Always verify facts against official docs before writing exam-critical content.**

### Authority hierarchy

**Official docs are the arbiter.** Everything else is evidence, ranked:

| Tier | Source | Trust |
|------|--------|-------|
| 0 | **Official CCA-F Exam Guide** | **Authoritative on the exam itself** — weights, structure, scope, logistics. Beats the docs on what is *tested*; the docs still win on what is *true* |
| 1 | **Official Anthropic docs** (table above) | **Authoritative on technical fact.** Beats everything, including this vault |
| 2 | `01`–`05` vault notes | **Verified** against cited sources — but verified *at a point in time*, and the SDK moves. High trust, not infallible |
| 3 | `06 - Youtube Course/` · `youtube-transcript/` | **Unverified third-party.** Auto-captions of a YouTube course, with known errors |

Resolution rules:

- **Video vs vault note** → the vault note wins. Flag the conflict in the episode note; leave `01`–`05` alone.
- **Video vs official docs** → docs win. Record both sides per *When writing YouTube course notes* rule 6.
- **Vault note vs official docs** → **docs win.** This is a real finding, not an inconvenience — surface it loudly and fix the vault note, citing the doc URL and the date checked.

### Scope of work vs scope of authority

Keep these separate:

- **Scope of work:** episode tasks write to `06 - Youtube Course/` only, with **one narrow exception** — after authoring an episode, they update the two index files that list episodes (`00 - START HERE.md` and `01 - Roadmap/CCA-F Study Roadmap.md`) so the new note is reachable. That exception is **link-only**: flip the `⬜` → `✅` marker and wrap the episode name in `[[...]]`. Nothing else. Do **not** launch a background audit of `01`–`05`, and don't "reconcile" verified notes to match a video.
- **Scope of authority:** if official docs contradict something in `01`–`05` *while you're passing through*, that outranks the note. Report it prominently, fix it with a source citation, and never bury it inside an unrelated summary.

Assume neither that the existing notes are wrong nor that they're beyond checking. They earn trust from their sources — so when a source has moved on, so should the note.

> [!IMPORTANT] Two gotchas when fetching docs
> - **`docs.anthropic.com` 301-redirects to `platform.claude.com`.** Fetchers that don't follow cross-host redirects will fail — request the `platform.claude.com` URL directly.
> - **The bundled `claude-api` skill does not cover the Claude Agent SDK.** It covers the Claude API and Managed Agents. `AgentDefinition`, subagents, and the `Task`/`Agent` tool live in the Agent SDK docs and must be fetched separately.

### Known corrections to the YouTube course

The video course states these incorrectly. Each was verified against official docs and already appears in the relevant `D1`–`D5` note — recheck whenever an episode touches them:

| Claim | Verified position |
|-------|-------------------|
| `stop_reason` values | **Seven** exist in the docs; **three** drive loop control: `end_turn`, `tool_use`, `pause_turn`. **Scope:** the exam blueprint names only `tool_use` + `end_turn` — that is the exam answer; the seven-value table is production correctness. See [[Official Exam Blueprint]] § 5 |
| Subagent spawn tool | Renamed **`Task` → `Agent`** in Claude Code v2.1.63. `Task` remains a valid alias and stays the **exam-safe answer** |
| `AgentDefinition` required fields | **Only `description` + `prompt`.** The inner field is `tools`, not `allowedTools` |
| Subagent nesting | **Allowed** by default (3 layers). Hub-and-spoke is a design principle, not an SDK constraint |

---

## 🚫 Constraints

- **Do not** create planning or tracking files (todos, project notes, etc.) inside the vault — this is a study content vault only
- **Do not** create study content outside the vault root. *Scope: this governs **notes**. Agent tooling — slash commands, skills, settings — belongs in the vault's own `.claude/`, so it stays version-controlled with the notes it serves. Don't install this vault's tooling into `~/.claude/`.*
- **Do not** modify `.github/copilot-instructions.md` — that is managed separately
- **Do not** edit anything under `youtube-transcript/` — it is read-only source material
- **Do not** invent exam content. If uncertain, note it as `> [!WARNING] Unverified — confirm against official study guide`
- **Keep notes exam-focused** — avoid tangential deep dives that don't map to the 5 official domains

---

## 🗃️ Domain → Subdomain Map (Quick Reference)

| Domain | File | Subdomains |
|--------|------|-----------|
| D1: Agentic Architecture | `D1 - Agentic Architecture & Orchestration.md` | 1.1 Agent loop · 1.2 Multi-agent · 1.3 Subagent spawning · 1.4 Workflows & handoffs · 1.5 Hooks · 1.6 Task decomposition · 1.7 Session management |
| D2: Tool Design & MCP | `D2 - Tool Design & MCP Integration.md` | 2.1 Tool interfaces · 2.2 Error responses · 2.3 Tool distribution · 2.4 MCP servers · 2.5 Built-in tools |
| D3: Claude Code Config | `D3 - Claude Code Configuration & Workflows.md` | 3.1 CLAUDE.md hierarchy · 3.2 Commands & skills · 3.3 Path-specific rules · 3.4 Plan mode · 3.5 Iterative refinement · 3.6 CI/CD |
| D4: Prompt Engineering | `D4 - Prompt Engineering & Structured Output.md` | 4.1 Explicit criteria · 4.2 Few-shot · 4.3 JSON schemas · 4.4 Validation & retry · 4.5 Batch processing · 4.6 Multi-instance review |
| D5: Context & Reliability | `D5 - Context Management & Reliability.md` | 5.1 Long context · 5.2 Escalation · 5.3 Error propagation · 5.4 Codebase exploration · 5.5 Human review · 5.6 Provenance |
