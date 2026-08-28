# CCA-F — Claude Certified Architect (Foundations) Study Vault

An [Obsidian](https://obsidian.md) vault of study notes for the **Claude Certified Architect – Foundations (CCA-F)** certification — exam code **CCAR-F**, an Anthropic credential delivered by **Pearson VUE**. It is a content repository — Markdown notes only, no build or run step.

> **Entry point:** open [`00 - START HERE.md`](00%20-%20START%20HERE.md) — the map of content (MOC) for the whole vault.

## What the exam covers

CCA-F certifies solution architects who design and implement production applications with Claude. The vault is organized around the five official exam domains:

| # | Domain |
|---|--------|
| 1 | Agentic Architecture & Orchestration |
| 2 | Tool Design & MCP Integration |
| 3 | Claude Code Configuration & Workflows |
| 4 | Prompt Engineering & Structured Output |
| 5 | Context Management & Reliability |

## Structure

Content lives in numbered folders by study order:

| Folder | Contents |
|--------|----------|
| `01 - Roadmap/` | `Official Exam Blueprint.md` — the authoritative exam spec (weights, 30 task statements, 6 scenarios, in/out of scope) · `CCA-F Study Roadmap.md` — the 6-week plan + progress tracker |
| `02 - Courses/` | `C1`–`C6` — one lesson-doc per required training course |
| `03 - Domains/` | `00 - Model Family & API Fundamentals` + `D1`–`D5` per-domain deep dives |
| `04 - Exam Cram/` | `Flashcards.md`, `Critical Terms Glossary.md`, and `Handbook/` (scenario-based cram, `00`–`08`) |
| `05 - Practice/` | `Weak Areas Deep Dive.md` (mistake log) + **six** exam sets, a folder each: `Exam Guide - Sample Questions/` (**official** — 12 Qs with Anthropic's own rationales), `Vault-authored - Claude Code Scenario Drills/` (20 Qs for official scenarios 2 & 5, written for this vault), three CyberSkill sittings (`New Mock Exam/` questions + matched key, `Mock Exam/` key only, `Timed Mock 2026-08-24/` stems + grader-authoritative key), and `CCA-F Question Bank/` (questions only) |
| `06 - Youtube Course/` | Per-episode notes for the Peace Of Code course — `EP<NN> - <Title>/` folders, each with a study guide + its own flashcard deck |
| `youtube-transcript/` | **Read-only source material** — third-party auto-generated transcripts. Never edited or linked to from study notes |

Agent/authoring conventions live in [`AGENTS.md`](AGENTS.md) and [`CLAUDE.md`](CLAUDE.md). Agent tooling — slash commands and skills — lives in `.claude/` (version-controlled) with parallel copies for non-Claude agents in `.agents/skills/`; `skills-lock.json` pins the external `baoyu-youtube-transcript` skill.

## Conventions

- **Two link styles coexist — match the file you're editing.** All notes use Obsidian-flavored Markdown with `[[wikilinks]]`, `> [!TYPE]` callouts, YAML frontmatter, and tags — **except** `04 - Exam Cram/Handbook/`, which uses plain GitHub Markdown with standard `[link](file.md)` relative links (keep that folder intact as a unit).
- Exam terms are code-formatted: CLI flags (`--resume`), API fields (`stop_reason`), hook names (`PreToolUse`), config files (`CLAUDE.md`, `.mcp.json`), SDK objects (`AgentDefinition`).
- Model/API facts are verified against official sources: [code.claude.com/docs](https://code.claude.com/docs), [platform.claude.com/docs](https://platform.claude.com/docs/en/home), and the Anthropic engineering blog. Anything unverified is flagged inline with a `> [!WARNING] Unverified` callout.

## How to use it

1. Open the folder in Obsidian (wikilinks, callouts, and Mermaid diagrams render natively).
2. Start at [`00 - START HERE.md`](00%20-%20START%20HERE.md), then follow the [study roadmap](01%20-%20Roadmap/CCA-F%20Study%20Roadmap.md).
3. Use `04 - Exam Cram/` for last-mile review and `05 - Practice/` to self-test.
4. Working with an AI agent (Claude Code, Copilot CLI)? Start the session from inside `CCA-F/` so it discovers `.claude/commands/` and `.claude/skills/`, then run `/cca-episode <transcript-path>` to generate a new episode study guide + deck from a `youtube-transcript/` source.

---

*Personal study material. Not affiliated with or endorsed by Anthropic.*
