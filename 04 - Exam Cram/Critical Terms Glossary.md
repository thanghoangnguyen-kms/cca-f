---
tags:
  - CCA-F
  - exam-cram
  - glossary
date: 2026-08-24
status: done
---

# Critical Terms Glossary

> [!NOTE]
> Memorize-by-heart reference for every exam-critical term across all 5 domains. Grouped by domain; see [[CCA-F Study Roadmap]] for the study plan and the deep-dive notes ([[00 - Claude Model Family & API Fundamentals]], [[D1 - Agentic Architecture & Orchestration]], [[D2 - Tool Design & MCP Integration]], [[D3 - Claude Code Configuration & Workflows]], [[D4 - Prompt Engineering & Structured Output]], [[D5 - Context Management & Reliability]]) for full explanations and examples. Pair with [[Flashcards]] for active recall.

---

## 00 — Fundamentals (Models & API Core)

| Term | Domain | What it does / why it matters |
|---|---|---|
| **Messages API** (`POST /v1/messages`) | Fundamentals | The single endpoint for everything — text, tools, structured output, streaming. Not separate APIs per feature. |
| `model` | Fundamentals | Required request field. Always use the exact model ID string (e.g. `claude-opus-5`); never append date suffixes to aliases. |
| `max_tokens` | Fundamentals | Required request field — the output token cap for the response. |
| `messages` | Fundamentals | Required request field — ordered list of `{role, content}` turns. The API is **stateless**: you resend full history every turn. |
| `system` | Fundamentals | Optional field — system prompt, kept separate from the `messages` array. |
| `stop_reason` | Fundamentals | Field on the response indicating why generation stopped. Values: `end_turn` (done — terminate loop), `tool_use` (execute tool, append result, continue loop), `max_tokens` (hit output cap — handle truncation), `stop_sequence` (hit a configured stop string), `pause_turn` (server-tool loop paused mid-turn — resume the request), `refusal` (safety decline — check `stop_details`), `model_context_window_exceeded` (Claude 4.5+ only — context window exhausted, distinct from `max_tokens`'s output cap). |
| `tool_choice` | Fundamentals / D2 / D4 | Controls how Claude uses tools: `{"type":"auto"}` (default — model decides), `{"type":"any"}` (must call some tool), `{"type":"tool","name":...}` (force one specific tool), `{"type":"none"}` (no tool calls). |
| `output_config` | Fundamentals / D4 | Request field controlling both output format and thinking effort. `output_config.format` replaces the deprecated top-level `output_format`. `output_config.effort` (`low`\|`medium`\|`high`\|`xhigh`\|`max`) controls thinking depth (default `high`; `xhigh` best for coding/agentic work). |
| Adaptive thinking (`thinking: {type:"adaptive"}`) | Fundamentals | Current thinking mode. **Learn the rule, not the model list:** the old `thinking: {type:"enabled", budget_tokens:N}` is *deprecated* on the **4.6** models (requests still succeed) and **rejected with 400 on Claude 4.7 and later** — which includes Fable 5, Mythos 5, Opus 5, Opus 4.7/4.8, Sonnet 5. Haiku 4.5 still supports it. ([docs](https://platform.claude.com/docs/en/build-with-claude/thinking-troubleshooting), checked 2026-08-24) |
| Prefill removal | Fundamentals | Last-assistant-turn prefills return **400** on **Claude 4.6 and later** — Fable 5, Mythos 5, Opus 5, Opus 4.6-4.8, Sonnet 4.6/5. Use structured outputs or system-prompt instructions instead. ([docs](https://platform.claude.com/docs/en/about-claude/models/migration-guide), checked 2026-08-24) |
| `cache_control: {type:"ephemeral"}` | Fundamentals / D5 | Marks a content block as cacheable. Prefix match — any byte change earlier in the prefix invalidates everything after it. Default TTL 5 min (extended TTL: 1h, explicit). ~0.1x read cost, ~1.25x write cost. Verify with `usage.cache_read_input_tokens`. |
| `--print` / `-p` | Fundamentals / D3 | CLI flag for non-interactive mode; required in CI/automated pipelines — without it Claude Code waits for input and hangs. |
| `--output-format json` | Fundamentals / D3 | CLI flag for machine-parseable JSON output; combine with `--json-schema '<inline schema>'` to enforce structure in CI. The flag takes an **inline JSON Schema string, never a file path**; the validated object arrives in `structured_output`, not `result`. |
| `--resume <session-name>` | Fundamentals / D1 / D3 | Resume a named session from the CLI. `--continue` resumes the most recent session. |
| `/compact` | Fundamentals / D5 | Slash command that summarizes the conversation to reduce token usage while preserving key info. Skill descriptions are **not** re-injected after `/compact` — only skills actually invoked persist. |
| `/init` | Fundamentals / D3 | Slash command that generates a starter `CLAUDE.md` from the project structure. |
| Explore / Plan built-in subagents | Fundamentals / D1 | Skip `CLAUDE.md` and parent session git status to stay fast/cheap. Custom subagents **do** load `CLAUDE.md`. |

---

## D1 — Agentic Architecture & Orchestration

| Term | Domain | What it does / why it matters |
|---|---|---|
| Agent loop | D1 | Claude evaluates state → produces text (done) or tool calls (execute) → tool results fed back → repeat, until `stop_reason: "end_turn"`. |
| `max_turns` / `maxTurns` | D1 | Loop control — caps tool-use turns (counts only turns with tool calls, not the final text turn). |
| `max_budget_usd` / `maxBudgetUsd` | D1 | Loop control — caps execution by spend threshold; good default for production. |
| `AgentDefinition` | D1 | SDK object defining a subagent: `description` (required — routing signal), `prompt` (required — role/behavior), `tools` (optional allowlist), `disallowedTools` (optional denylist), `model` (optional override), `skills` (optional preload), `memory` (optional: `user`\|`project`\|`local`). |
| `allowedTools` / `"Task"` | D1 | The coordinator's `allowedTools` must include `"Task"` for it to invoke subagents without permission prompts. |
| Filesystem-based subagents | D1 | Declarative subagent definitions stored as markdown under `.claude/agents/`, vs. programmatic `agents` param in SDK code. |
| `fork_session` | D1 | Creates an independent branch from a shared analysis baseline so divergent approaches can be explored without losing or mutating the original session. |
| `PreToolUse` | D1 / D2 | Hook fired before a tool executes; can allow/block/modify. Exam use case: block dangerous ops (e.g., `.env` writes, refunds over a threshold). |
| `PostToolUse` | D1 / D2 | Hook fired after a tool result returns; used for data normalization and audit logging. |
| `SubagentStop` | D1 | Hook fired when a subagent completes — used to aggregate results. |
| `PreCompact` | D1 / D5 | Hook fired before context compaction — used to archive the full transcript. |
| `UserPromptSubmit` | D1 | Hook fired when a prompt is submitted — used to inject additional context. |
| `Stop` | D1 | Hook fired when the agent stops — used to save session state. |
| `PermissionRequest` | D1 | Hook fired when a permission dialog would show — used for custom permission handling. |
| Programmatic vs. prompt-based enforcement | D1 | Programmatic (hooks/gates) = deterministic, 100% reliable, for compliance-critical steps. Prompt-based guidance = probabilistic, non-zero failure rate, for general ordering only. |

> [!IMPORTANT] The single most important exam heuristic
> If something **must always** happen → enforce it in **code** (hook / gate / schema validation / deterministic routing). If something **should usually** be right → guide it with a **prompt** (instructions / examples / tool descriptions). Keywords like "compliance", "guaranteed", "100%", "cannot be left to model discretion" signal a code/hook answer.

---

## D2 — Tool Design & MCP Integration

| Term | Domain | What it does / why it matters |
|---|---|---|
| `isError` | D2 | Boolean flag MCP tools return to signal failure — **not** a thrown exception. `isError: true` means something went wrong accessing data; an empty result array with `isError: false` means the query succeeded but found no matching data. |
| `.mcp.json` | D2 / D3 | Project-scope MCP server config file, checked into version control and shared with the whole team. Use `${VAR_NAME}` syntax for credentials — never commit secrets directly. |
| `~/.claude.json` | D2 / D3 | User-scope MCP server config — personal only, **not** shared via version control. |
| `tool_choice` | D2 / D4 | (See Fundamentals row above.) In D2 context: `"any"` guarantees structured/tool output; `{"type":"tool","name":...}` forces a specific tool (e.g., ensure metadata extraction runs before enrichment). |
| MCP server | D2 | External process exposing tools/resources to Claude via a standard protocol; distributed at project or user scope. |

---

## D3 — Claude Code Configuration & Workflows

| Term | Domain | What it does / why it matters |
|---|---|---|
| `CLAUDE.md` | D3 | Project/user/managed-policy context file loaded into every session. Hierarchy loads broad → specific: managed policy → user (`~/.claude/CLAUDE.md`) → project (`./CLAUDE.md` or `./.claude/CLAUDE.md`), concatenated root → working directory; more specific instructions appear later in context. Subdirectory `CLAUDE.md` files are **not** loaded at launch — they load on demand when Claude reads files in that subdirectory. |
| `CLAUDE.local.md` | D3 | Appended after `CLAUDE.md` at each level; typically personal/local overrides not meant for version control. |
| `@import` | D3 | Syntax for pulling modular files into `CLAUDE.md` (avoids one monolithic file). Has a depth limit on import chains — know it's finite for the exam. |
| `/memory` | D3 | Slash command that shows which `CLAUDE.md`/memory files are currently loaded — used to diagnose inconsistent behavior across sessions. |
| `context: fork` | D3 | SKILL.md frontmatter field. Runs the skill in an **isolated subagent context** so its output doesn't pollute the main session's context window. |
| `allowed-tools` (SKILL.md) | D3 | Frontmatter field that **pre-approves** the listed tools for the turn that invokes the skill, so Claude uses them without a permission prompt. It does **not** restrict: every other tool stays callable and normal permission settings still govern it. The grant clears on your next message. ([docs](https://code.claude.com/docs/en/skills), checked 2026-08-24) |
| `disallowed-tools` (SKILL.md) | D3 | The field that actually **restricts** — removes the listed tools from Claude's available pool while the skill is active. Also clears on your next message. Don't confuse with `allowed-tools`. |
| `argument-hint` (SKILL.md) | D3 | Frontmatter field documenting expected slash-command arguments. |
| Path-scoped rules (`.claude/rules/`) | D3 | Rules with `paths:` frontmatter that load only for matching files — preferred over subdirectory `CLAUDE.md` when instructions should apply narrowly rather than always-on-demand. |
| Plan mode | D3 | Claude Code mode for proposing a plan before executing — used for higher-risk or exploratory changes. |
| `-p` / `--print` | D3 / Fundamentals | Non-interactive CLI flag; required for CI (prevents hanging on input prompts). |
| `--output-format json` | D3 / Fundamentals | Machine-parseable CLI output; pair with `--json-schema '<inline schema>'` for enforced structure in pipelines — **inline JSON string, never a file path**; validated object lands in `structured_output`. |
| AGENTS.md vs CLAUDE.md | D3 | Claude Code reads `CLAUDE.md`, **not** `AGENTS.md`. To reuse an existing `AGENTS.md`, reference it from `CLAUDE.md` or symlink `CLAUDE.md → AGENTS.md`. |

---

## D4 — Prompt Engineering & Structured Output

| Term | Domain | What it does / why it matters |
|---|---|---|
| `output_config.format` | D4 / Fundamentals | Messages API field for structured output: `{type: "json_schema", schema: {...}}`. Replaces the deprecated top-level `output_format` parameter. |
| Strict tool use (`strict: true`) | D4 / D2 | Set on a tool definition to guarantee schema-valid tool inputs (constrained decoding). Strict schemas prevent **syntax** errors but do **not** prevent **semantic** errors (e.g., line items that don't sum to the total, right-shaped-but-wrong values). |
| `tool_choice` | D4 / D2 | (See above.) For structured output: `"any"` when multiple extraction schemas exist and the document type is unknown; force a specific tool name to guarantee a particular schema. |
| Message Batches API (`POST /v1/messages/batches`) | D4 | Asynchronous bulk processing at **50% cost**. Up to 100,000 requests or 256 MB per batch (whichever first). Results available when all complete or after 24h max; results retained 29 days. Multi-turn conversations and **server-side** tool use work inside a batch request (it runs the same agentic loop as a sync call); what you cannot do is a **client-executed** tool round-trip mid-request — a limit of any single Messages API call, not something batch uniquely lacks. See `D4`. Not ZDR-eligible. Use for non-latency-sensitive bulk work (overnight reports, nightly test gen) — **never** for pre-merge blocking checks or latency-sensitive user-facing features. |
| `custom_id` | D4 | Required per-request field in a Message Batches API request (1–64 chars, alphanumeric + hyphens + underscores). Correlates request ↔ result since batch results arrive in **any order** — always key by `custom_id`, and on partial failure resubmit only the failed `custom_id`s (chunking if the failure was context-limit related). |
| Few-shot examples | D4 | Concrete input/output examples embedded in the prompt to steer output shape/quality — a prompt-based (probabilistic) technique. |
| Explicit criteria | D4 | Spelling out evaluation/success criteria directly in the prompt rather than relying on implicit understanding. |
| Multi-instance review | D4 | Running multiple independent Claude passes over the same output (e.g., separate reviewer calls) and reconciling results for higher reliability. |

---

## D5 — Context Management & Reliability

| Term | Domain | What it does / why it matters |
|---|---|---|
| `/compact` | D5 / Fundamentals | Summarizes the conversation to reduce token usage while preserving key info. Skill descriptions are **not** re-injected after `/compact` — only skills actually invoked in the session are preserved. |
| `/clear` | D5 | Slash command that wipes the current session context entirely (fresh start). |
| `/usage` | D5 | Slash command that reports current token/context usage. |
| `/rename` | D5 | Slash command that renames the current session. |
| `PreCompact` hook | D5 / D1 | Fires before context compaction — used to archive the full transcript before it's summarized away. |
| `cache_control` | D5 / Fundamentals | (See Fundamentals row.) Prefix-match caching; any earlier-byte change invalidates the cache for everything after it — order cacheable content so it's stable and comes first. |
| Escalation | D5 | Pattern of routing a task to a stronger model or to human review when confidence is low or stakes are high, rather than silently guessing. |
| Error propagation | D5 | Discipline of surfacing tool/subagent errors up the chain (not swallowing them) so failures are visible and actionable rather than silently corrupting downstream state. |
| Provenance | D5 | Tracking where a piece of information/data came from (source URLs, document IDs, page numbers) so outputs can be traced back and verified. |
| Structured summary vs. resume | D5 / D1 | When prior tool results are stale or the session is old, starting fresh with an injected structured summary is more reliable than resuming with stale data. |
| Codebase exploration | D5 | Read-heavy investigation phase (e.g., via `Explore` subagent) before making changes — kept fast/cheap by skipping `CLAUDE.md` loading. |
| Human review gate | D5 | A deliberate checkpoint requiring human sign-off before a high-stakes action proceeds — a code-level (not prompt-level) control. |

---

## Model Lineup (checked 2026-08-24)

> [!IMPORTANT] Use EXACT model ID strings; never append date suffixes to aliases.

> [!WARNING] Verify this table before exam day
> This is the fastest-rotting section in the vault — it has already crossed one model launch. Re-check against [the models overview](https://platform.claude.com/docs/en/models/overview) and [pricing](https://platform.claude.com/docs/en/about-claude/pricing) rather than trusting the values below.

**Current models**

| Model | Model ID | Context | Max output | Input $/1M | Output $/1M |
|---|---|---|---|---|---|
| Claude Fable 5 | `claude-fable-5` | 1M | 128K | $10.00 | $50.00 |
| Claude Mythos 5 (Project Glasswing only) | `claude-mythos-5` | 1M | 128K | $10.00 | $50.00 |
| Claude Opus 5 | `claude-opus-5` | 1M | 128K | $5.00 | $25.00 |
| Claude Sonnet 5 | `claude-sonnet-5` | 1M | 128K | $2.00 | $10.00 |
| Claude Haiku 4.5 | `claude-haiku-4-5` | 200K | 64K | $1.00 | $5.00 |

**Legacy models** — still available, no longer recommended for new work

| Model | Model ID | Context | Max output | Input $/1M | Output $/1M |
|---|---|---|---|---|---|
| Claude Opus 4.8 | `claude-opus-4-8` | 1M | 128K | $5.00 | $25.00 |
| Claude Opus 4.7 | `claude-opus-4-7` | 1M | 128K | $5.00 | $25.00 |
| Claude Opus 4.6 | `claude-opus-4-6` | 1M | 128K | $5.00 | $25.00 |
| Claude Sonnet 4.6 | `claude-sonnet-4-6` | 1M | 128K | $3.00 | $15.00 |

- **Default / most-used model:** `claude-opus-5` — docs: "If you're unsure which model to use, start with Claude Opus 5 for complex agentic coding and enterprise work." **Most capable:** `claude-fable-5` ("Anthropic's most capable widely released model").
- Current generation: Fable 5, Mythos 5, Opus 5, Sonnet 5, Haiku 4.5. The Opus 4.x line and Sonnet 4.6 are now **legacy**.
- **Sonnet 5 is $2 / $10 flat.** The $3 / $15 increase once scheduled for 2026-09-01 **will not occur** — the introductory price became the standard price. Any answer implying a September price rise is wrong.
- Max output figures are for the synchronous Messages API. On the Batch API, Opus 5 / 4.8 / 4.7 / 4.6 and Sonnet 5 / 4.6 reach **300K** output tokens via the `output-300k-2026-03-24` beta header.

*Sources: [models overview](https://platform.claude.com/docs/en/models/overview) · [pricing](https://platform.claude.com/docs/en/about-claude/pricing), checked 2026-08-24. Corrected from a stale 2026-07 lineup that omitted Opus 5, named `claude-opus-4-8` as the default, and carried the cancelled Sonnet 5 price rise.*

---

## See also

- [[CCA-F Study Roadmap]] — overall study plan and sequencing
- [[00 - Claude Model Family & API Fundamentals]] — full fundamentals deep dive
- [[D1 - Agentic Architecture & Orchestration]] · [[D2 - Tool Design & MCP Integration]] · [[D3 - Claude Code Configuration & Workflows]] · [[D4 - Prompt Engineering & Structured Output]] · [[D5 - Context Management & Reliability]]
- [[Flashcards]] — active-recall drills for these same terms
