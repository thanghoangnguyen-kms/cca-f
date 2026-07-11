# Copilot Instructions

This is an **Obsidian vault** named `CCA-F`, used for exam preparation for the **Claude Certified Architect – Foundations (CCA-F)** certification by Anthropic/Certiport.

> **`AGENTS.md` in the vault root is the authoritative spec.** Read it before writing or editing notes — this file is the high-level map; AGENTS.md has the details.

## What This Is

An Obsidian vault of study notes. It is a **content repository — Markdown notes only**. There is no build, test, lint, or run step; "working in this repo" means writing and editing `.md` notes.

## Vault Structure

Content is organized into numbered folders by study order. **`00 - START HERE.md` (vault root) is the entry point / map of content.**

```
CCA-F/
├── 00 - START HERE.md              ← entry point (MOC); check this first
├── 01 - Roadmap/
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
│   ├── Flashcards.md
│   ├── Critical Terms Glossary.md
│   └── Handbook/                    ← scenario-based cram (00–08, plain MD links)
├── 05 - Practice/
│   └── Weak Areas Deep Dive.md       ← personal mistake log
├── AGENTS.md · CLAUDE.md             ← agent instructions (vault root)
└── .github/copilot-instructions.md
```

Domain notes are named `D<N> - <Topic>.md`; course notes `C<N> - <Course>.md`.

**Two link conventions coexist — match the file you're editing:**
- All notes except the handbook use **Obsidian-flavored Markdown**: `[[wikilinks]]`, `> [!TYPE]` callouts, YAML frontmatter (resolved by filename, so notes move between folders freely).
- The `04 - Exam Cram/Handbook/` files use **standard `[link](file.md)` relative links** — keep that folder intact as a unit so its links don't break.

## Obsidian Markdown Conventions

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

## Note Frontmatter Template

```yaml
---
tags:
  - CCA-F
  - <domain-tag>        # e.g. domain-1, domain-2, mcp, prompt-engineering
date: YYYY-MM-DD
status: not-started     # not-started | in-progress | done | needs-review
domain: "N of 5"        # e.g. "1 of 5"
---
```

## Content Rules

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

### When writing flashcard notes

- Format: `**Q: <question>**` followed by `A: <answer>` on the next line
- Group by domain with `## Domain N` headers
- Keep answers to 1–3 sentences max

### When referencing exam-specific terms

Always use **code formatting** for:
- CLI flags: `--resume`, `--print`, `--output-format json`
- API fields: `stop_reason`, `tool_choice`, `custom_id`, `isError`
- Hook names: `PreToolUse`, `PostToolUse`, `SubagentStart`
- Config files: `CLAUDE.md`, `.mcp.json`, `~/.claude.json`
- Commands: `/memory`, `/compact`
- SDK objects: `AgentDefinition`, `ResultMessage`, `ClaudeSDKClient`

## Content Focus

This vault maps to 5 official CCA-F exam domains:
1. Agentic Architecture & Orchestration
2. Tool Design & MCP Integration
3. Claude Code Configuration & Workflows
4. Prompt Engineering & Structured Output
5. Context Management & Reliability

When helping with content:
- Prioritize exam-accuracy over breadth — verify against official sources
- Use Markdown headings (`#`, `##`, `###`) to structure notes
- Use ❌ / ✅ for anti-pattern vs correct-pattern comparisons

## Research & Content Sources

| Source | Use For |
|--------|---------|
| https://code.claude.com/docs/en/overview | Claude Code — all domains |
| https://code.claude.com/docs/llms.txt | Full doc index — find specific pages |
| https://docs.anthropic.com | Claude API, models, features |
| https://anthropic.skilljar.com | Official Anthropic courses |
| https://www.certsafari.com/anthropic/claude-certified-architect | Exam domain breakdown + 614 practice questions |
| https://github.com/FlorianBruniaux/claude-code-ultimate-guide | Deep architecture, security, workflows |
| https://github.com/anthropics/claude-cookbooks | Hands-on code examples |

**Always verify facts against official docs** before writing exam-critical content.

## Constraints

- **Do not** create planning or tracking files (todos, project notes, etc.) inside the vault — this is a study content vault only
- **Do not** create files outside the vault root (`/Users/thanghoangnguyen/Documents/Obsidian Vault/CCA-F/`)
- **Do not** invent exam content. If uncertain, note it as `> [!WARNING] Unverified — confirm against official study guide`
- **Keep notes exam-focused** — avoid tangential deep dives that don't map to the 5 official domains

## Domain → Subdomain Map (Quick Reference)

| Domain | File | Subdomains |
|--------|------|-----------|
| D1: Agentic Architecture | `D1 - Agentic Architecture & Orchestration.md` | 1.1 Agent loop · 1.2 Multi-agent · 1.3 Subagent spawning · 1.4 Workflows & handoffs · 1.5 Hooks · 1.6 Task decomposition · 1.7 Session management |
| D2: Tool Design & MCP | `D2 - Tool Design & MCP Integration.md` | 2.1 Tool interfaces · 2.2 Error responses · 2.3 Tool distribution · 2.4 MCP servers · 2.5 Built-in tools |
| D3: Claude Code Config | `D3 - Claude Code Configuration & Workflows.md` | 3.1 CLAUDE.md hierarchy · 3.2 Commands & skills · 3.3 Path-specific rules · 3.4 Plan mode · 3.5 Iterative refinement · 3.6 CI/CD |
| D4: Prompt Engineering | `D4 - Prompt Engineering & Structured Output.md` | 4.1 Explicit criteria · 4.2 Few-shot · 4.3 JSON schemas · 4.4 Validation & retry · 4.5 Batch processing · 4.6 Multi-instance review |
| D5: Context & Reliability | `D5 - Context Management & Reliability.md` | 5.1 Long context · 5.2 Escalation · 5.3 Error propagation · 5.4 Codebase exploration · 5.5 Human review · 5.6 Provenance |
