---
tags:
  - CCA-F
  - handbook
  - domain-3
date: 2026-08-28
status: done
domain: "3 of 5"
---

# 09 — CLI Flags (real, rumored, fabricated)

Covers: every `claude` CLI flag worth exam time, sorted into three tiers —
blueprint-named, docs-real, and fabricated. Companion to
[`06`](06-claude-code-operations.md) (CI/CD, `--bare`) and
[`03`](03-context-and-sessions.md) (sessions).

Verified 2026-08-28 against the official CLI reference **and** `claude --help`
on v2.1.238. Where the two disagree, both readings are shown — **the docs are
the exam answer.**

---

## Core concepts (memorize)

### Tier 1 — Blueprint-named (know these cold)

The exam guide's appendix names **three** flags under "Claude Code CLI", plus
two session items under "Claude Code". Everything else on this page is
recognition-only.

| Flag | Does | Exam trigger |
|---|---|---|
| **`-p` / `--print`** | Run non-interactively: print the response and exit | "Claude Code hangs in CI/GitHub Actions." **The single most-tested flag.** |
| **`--output-format json`** | Machine-parsable result. Values: `text` (default), `json`, `stream-json`. **Print mode only** | "The pipeline needs to parse the result" / gate a merge on the output |
| **`--json-schema <schema>`** | Output validated against a JSON Schema. **Print mode only**; pair with `--output-format json` — the result lands in `structured_output` | "Guarantee the shape of the output" — structural guarantee in *code*, not a prompt |
| **`--resume [id\|name]`** | Resume a specific past session | "Continue the review from yesterday" |
| **`fork_session`** | Branch a new session from a shared baseline; original preserved | "Explore alternatives without polluting the original" |

> **`fork_session` is the blueprint's spelling** (the SDK option). The **CLI
> flag is `--fork-session`**, used together with `--resume` or `--continue`.
> Same feature, two spellings — recognize both.

### Tier 2 — Real but not blueprint-named (recognize, don't memorize)

Grouped by the job they do. All documented in the official CLI reference.

| Job | Flags |
|---|---|
| **Sessions** | `--continue` / `-c` (most recent in this dir) · `--fork-session` · `--session-id <uuid>` · `--name` / `-n` (display name) |
| **CI safety rails** | `--max-turns` (caps agentic turns, print mode only; **exits with an error at the limit** — the runaway-loop guard) · `--max-budget-usd` (spend cap, print mode only) · `--fallback-model` (print mode only) |
| **Permissions** | `--tools` (**which tools exist at all**) · `--allowedTools` (pre-approve, no prompt) · `--disallowedTools` (deny specific tools) · `--permission-mode` (`default`, `acceptEdits`, `plan`, `auto`, `dontAsk`, `bypassPermissions`, `manual`) · `--permission-prompt-tool` (an MCP tool answers prompts non-interactively) · `--dangerously-skip-permissions` |
| **Context control** | `--add-dir` (extra readable/editable dirs) · `--system-prompt` (replace) · `--append-system-prompt` (add to default) · `--settings` (settings file or inline JSON) |
| **MCP** | `--mcp-config` (load servers from JSON files/strings) · `--strict-mcp-config` (use **only** those, ignore all other MCP config) |
| **Model / agents** | `--model` (alias or full name) · `--agents <json>` (define subagents inline) |
| **Startup mode** | `--bare` (minimal, for scripting) · `--safe-mode` (disable customizations, for troubleshooting) |

**Three flags control tools, and they are not interchangeable:**

| Question | Flag |
|---|---|
| Which tools **exist** in this session? | `--tools` (`default` = all; `none` = disable all; or explicit tool names) |
| Which tools run **without a prompt**? | `--allowedTools` — pre-approves only; removes nothing |
| Which tools are **denied**? | `--disallowedTools` — removes matching tools from Claude's context |

> **`--help` is not exhaustive.** The CLI reference says so directly: *"a flag's
> absence from `--help` does not mean it is unavailable."* `--max-turns` is the
> live example — documented, but missing from `claude --help` on v2.1.238.
> Two more docs-vs-`--help` splits: `--tools none` (docs) vs `--tools ""`
> (`--help`), and `--permission-mode default` (docs) — `--help` shows only its
> alias `manual`. **Both values work; the docs wording is the exam answer.**

### `--bare` (minimal mode) — the flag candidates report seeing

Starts Claude Code with auto-discovery off so **you fully control context and
environment**. Two published descriptions, both current:

| Source | `--bare` skips |
|---|---|
| **CLI reference** (exam answer) | Auto-discovery of **hooks, skills, custom commands, subagents, plugins, MCP servers, auto-memory, and `CLAUDE.md`** — so scripted calls start faster |
| **`claude --help`** (v2.1.238) | Hooks, **LSP**, plugin sync, **attribution**, auto-memory, background prefetches, **keychain reads**, `CLAUDE.md` auto-discovery |

**The overlap is what gets tested: hooks · auto-memory · `CLAUDE.md`
auto-discovery.** All three stop happening in both readings. (On plugins the
two differ: the docs say *plugins*, `--help` says *plugin sync*.)

**Skills are the nuance.** "Does `--bare` disable skills?" — not entirely.
`--help` says skills still resolve via `/skill-name`; the docs say skills in a
directory passed with `--add-dir` still load. Custom **commands** and
**subagents** are skipped.

What `--bare` does **not** take away: Claude still has Bash, file read, and file
edit tools. Sets `CLAUDE_CODE_SIMPLE`. But it **does** take away subscription
auth — `--bare` needs `ANTHROPIC_API_KEY` or an `apiKeyHelper` passed via
`--settings`; OAuth and the keychain are never read. (Bedrock/Vertex/Foundry use
their own credentials.) You put context back explicitly with
`--system-prompt` / `--append-system-prompt`, `--add-dir`, `--mcp-config`,
`--settings`, `--agents`, `--plugin-dir`.

### `--bare` vs `--safe-mode` — different problems

| | `--bare` | `--safe-mode` |
|---|---|---|
| **For** | Scripted / programmatic runs you want fast and predictable | Troubleshooting a broken configuration |
| **Turns off** | Auto-discovery (hooks, plugins, memory, `CLAUDE.md`, MCP) | **All** customizations — `CLAUDE.md`, skills, plugins, hooks, MCP, commands, agents, themes, keybindings |
| **Still applies** | Anything you pass explicitly by flag | Admin/policy settings, auth, model choice, built-in tools, permissions |
| **Sets** | `CLAUDE_CODE_SIMPLE` | `CLAUDE_CODE_SAFE_MODE` |

### Tier 3 — Fabricated (instantly wrong)

| Fake flag | Why it's tempting | Reality |
|---|---|---|
| `--headless` | "Headless" is **legitimate vocabulary** for running Claude Code programmatically — the docs use the word in prose | No such flag |
| `--non-interactive` | Reads like the obvious name for the feature — the most tempting of the four | No such flag |
| `--ci` | Borrowed from other CLIs | No such flag |
| `--batch` | Confuses the CLI with the **Message Batches API** (a different thing entirely) | No such flag |

**`-p` / `--print` is the only *flag* that turns on non-interactive mode** — a
non-TTY stdout (piped or redirected output) triggers it too. An invalid flag is
reported to stderr and the run never starts.

---

## Decision rules

| Trigger | Answer |
|---|---|
| Claude Code hangs in GitHub Actions | `claude -p` (missing `-p` is the classic CI failure) |
| Pipeline must parse the result | `--output-format json` |
| Output shape must be **guaranteed** | `--json-schema` — code enforces, prompts only persuade |
| Agent loops until the job's wall-clock timeout | `--max-turns` — `-p` bounds *waiting*, not *turns* |
| CI job must not run away on spend | `--max-budget-usd` |
| Subagent spawning in CI | `--allowedTools` must include **`Task`** — the exam answer; the SDK renamed the tool `Agent` in v2.1.63, keeping `Task` as an alias |
| Restrict **which tools exist** in the session | `--tools` — **not** `--allowedTools` |
| Deny **specific** tools or commands | `--disallowedTools` (or deny rules) |
| Fast, predictable scripted run, no auto-context | `--bare` |
| "My config is broken, what's causing it?" | `--safe-mode` |
| Only the MCP servers I name, nothing else | `--mcp-config` + `--strict-mcp-config` |
| Branch alternatives from one baseline | `--fork-session` (with `--resume`/`--continue`) |
| Option offers `--headless` / `--ci` / `--batch` / `--non-interactive` | Eliminate it — none exist |

---

## Anti-patterns

- Running Claude Code in CI **without `-p`** — it waits for input and hangs.
- Picking `--headless` or `--non-interactive` because the *concept* is right.
  The vocabulary is fine; the **flag name** is fabricated.
- Assuming `-p` bounds the run. It removes the wait for a human; a confused
  agent can still loop to the job timeout. That's `--max-turns`.
- Using `--allowedTools` to **restrict**. On its own it only stops the prompt —
  it removes nothing. Scoping what exists is `--tools`; denying is
  `--disallowedTools`.
- Assuming `--bare` still runs hooks or loads `CLAUDE.md` — it does neither.
- Reaching for `--dangerously-skip-permissions` in CI when the scoped answer is
  `--permission-mode dontAsk` **plus** `--allowedTools`. Neither half works
  alone: `dontAsk` auto-denies everything unmatched, `--allowedTools` supplies
  the matches.
- Treating `--batch` as the Message Batches API. Batches are an **API**
  feature, not a CLI flag.

---

## Key phrases to recognize

"`-p` / `--print` is the only non-interactive form" · "`--output-format json`
for pipelines" · "`--json-schema` guarantees shape" · "`--max-turns` caps
runaway loops" · "`--tools` scopes, `--allowedTools` pre-approves,
`--disallowedTools` denies" · "`--bare` skips hooks/plugins/auto-memory/`CLAUDE.md`" ·
"`--safe-mode` is for a broken config, `--bare` is for scripts" ·
"`--headless`/`--ci`/`--batch`/`--non-interactive` do not exist."

## Sources
- Claude Code CLI reference — https://code.claude.com/docs/en/cli-reference (checked 2026-08-28)
- Claude Code permission modes — https://code.claude.com/docs/en/permission-modes (checked 2026-08-28)
- Claude Code headless mode — https://code.claude.com/docs/en/headless (checked 2026-08-28)
- `claude --help`, Claude Code v2.1.238 (checked 2026-08-28)
- CCA-F Exam Guide v1.0, appendix "Technologies and concepts named" — the Tier 1 list
