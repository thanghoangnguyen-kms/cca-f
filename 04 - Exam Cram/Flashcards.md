---
tags:
  - CCA-F
  - exam-cram
  - flashcards
date: 2026-07-11
status: done
---

# 🃏 Flashcards & Key Terms

> [!NOTE] How to Use This Deck
> Active-recall cards grouped by domain, plus a **Fundamentals** section for the model lineup and API basics. Cover the `A:` line and try to answer before revealing it. Cross-reference the source notes if a card doesn't click: [[00 - Claude Model Family & API Fundamentals]], [[D1 - Agentic Architecture & Orchestration]], [[D2 - Tool Design & MCP Integration]], [[D3 - Claude Code Configuration & Workflows]], [[D4 - Prompt Engineering & Structured Output]], [[D5 - Context Management & Reliability]], [[Critical Terms Glossary]]. Roadmap: [[CCA-F Study Roadmap]].

---

## Fundamentals

**Q: What is the current (2026-07) Claude model lineup and their API IDs?**
A: Claude Fable 5 (`claude-fable-5`), Claude Mythos 5 (`claude-mythos-5`, Project Glasswing only), Claude Opus 4.8 (`claude-opus-4-8`), Claude Opus 4.7 (`claude-opus-4-7`), Claude Sonnet 5 (`claude-sonnet-5`), Claude Sonnet 4.6 (`claude-sonnet-4-6`), Claude Haiku 4.5 (`claude-haiku-4-5`).

**Q: Which model is the default / most-used model, and which is most capable?**
A: `claude-opus-4-8` is the default/most-used model. `claude-fable-5` is the most capable model.

**Q: What is the context window and max output for the current model lineup?**
A: All models except Haiku have a 1M-token context window and 128K max output. Haiku 4.5 has a 200K context window and 64K max output.

**Q: What is the single API endpoint all Claude requests go through?**
A: `POST /v1/messages` (the Messages API) — tool use and structured output are features of this one endpoint, not separate APIs.

**Q: Is the Messages API stateful or stateless? What does that imply?**
A: Stateless — you must resend the full conversation history with every request; the server does not retain prior turns for you.

**Q: What are the primary `stop_reason` values and what does each mean?**
A: `end_turn` (done — terminate loop), `tool_use` (execute tool, continue loop), `max_tokens` (hit output cap, response may be truncated), `pause_turn` (server-tool loop paused, resume the request), `stop_sequence` (hit a configured stop string), `refusal` (safety decline — check `stop_details`), and `model_context_window_exceeded` (Claude 4.5+ only — context window exhausted; distinct from `max_tokens`, which caps output, not total context).

**Q: How is thinking depth controlled on current models, and what changed from the old API?**
A: Use `thinking: {type: "adaptive"}` with depth set via `output_config: {effort: "low"|"medium"|"high"|"xhigh"|"max"}` (default `high`). The old `thinking: {type: "enabled", budget_tokens: N}` is deprecated and returns a 400 error on Fable 5, Opus 4.7/4.8, and Sonnet 5.

**Q: How do you enforce structured output on the current API, and what's deprecated?**
A: Use `output_config: {format: {type: "json_schema", schema: {...}}}`. The old top-level `output_format` field is deprecated. `strict: true` on a tool definition guarantees valid tool inputs.

**Q: What are the four `tool_choice` values?**
A: `{"type": "auto"}` (default — model decides), `{"type": "any"}` (must call some tool), `{"type": "tool", "name": ...}` (force one specific tool), `{"type": "none"}` (no tool calls).

**Q: What are the key facts about the Message Batches API?**
A: `POST /v1/messages/batches`, asynchronous, 50% of standard cost, up to 100K requests per batch, most finish under 1 hour (max 24h), and `custom_id` correlates each request to its result since results arrive in any order.

**Q: How does prompt caching work, and what invalidates it?**
A: Mark a content block `cache_control: {type: "ephemeral"}`. Caching is a prefix match — any byte change earlier in the prefix invalidates everything cached after it. Reads cost ~0.1x, writes cost ~1.25x, default TTL 5 minutes. Verify hits via `usage.cache_read_input_tokens`.

**Q: What happened to assistant-turn prefilling?**
A: It's removed — a last-assistant-turn prefill now returns a 400 error on Fable 5, Opus 4.6/4.7/4.8, and Sonnet 4.6/5. Use structured outputs or system-prompt instructions instead.

**Q: What is the golden heuristic for choosing code vs. prompt to enforce a behavior?**
A: If something MUST always happen, enforce it in **code** (hook / gate / schema validation / deterministic routing). If something should USUALLY be right, guide it with a **prompt** (instructions / examples / tool descriptions). Prompts shift probability; code guarantees outcomes.

**Q: What keywords in an exam question signal the "code" answer over the "prompt" answer?**
A: "Compliance", "guaranteed", "100%", and "cannot be left to model discretion" — these all point to hooks/gates/schema validation, not prompt instructions.

---

## Domain 1

**Q: What is the correct signal to terminate the agentic loop?**
A: `stop_reason: "end_turn"`. Never use arbitrary iteration caps, parsed natural-language "done" signals, or the mere presence of text content as the primary termination check.

**Q: What happens when `stop_reason` is `"tool_use"`?**
A: Claude wants to call a tool — execute it, feed the result back as the next message, and continue the loop.

**Q: Do subagents automatically inherit the coordinator's conversation history?**
A: No. Subagent context is isolated by default; the coordinator must explicitly inject relevant context into each subagent's prompt.

**Q: What must be in `allowedTools` for a coordinator to invoke subagents without permission prompts?**
A: `"Task"`.

**Q: What are the required fields of an `AgentDefinition`?**
A: `description` (how Claude decides when to invoke it) and `prompt` (the subagent's role/expertise/constraints). Optional fields include `tools`, `disallowedTools`, `model`, `skills`, and `memory`.

**Q: How do you run subagents in parallel rather than sequentially?**
A: Emit multiple `Task` tool calls within a single response — calls spread across separate turns run sequentially, not in parallel.

**Q: Hooks vs prompt instructions — which gives a deterministic guarantee?**
A: Hooks. A `PreToolUse` hook that returns `permissionDecision: "deny"` blocks a tool call absolutely; prompt instructions are probabilistic and carry a non-zero failure rate.

**Q: Name the key Agent SDK hook events and one exam use case each.**
A: `PreToolUse` (block dangerous ops before execution), `PostToolUse` (normalize/audit tool results — also handles tool errors), `UserPromptSubmit` (inject context), `Stop` (save session state), `SubagentStop` (aggregate parallel subagents), `PreCompact` (archive transcript before compaction).

**Q: When should you start a fresh session instead of resuming an old one?**
A: When prior tool results are stale — starting fresh with an injected structured summary is more reliable than resuming a session full of stale data.

**Q: What is `fork_session` used for?**
A: Branching an independent copy from a shared analysis baseline to explore an alternative approach without losing or mutating the original session.

**Q: Prompt chaining vs dynamic adaptive decomposition — when to use each?**
A: Prompt chaining (fixed sequential steps) suits predictable multi-aspect tasks with known steps. Dynamic decomposition suits open-ended investigation where the plan must adapt as findings emerge.

**Q: Why split a large multi-file code review into per-file and cross-file passes?**
A: Reviewing 10+ files simultaneously causes attention dilution and contradictory findings; a per-file local pass followed by a cross-file integration pass keeps each pass focused.

---

## Domain 2

**Q: What is the primary mechanism an LLM uses to select which tool to call?**
A: The tool's description. Vague or near-identical descriptions across similar tools cause unreliable, misrouted tool selection.

**Q: What five elements should a good tool description include?**
A: Purpose, expected input formats/constraints, example queries, edge cases/boundaries, and guidance on when to use this tool vs. alternatives.

**Q: How do well-designed MCP tools signal failure?**
A: By returning `isError: true` in a structured response (with fields like `errorCategory`, `isRetryable`, and a description) — not by throwing an exception the agent can't reason about.

**Q: Is an empty results array from an MCP tool an error?**
A: No — if `isError: false`, an empty array means the query succeeded but no matching data exists. Conflating this with a real access failure causes wasted retries.

**Q: What are the four MCP error categories and are they retryable?**
A: Transient (`isRetryable: true`), Validation (`false`), Business/policy (`false`), Permission (`false`).

**Q: How many tools per agent is reliable, and how many degrades selection?**
A: 4–5 tools per agent is reliable; 10+ degrades selection reliability; 18+ causes significant tool misuse.

**Q: What are the two MCP configuration scopes and their file locations?**
A: Project scope → `.mcp.json` (version-controlled, team-shared). User scope → `~/.claude.json` (personal, not shared via version control).

**Q: What is the MCP tool naming convention?**
A: `mcp__{server-name}__{tool-name}` — e.g., `mcp__github__list_issues`.

**Q: What are the four MCP transport types?**
A: `stdio` (local process via `command`/`args`), HTTP/`streamable-http` (recommended for remote servers), SSE (legacy, deprecated), WebSocket (bidirectional/event-pushing).

**Q: Does `permissionMode: "acceptEdits"` auto-approve MCP tool calls?**
A: No. It auto-approves file edits and filesystem commands but not MCP tools — those need explicit `allowedTools` (e.g., wildcard `"mcp__server__*"`).

**Q: Grep vs Glob — what does each search?**
A: `Grep` searches file **contents** by pattern. `Glob` finds files by **name/path** pattern.

**Q: When does `Edit` fail, and what's the fallback?**
A: When the anchor text isn't unique in the file. Fallback: `Read` the full file, then `Write` the entire modified content back.

---

## Domain 3

**Q: What are the four CLAUDE.md scopes, from broadest to most specific?**
A: Managed policy (org-wide, e.g. `/Library/Application Support/ClaudeCode/CLAUDE.md`) → User (`~/.claude/CLAUDE.md`, personal) → Project (`./CLAUDE.md`, version-controlled, team-shared) → Local (`./CLAUDE.local.md`, personal + gitignored). Files concatenate broad-to-specific.

**Q: A new team member isn't getting expected instructions — what's the likely cause?**
A: The instructions were written to user scope (`~/.claude/CLAUDE.md`), which is not version-controlled, instead of project scope (`./CLAUDE.md`).

**Q: Do subdirectory CLAUDE.md files load at session launch?**
A: No — they load on demand, only when Claude reads files within that subdirectory.

**Q: What is the `@import` chain depth limit for CLAUDE.md?**
A: 4 hops maximum.

**Q: What does `context: fork` do in a SKILL.md frontmatter?**
A: Runs the skill in an isolated subagent context so verbose intermediate output stays contained — only the final summary returns to the main session.

**Q: What does `disable-model-invocation: true` do in a skill?**
A: Prevents Claude from auto-triggering the skill based on context; it only runs when explicitly invoked via `/skill-name`.

**Q: When should you prefer plan mode over direct execution?**
A: For complex tasks with multiple valid approaches, large-scale/multi-file changes, architectural decisions, or open-ended investigation — plan mode enables safe exploration before committing to changes.

**Q: What flag is required to run Claude Code non-interactively in CI, and why?**
A: `-p` / `--print`. Without it, Claude Code waits for interactive input and hangs in an automated pipeline.

**Q: How do you get machine-parseable structured output from a CI-invoked Claude Code run?**
A: `--output-format json` combined with `--json-schema <schema-file>` to enforce a specific JSON structure.

**Q: Why is self-review in the same session unreliable, and what's the fix?**
A: The model retains its own generation reasoning context, making it less likely to question its prior decisions. Fix: spin up an independent Claude Code instance with no prior reasoning context to review.

**Q: When should you use path-scoped `.claude/rules/` files instead of a subdirectory CLAUDE.md?**
A: When conventions apply to files spread across multiple directories (e.g., all `*.test.tsx` files anywhere) — a single `paths:`-scoped rule file targets by pattern regardless of location.

**Q: What does `/memory` do?**
A: Shows which CLAUDE.md and memory files are currently loaded — used to diagnose inconsistent behavior across sessions.

---

## Domain 4

**Q: Why don't vague filters like "only report high-confidence findings" reduce false positives?**
A: They don't specify which category of issue to skip. Explicit categorical criteria with concrete code examples (e.g., "flag only when claimed behavior contradicts actual code") are what actually improve precision.

**Q: How many few-shot examples are typically recommended, and what should they show?**
A: 2–4 targeted examples, wrapped in `<example>`/`<examples>` tags. They should demonstrate exact output format and the reasoning behind ambiguous decisions, not just correct outputs.

**Q: What's the most reliable mechanism for guaranteed schema-compliant structured output?**
A: Tool use with a JSON schema (or `output_config.format` with `type: "json_schema"`) — this eliminates JSON syntax errors via constrained decoding. Plain "respond as JSON" prompting gives no such guarantee.

**Q: Does a strict JSON schema prevent semantic errors too?**
A: No — it only guarantees syntactic/structural validity. It does not prevent semantic errors like line items that don't sum to the stated total.

**Q: Which `tool_choice` value guarantees the model calls a tool instead of returning plain text?**
A: `"any"` — the model must call some tool (any available one).

**Q: Why should extraction schema fields be nullable rather than required?**
A: When a source document may not contain that field's data, marking it nullable/optional prevents the model from fabricating a value just to satisfy a required field.

**Q: When are retries ineffective in a validation/retry extraction loop?**
A: When the required information simply doesn't exist in the source document — no amount of retrying with error feedback will make missing data appear.

**Q: What are the Batch API's defining cost/latency characteristics?**
A: 50% of standard synchronous pricing, up to 100K requests per batch, most complete inside an hour (24h max), and no latency SLA — never use it for pre-merge or other latency-sensitive blocking checks.

**Q: What does `custom_id` do in a batch request, and why does it matter?**
A: It correlates each request to its result. Batch results can arrive in any order, so you must key off `custom_id` to match them back up.

**Q: Batch vs synchronous API — how do you decide?**
A: Batch API for non-latency-sensitive bulk work (overnight reports, nightly test generation). Synchronous API for blocking workflows (pre-merge checks, interactive user-facing features).

**Q: What's the fix for the self-review limitation described in prompt engineering / multi-instance review architectures?**
A: Use an independent review instance (a fresh session with no prior generation reasoning context) rather than asking the same session to review its own output.

**Q: For 20K+ token inputs, where should long documents and the query go?**
A: Long documents at the top, the query/instructions at the bottom — this ordering can improve response quality by up to 30%.

---

## Domain 5

**Q: What kinds of information are most at risk during progressive summarization/context compaction?**
A: Numerical values, specific dates/timestamps, order/customer IDs, and customer-stated expectations. Fix: keep a persistent "case facts" block outside the summarized history, included in every prompt.

**Q: What does `/compact` do, and what is NOT preserved across it?**
A: `/compact` summarizes conversation history to reduce token usage while preserving key findings. Skill descriptions are not automatically re-injected after `/compact` — only skills you actually invoked get preserved.

**Q: What are the signs of context degradation in a long session?**
A: Claude references "typical patterns" instead of specific findings, gives inconsistent answers about the same component, or appears to "forget" decisions made earlier in the session.

**Q: What is the "lost in the middle" effect, and how do you mitigate it?**
A: Models reliably attend to the beginning and end of long inputs but may deprioritize the middle. Mitigate by placing key-findings summaries at the start and using explicit section headers.

**Q: When a customer explicitly requests a human agent, what should happen?**
A: Escalate immediately, with no investigation or resolution attempt first — this is a non-negotiable trigger, unlike general case complexity.

**Q: Are sentiment analysis or self-reported model confidence reliable escalation triggers?**
A: No — frustrated tone doesn't equal case complexity, and a model's self-reported confidence doesn't equal actual complexity. Use explicit business rules with few-shot examples instead.

**Q: What should a subagent return when it can't recover from an error, instead of a generic failure message?**
A: Structured error context: failure type, attempted query, partial results, retry history, and possible alternative approaches — enough for the coordinator to decide retry vs. escalate vs. alternate source.

**Q: Access failure vs. valid empty result — why does conflating them matter?**
A: Access failure means something went wrong (consider retry/escalation); a valid empty result means the query succeeded with no matching data (do not retry). Conflating them wastes retry attempts.

**Q: What are scratchpad files used for during large codebase exploration?**
A: Persisting key findings (entry points, known bugs, module mappings) across context boundaries, so degraded/reset context can be restored by referencing the file instead of re-exploring from scratch.

**Q: What is the aggregate accuracy trap?**
A: A high overall accuracy figure (e.g., 97%) can mask poor performance on specific document types or fields (e.g., 99% English vs. 60% French) — always break down accuracy by category before reducing human review.

**Q: What must be preserved when one agent's findings are summarized by a downstream agent (provenance)?**
A: A structured claim-source mapping (document, URL, excerpt, date) — plain-text summarization silently strips attribution, so require structured claim-source objects to survive synthesis.

**Q: How should conflicting statistics from credible sources be handled in a synthesis output?**
A: Annotate the conflict with source attribution for each value — never arbitrarily pick one or average them without noting the disagreement (and include collection/publication dates to rule out temporal, not real, discrepancies).

---

*Related: [[CCA-F Study Roadmap]] · [[Critical Terms Glossary]] · [[00 - Claude Model Family & API Fundamentals]] · [[D1 - Agentic Architecture & Orchestration]] · [[D2 - Tool Design & MCP Integration]] · [[D3 - Claude Code Configuration & Workflows]] · [[D4 - Prompt Engineering & Structured Output]] · [[D5 - Context Management & Reliability]]*
