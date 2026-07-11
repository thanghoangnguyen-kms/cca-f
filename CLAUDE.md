# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

An **Obsidian vault** (`CCA-F`) of study notes for the **Claude Certified Architect – Foundations** exam. It is a content repository — Markdown notes only. There is **no build, test, lint, or run step**; "working in this repo" means writing and editing `.md` notes.

**`AGENTS.md` in the vault root is the authoritative spec** for conventions, frontmatter, callout types, content rules, and constraints. Read it before writing or editing notes — the rules below are the high-level map; AGENTS.md has the details.

## Structure

Content lives in numbered folders by study order. `00 - START HERE.md` (root) is the entry-point map of content.

- `01 - Roadmap/` — `CCA-F Study Roadmap.md`, the 6-week plan + progress tracker.
- `02 - Courses/` — `C1`–`C6` lesson-docs, one per required training course ("what to know after finishing it").
- `03 - Domains/` — `00 - Claude Model Family & API Fundamentals.md` plus `D1`–`D5`, the per-domain deep dives.
- `04 - Exam Cram/` — `Flashcards.md`, `Critical Terms Glossary.md`, and `Handbook/` (a standalone scenario-based cram set, `00`–`08`).
- `05 - Practice/` — `Weak Areas Deep Dive.md`, a personal mistake log.

**Two link conventions coexist — match the file you're editing.** All notes except the handbook use **Obsidian-flavored Markdown**: `[[wikilinks]]` (resolved by filename, so notes move between folders freely), `> [!TYPE]` callouts, YAML frontmatter, tags. The `04 - Exam Cram/Handbook/` files are plain GitHub Markdown with **standard `[link](file.md)` relative links** — keep that folder intact as a unit so its links don't break.

## Conventions (root notes)

- Internal links use `[[Note Name]]`, never `[text](path)`.
- Every domain note opens with a `[!NOTE]` exam-coverage callout and a `**Back to:** [[CCA-F Study Roadmap]]` link, structures subdomains as `## N.M — Title`, and ends with a Practice Checklist, Quick-Reference Flash Cards, and a `*Next: [[...]]*` link.
- Callouts: `[!NOTE]` `[!TIP]` `[!WARNING]` (anti-patterns) `[!IMPORTANT]` (must-know facts) `[!EXAMPLE]`. Use ❌ / ✅ for wrong-vs-right comparisons.
- Code-format all exam terms: CLI flags (`--resume`), API fields (`stop_reason`), hook names (`PreToolUse`), config files (`CLAUDE.md`, `.mcp.json`), SDK objects (`AgentDefinition`).
- Frontmatter template with `tags`, `date`, `status` — see AGENTS.md.

## Constraints

- **Do not** create planning/tracking/todo files in the vault — content only.
- **Do not** create files outside the vault root.
- **Do not** modify `.github/copilot-instructions.md` (managed separately).
- **Do not** invent exam content. Verify against the official sources listed in AGENTS.md, and flag anything uncertain with `> [!WARNING] Unverified`.

## Note on tooling

The user's global config routes shell commands through `rtk` (a token-optimizing proxy) via a Claude Code hook — e.g. `find`/`grep` become `rtk find`/`rtk grep`. This is transparent; no action needed.
