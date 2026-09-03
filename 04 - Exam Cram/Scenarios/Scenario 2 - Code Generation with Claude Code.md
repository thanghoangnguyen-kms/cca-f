---
tags:
  - CCA-F
  - scenarios
date: 2026-09-03
status: done
---

# Scenario 2 — Code Generation with Claude Code

> [!NOTE] What the blueprint states
> A team uses Claude Code for **generation, refactoring, debugging, and docs**.
> Custom slash commands, `CLAUDE.md` configuration, **plan mode vs direct
> execution**. Primary domains **D3, D5**. Official sample questions Q4–Q6 ·
> **no third-party bank tests this scenario**.

> [!WARNING] Thin drill volume, high leakage
> Scenarios 2 and 5 are the two frames no CyberSkill sitting reaches. Passer
> score reports repeatedly flag **D3 as the weakest domain even for daily Claude
> Code users** — fluency with the parts you reach for, blind spots around the
> rest. Treat this note as primary material, not revision.

---

## Requirements → what actually matters

| The stem says | What it actually constrains |
|---|---|
| "**the team**" / "every developer" / "shared" | Project scope, committed to git. User-level config is invisible to teammates |
| "**different conventions** in different parts of the codebase" | Path-conditional loading, not one bigger instruction file |
| "a **new team member** isn't getting…" | Almost always a **scope** error, not a content error |
| "**repeatable** / every time we…" | A slash command or a skill, not a pasted prompt |
| "**multi-file refactor** / architectural change" | Plan mode — approve the approach before edits land |
| "**small, well-defined** fix" | Direct execution. Plan mode here is overhead |
| "the session has gone on a long time / answers are drifting" | A D5 context question wearing a D3 costume |

> [!IMPORTANT] The layer question decides most items here
> Nearly every distractor in this scenario is a **correct mechanism installed at
> the wrong layer** (`DF3`). Before choosing, name the layer that owns the
> concern: prompt · `CLAUDE.md` · `.claude/rules/` · `.claude/commands/` ·
> `.claude/skills/` · `.mcp.json` · `settings.json` · hook.

---

## Requirement → mechanism → layer

| Requirement | Mechanism | Why this layer |
|---|---|---|
| Conventions every teammate gets | **Project-root `CLAUDE.md`**, committed | User-level `~/.claude/CLAUDE.md` is personal and never version-controlled |
| Personal preferences that shouldn't be imposed | **User-level `~/.claude/CLAUDE.md`** | Deliberately outside the repo |
| Conventions that apply only to some paths | **`.claude/rules/` with `paths:` YAML frontmatter** (`src/api/**/*`, `**/*.test.*`) | Loads conditionally. Stuffing them into one `CLAUDE.md` burns context on every request and applies rules where they are wrong |
| A repeatable multi-step workflow | **`.claude/commands/`** slash command — project scope to share, user scope to keep private | Version-controlled, reviewable, invocable by name |
| A packaged capability with its own context | **`.claude/skills/` with `SKILL.md` frontmatter** — `context: fork`, `allowed-tools`, `argument-hint` | Isolates its own context so it doesn't pollute the main session |
| MCP servers for the team | **`.mcp.json` at the project root, `--scope project`, committed** | Not `CLAUDE.md`. The three scopes match duplicates by **name**, and the entire winning entry is used — fields never merge across scopes |
| Approach agreed before code changes | **Plan mode** | Review the plan, not the diff |
| A small bounded change | **Direct execution** | Plan mode adds a round trip for no decision |
| Quality improves over iterations | **Iterative refinement** — critique against explicit criteria, then revise | Criteria make the second pass sharper than "try again" |
| Long session degrading | **`/compact`, scratchpad files, or a subagent with a summary** | A `PreCompact` hook can shape what survives |

Task statements in play: **3.1, 3.2, 3.3, 3.4, 3.5, 5.1**.

---

## Trade-offs — what each mechanism costs

- **Everything in one `CLAUDE.md`** is simple and spends context on every request, including the rules irrelevant to the file being edited. Path rules cost a little indirection and buy that back.
- **Plan mode** costs a round trip and buys a cheap place to catch a wrong approach — before it is spread across nine files.
- **Skills with `context: fork`** buy isolation and cost the ability to see the main conversation, so anything they need must be passed in.
- **Project scope** buys team consistency and costs individual latitude; that is usually the intent, but read whether the stem wants sharing or privacy.
- **`/compact`** buys headroom and costs whatever the summary drops — which is why the `PreCompact` hook exists.

---

## Attractive but wrong

| Option | Family | Why it fails here |
|---|---|---|
| Put the team's conventions in `~/.claude/CLAUDE.md` | `DF3` | Right file, wrong scope — not in version control, so teammates never see it |
| Put MCP server config or env vars in `CLAUDE.md` | `DF3` | `CLAUDE.md` carries conventions; `.mcp.json` carries servers; `settings.json` carries env |
| Put coding conventions in `settings.json` | `DF3` | The same swap in the other direction |
| Add all conventions to one big `CLAUDE.md` instead of path rules | `DF5` | Complexity that doesn't pay: context burned on every request, rules applied where wrong |
| Tell the developer to paste the workflow prompt each time | `DF2` | A repeatable process belongs in a command, not in discipline |
| Use plan mode for a one-line bug fix | `DF5` | Ceremony with no decision to review |
| Use direct execution for a cross-cutting refactor | `DF7` | You find out at the diff, after the damage |
| Fix drifting answers with a bigger context window | `DF4` | Solves saturation; the problem is degradation. Compaction, scratchpad, or a subagent |
| Assume `--bare` still runs hooks, loads `CLAUDE.md`, or writes memory | `DF7` | It does not — naming the flag isn't knowing its contract |
| Use `sed -i` where `Edit` is the right tool | `DF3` | Wrong layer, and it bypasses everything Claude Code knows about the file |
| Bulk-read the whole codebase before starting | `DF5` | Grep entry points, follow imports just-in-time |
| Re-read every file after resuming when three changed | `DF7` | Resume and communicate the **delta** |

---

## Drill this scenario

- Official items **Q4–Q6** in `05 - Practice/Exam Guide - Sample Questions/`
- `05 - Practice/Vault-authored - Claude Code Scenario Drills/` Q1–Q10 — vault-authored, exam-accurate topics, uncalibrated difficulty
- Connectry MCP task statements **3.1–3.5**
- Handbook: [[06-claude-code-operations]] · [[09-cli-flags]] · [[03-context-and-sessions]]

**Back to:** [[Scenario Index]] · [[D3 - Claude Code Configuration & Workflows]] · [[D5 - Context Management & Reliability]]
