# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working in this repository.

## What this is

An **Obsidian vault** (`CCA-F`) of study notes for the **Claude Certified Architect – Foundations** exam. It is a content repository — Markdown notes only. There is **no build, test, lint, or run step**; "working in this repo" means writing and editing `.md` notes.

> [!IMPORTANT]
> **`AGENTS.md` in the vault root is the authoritative spec** for structure, frontmatter, callouts, content rules, sources, and constraints. **Read it before writing or editing any note.**
>
> This file is the orientation map and carries only what must apply *without* reading AGENTS.md. Detail lives there, deliberately not here — duplicating it is how the two files drift apart. If they ever disagree, **AGENTS.md wins**, then fix this file.

## Structure

Content lives in numbered folders by study order. `00 - START HERE.md` (root) is the entry-point map.

| Folder | Contents |
|--------|----------|
| `01 - Roadmap/` | `CCA-F Study Roadmap.md` — 6-week plan + progress tracker |
| `02 - Courses/` | `C1`–`C6`, one lesson-doc per required training course |
| `03 - Domains/` | `00 - Claude Model Family & API Fundamentals.md` + `D1`–`D5` domain deep dives |
| `04 - Exam Cram/` | `Flashcards.md` (vault-wide deck), `Critical Terms Glossary.md`, `Handbook/` |
| `05 - Practice/` | Mistake log, practice questions, mock exam answer key |
| `06 - Youtube Course/` | `EP<NN> - <Title>/` — one folder per episode, each holding a study guide + its own deck |
| `youtube-transcript/` | **Source material — read-only input, never edit, never link to from notes** |

## The three rules most easily gotten wrong

1. **Two link conventions coexist — match the file you're editing.** Everything uses `[[wikilinks]]` **except** `04 - Exam Cram/Handbook/`, which is plain GitHub Markdown with relative `[text](file.md)` links. Keep that folder intact as a unit so its links don't break.
2. **Episode notes repeat their folder name** — `EP01 - Foo/EP01 - Foo.md` — because Obsidian resolves `[[wikilinks]]` by filename. Don't "tidy" this.
3. **Never append episode flashcards to the vault-wide `Flashcards.md`.** Episode decks stay in their episode folder and are **self-contained** — overlap with the shared deck is expected, so don't dedupe against it.

## Conventions (summary — see AGENTS.md for the spec)

- Internal links: `[[Note Name]]`, never `[text](path)`.
- Callouts: `[!NOTE]` `[!TIP]` `[!WARNING]` (anti-patterns) `[!IMPORTANT]` (must-know) `[!EXAMPLE]`. Use ❌ / ✅ for wrong-vs-right.
- Code-format every exam term: API fields (`stop_reason`), tool names (`Task`), SDK objects (`AgentDefinition`), config files (`CLAUDE.md`, `.mcp.json`), CLI flags (`--resume`), hooks (`PreToolUse`).
- YAML frontmatter on every note: `tags`, `date`, `status`, `domain`.
- Domain notes (`D1`–`D5`) have a required opening and closing shape — AGENTS.md § Content Rules.

## Authority hierarchy

**Official Anthropic docs are the arbiter.** Everything else is ranked evidence:

1. **Official docs** — authoritative, beats everything including this vault
2. **`01`–`05` notes** — verified against cited sources, but verified *at a point in time*; the SDK moves. High trust, not infallible
3. **`06 - Youtube Course/` and `youtube-transcript/`** — unverified third-party auto-captions with known errors

Resolution: video vs vault note → **the note wins** (flag it, leave `01`–`05` alone). Video vs docs → **docs win** (show both, name the exam answer). Note vs docs → **docs win** — that's a real finding; surface it loudly and fix the note with a source citation.

**Scope of work vs scope of authority are different things.** Episode tasks write to `06` only — no background audits of `01`–`05`, no reconciling verified notes to match a video. But if official docs contradict a vault note while you're passing through, that outranks the note: report it prominently, don't bury it.

See AGENTS.md § Research & Content Sources for the source table, the **known corrections** to recheck (`stop_reason` value count, the `Task`→`Agent` rename, `AgentDefinition` required fields, subagent nesting), and two fetching gotchas that will otherwise waste a cycle. Mark anything unconfirmed `> [!WARNING] Unverified`.

## Constraints

- **Do not** create planning/tracking/todo files in the vault — content only.
- **Do not** create study *content* outside the vault root. Agent tooling (slash commands, skills) belongs in `~/.claude/` or `.claude/` and is exempt.
- **Do not** modify `.github/copilot-instructions.md` (managed separately).
- **Do not** edit anything under `youtube-transcript/`.

## Tooling

All agent tooling is **vault-local and version-controlled** under `.claude/` — nothing lives in `~/.claude/`.

| Path | What |
|------|------|
| `.claude/commands/cca-episode.md` | **`/cca-episode <transcript-path>`** — builds a verified study guide + flashcard deck for one YouTube episode. Owns that whole workflow; prefer it over ad-hoc note generation |
| `.claude/skills/lecture-to-study-guide/` | The 7-section study-guide skeleton `/cca-episode` builds on |
| `.claude/skills/mermaid-diagrams/` · `claude-md-improver/` | Vault-local skills |
| `.agents/skills/` | Parallel copies for non-Claude agents — currently untracked, and **not** kept in sync automatically |
| `skills-lock.json` | Pins the external `baoyu-youtube-transcript` skill |

> [!IMPORTANT] Start sessions from inside `CCA-F/`
> Claude Code discovers `.claude/commands/` and `.claude/skills/` relative to the directory it was **started in**. Launched from the parent Obsidian Vault folder, none of the above is visible — `/cca-episode` won't exist. `cd` into `CCA-F/` first.
- Shell commands route through `rtk`, a token-optimizing proxy, via a global hook — `find`/`grep` become `rtk find`/`rtk grep`. Transparent; no action needed.
