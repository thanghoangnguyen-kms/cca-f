---
tags:
  - CCA-F
  - scenarios
date: 2026-09-03
status: done
---

# Scenario 4 — Developer Productivity with Claude

> [!NOTE] What the blueprint states
> Agent SDK agent for **exploring unfamiliar codebases, legacy systems, and
> boilerplate**. Built-in tools (`Read`, `Write`, `Bash`, `Grep`, `Glob`) plus MCP
> servers. Primary domains **D2, D3, D1**. **No official sample question** ·
> `code_exploration` in all three CyberSkill sittings.

> [!TIP] Well-drilled, but only by third parties
> This is the scenario with the most bank coverage and **zero official items**, so
> your calibration here comes entirely from CyberSkill. Where its key disagrees
> with official docs, docs win.

---

## Requirements → what actually matters

| The stem says | What it actually constrains |
|---|---|
| "**unfamiliar** codebase" / "legacy" | You cannot plan the exploration up front. Adapt as you learn |
| "the codebase is **large**" | Context strategy is the question, not tool choice |
| "find **where X is implemented**" | Content search → `Grep`. Filename pattern → `Glob` |
| "resume **the** session from yesterday" | `--resume <name/id>`. `--continue` only reaches the most recent |
| "try **two approaches** from the same starting point" | `fork_session` |
| "files **changed** since the session" | Resume **and state the delta** |
| "the edit **corrupted** the file" | An `Edit`-vs-`Write` uniqueness question |

> [!IMPORTANT] Exploration order is the recurring item
> **Grep the entry points, then follow imports just-in-time.** Every wrong answer
> in this family substitutes breadth for direction: reading everything, choosing
> files by size, or keyword-dragnetting the repo.

---

## Requirement → mechanism → layer

| Requirement | Mechanism | Why this layer |
|---|---|---|
| Locate code by content | **`Grep`** | Purpose-built; `Bash grep` bypasses what Claude Code tracks |
| Locate files by name or path shape | **`Glob`** | Pattern matching on paths, not contents |
| Understand a specific file | **`Read`** just-in-time | Reading ahead of need spends context on unknowns |
| Modify existing code | **`Edit`** with a unique `old_string` | Surgical, reviewable |
| When uniqueness cannot be met | **Read → edit in memory → `Write`** | Deliberate replacement instead of a `replace_all` that corrupts |
| Explore without flooding the session | **Subagent with a summary** | Verbose intermediate tool calls stay *inside* the subagent; only the compact result returns |
| Unknown error, no clear plan | **Adaptive decomposition** — investigate, then decide the next step | A full plan built on unknowns is fiction |
| Return to a specific past session | **`--resume <name/id>`** | `--continue` resumes the most recent only |
| Two mutually exclusive approaches | **`fork_session`**, one branch each | Shared baseline, divergent history |
| Resume when files moved on | Resume **+ tell it exactly which files changed** | Resuming silently reasons over stale reads |
| Team-wide MCP servers | **`.mcp.json` at project root, `--scope project`, committed** | Local scope is the default and is invisible to teammates |
| A repeatable exploration workflow | **`.claude/commands/`** slash command | Turns a good ad-hoc prompt into a shared asset |

Task statements in play: **1.7, 2.4, 2.5, 3.2, 5.4**.

---

## Trade-offs — what each mechanism costs

- **Just-in-time reading** keeps context clean and costs round trips. That is the right trade in a large repo and the wrong one in a three-file project.
- **Subagent exploration** buys context isolation and costs you the detail that didn't make the summary — so specify what the summary must contain.
- **`fork_session`** buys two clean branches and costs the ability to compare them in one place; you reconcile at the end.
- **Resume + delta** is cheaper than a fresh start and only as good as the delta you state. An incomplete delta is worse than starting clean.
- **`Edit`** is precise and brittle on non-unique strings; **`Write`** is safe and destroys anything you didn't carry forward.

---

## Attractive but wrong

| Option | Family | Why it fails here |
|---|---|---|
| Read the entire codebase into context first | `DF5` | Complexity that doesn't pay; saturates context before any question is answered |
| Pick files to read by size | `DF1` | File size is a proxy for nothing |
| Keyword-dragnet the whole repo | `DF1` | Recall without direction; buries the signal |
| Build a complete plan before investigating an unknown error | `DF5` | Plans an investigation you have not done |
| `--continue` to reach a session from two days ago | `DF7` | Names the wrong mechanism; `--continue` is most-recent-only |
| `fork_session` to refresh stale file reads | `DF7` | Forking copies the stale context. Resume + delta, or a fresh session with a summary |
| Resume silently and hope the files still match | `DF7` | Reasons over state that has moved |
| Re-read every file when three changed | `DF7` | Communicate the delta instead |
| `replace_all` to apply a rename across a file | `DF7` | Corrupts every incidental match |
| A giant brittle `old_string` to force uniqueness | `DF7` | Fails on any whitespace drift; use Read → memory → `Write` |
| `sed -i` because it is faster | `DF3` | Wrong layer — bypasses the edit path entirely |
| Put the MCP server in user scope so "it just works" | `DF3` | Right mechanism, wrong scope; teammates get nothing |
| Bigger context window to handle the large repo | `DF4` | Solves saturation; the problem is directed retrieval |

---

## Drill this scenario

- [[Answer Patterns Index]] § Part 3 drill sets **4 (Exploration order)**, **6 (Context isolation)**, **7 (Sessions)**
- Unified Bank `code_exploration` key — the deepest coverage in the vault
- `05 - Practice/CCA-F Question Bank/` — its `Glob`/`Edit` fallback and `--resume` items
- Handbook: [[03-context-and-sessions]] · [[09-cli-flags]] · [[08-quick-reference-tables]]

**Back to:** [[Scenario Index]] · [[D1 - Agentic Architecture & Orchestration]] · [[D2 - Tool Design & MCP Integration]] · [[D3 - Claude Code Configuration & Workflows]]
