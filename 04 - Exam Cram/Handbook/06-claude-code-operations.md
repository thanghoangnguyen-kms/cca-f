---
tags:
  - CCA-F
  - handbook
  - domain-3
date: 2026-08-24
status: done
domain: "3 of 5"
---

# 06 — Claude Code Operations

Covers: CLAUDE.md hierarchy, settings.json, CI/CD non-interactive mode, plan
mode, `--bare`, skills, and default tool selection. (Concept chapters 6, 7, 8, 9
+ reference tables.)

---

## Core concepts (memorize)

### CLAUDE.md hierarchy & project rules
- **CLAUDE.md** = project-level configuration/rules, **committed to version
  control**. Holds: architecture rules, naming conventions, forbidden patterns,
  testing expectations.
- Example rules: DB queries go through the repository layer; API responses use a
  standard envelope; error codes follow a taxonomy; don't call
  `client.messages.create` directly in business logic; don't return `None` from
  a tool; don't use an iteration cap as the main loop terminator.
- **`.claude/settings.json`** ≠ CLAUDE.md: MCP servers are **defined** in
  `.mcp.json` (project) or user config; `settings.json` only **enables/gates**
  them (e.g. `enabledMcpjsonServers`), plus **env vars, commands/args** — the
  runtime wiring, not the coding rules.

### CI/CD — non-interactive mode
- In non-interactive environments (e.g. GitHub Actions), Claude Code **hangs** if
  it waits for user input. Use **`claude -p` / `--print`** (non-interactive).
- Add **`--output-format json`** for easy parsing in a pipeline.
- **Forgetting `-p` is the classic CI failure.**

### Plan mode vs direct execution
- **Plan mode** makes Claude describe changes **before** editing code. Use when:
  the codebase is unfamiliar, the change is high-risk, you need human review
  first, or you need to align on architecture first.
- Not always needed — small, clear tasks can go **direct execution**.

### `--bare` (minimal mode)
- Starts Claude Code "bare," skipping most automatic features so **you fully
  control context and environment.** `--bare` **SKIPS**:
  - **Hooks** (PreToolUse/PostToolUse, etc.)
  - **LSP** (Language Server Protocol)
  - **Plugin sync**
  - **Attribution** (no "Co-Authored-By / Generated with Claude" in commits)
  - **Auto-memory** (no automatic MEMORY.md read/write)
  - **Background prefetches**
  - **Keychain reads**
  - **CLAUDE.md auto-discovery** (project + user files not auto-loaded)

> Full flag reference — the docs' wording of `--bare`, `--bare` vs
> `--safe-mode`, and the fabricated flags: [`09-cli-flags.md`](09-cli-flags.md).

### Skills frontmatter
| Field | Meaning |
|---|---|
| `name` | Display label in skill listings; the `/command` comes from the **directory name** (plugin skills excepted) |
| `description` | What the skill does — helps Claude know **when** to use it |
| `context` | Only valid value is `fork` (isolated subagent context); omit the field to run in the main conversation |
| `allowed-tools` | **Pre-approves** the listed tools for the invoking turn — no permission prompt. Does **NOT** restrict: every other tool stays callable under normal permission settings. Grant clears on your next message |
| `disallowed-tools` | The field that actually **restricts** — removes tools from Claude's pool while the skill is active |
| `argument-hint` | Suggested input when invoking the skill |

> `allowed-tools` is an **allow-list, not a whitelist-only sandbox.** Classic
> distractor: "restrict a skill to Read and Grep" is `disallowed-tools` (or deny
> rules), never `allowed-tools`.
> Source: <https://code.claude.com/docs/en/skills> (checked 2026-08-24)

Context modes: omit `context` = run in the main conversation context;
**`context: fork`** = run in a separate context / sub-agent. (`inherit` is
**not** a `context` value — it's a value of the separate `model` field.)

### Default tool selection (Claude Code built-ins)
| Situation | Tool |
|---|---|
| Edit a small section of an existing file | **Edit** |
| Large 1000+ line file, change a few sections | **Edit** |
| Create a brand-new file | **Write** |
| Deliberately overwrite an entire file | **Write** |
| Read a file before editing | **Read** |
| Find text/pattern across many files | **Grep** |
| Find files by name | **Glob** |
| Run a command/test/build/lint | **Bash** |
| Append code to end of file | Edit or Write (avoid Bash if not needed) |
| Edit via `sed -i` | Usually **avoid** — prefer Edit |
| Same exact string, replace all occurrences | Edit with **`replace_all: true`** |

---

## Decision rules

| Trigger | Answer |
|---|---|
| Claude Code hangs in GitHub Actions | Use `claude -p --output-format json` |
| Where do architecture/naming/forbidden rules live? | **CLAUDE.md** (committed) |
| Where are MCP servers defined vs. enabled? | **Defined** in `.mcp.json` (project)/user config; **enabled/gated** via `.claude/settings.json` (`enabledMcpjsonServers`) |
| Unfamiliar codebase / high-risk change | **Plan mode** first |
| Need full manual control, no hooks/memory/auto-CLAUDE.md | **`--bare`** |
| Skill should run isolated from main context | Frontmatter `context: fork` |
| Replace every occurrence of a string in one file | `replace_all: true` |

---

## Anti-patterns

- Running Claude Code in CI **without `-p`** (it waits and hangs).
- Putting **MCP/env config in CLAUDE.md** or **coding rules in settings.json**.
- Using **plan mode for every trivial task** (overhead) — or skipping it on a
  risky/unfamiliar change.
- Using `sed -i` when **Edit** is cleaner and safer.
- Assuming `--bare` still runs hooks/loads CLAUDE.md (it doesn't).

---

## Key phrases to recognize

"CLAUDE.md (committed) vs settings.json (MCP enable/gate, env)" · "`-p` /
`--print` / `--output-format json`" · "plan mode for unfamiliar/high-risk" ·
"`--bare` skips hooks/LSP/memory/CLAUDE.md" · "skill `context: fork` vs
omitted (main conversation)" ·
"Edit vs Write vs Grep vs Glob."

## Sources
- Best practices for Claude Code — https://code.claude.com/docs/en/best-practices
- Claude Code CLI reference — https://code.claude.com/docs/en/cli-reference
