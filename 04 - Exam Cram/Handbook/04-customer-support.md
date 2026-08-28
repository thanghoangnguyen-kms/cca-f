---
tags:
  - CCA-F
  - handbook
  - domain-2
  - domain-5
date: 2026-08-24
status: done
---

# 04 — Customer Support Agents

Covers: statelessness, deterministic guardrails/hooks, escalation, structured
handoff, error metadata, graceful degradation, context pruning. (Mock Q31–Q45;
concept chapters 3, 13.)

---

## Core concepts (memorize)

### The API is stateless
- Every request re-sends the **entire `messages` array.** Memory across turns is
  a function of **what you re-send**, not an internal model feature.
- If the agent "forgets" earlier answers (asks for the name again), you almost
  certainly **aren't re-sending the full history.** No "2-turn default limit"
  exists; a prompt saying "remember" can't create memory; tools don't clear state.

### Returning customers & stale tool results
- **Stale `tool_result` blocks poison reasoning** — the model keeps citing
  outdated data even after fresh calls.
- Best fix: **start a new session + inject a structured summary + make fresh
  tool calls before engaging.** Don't edit the middle of a transcript (orphans
  `tool_use` blocks), don't rely on a "prefer newest" prompt, don't auto-recall
  every tool (stale results still sit in context).

### Deterministic guardrails (hooks) beat prompts
- **Prompt = soft guidance; code = enforcement.** Prompts can be bypassed via
  injection, persuasion, or misread context.
- Compliance-critical rules ("refunds > $500 must escalate — cannot be left to
  model discretion") → a **hook that intercepts the tool call** and blocks/
  redirects. Fires **every time**, independent of the model. A 3% prompt-failure
  rate → hook drives it to 0.
- **PreToolUse hook** = check/block **before** a tool runs (e.g. don't allow
  `lookup_order` before verifying the customer).
- **PostToolUse hook** = normalize output, log, fix format **after** a tool runs.

### Escalation triggers
- Route **judgment calls** ("does this need a human?") through **clear
  natural-language criteria** the model reasons over: explicit human request,
  policy-exception need, or **can't make meaningful progress.**
- Route **hard limits** through **deterministic code** (e.g. amount thresholds).
- Do **NOT** trigger on: a fixed **failed-call counter**, **sentiment**
  thresholds, or a brittle **rules engine** over issue×segment×product.
- Model **self-reported confidence** is not a safety mechanism unless calibrated.
- When a clear escalation trigger fires, **hand off immediately** — don't keep processing.

### Scope boundaries (distinct from escalation)
- Regulated professional advice — **legal, medical, tax, immigration** — is
  **out of scope, always.** Name the boundary plainly, then route to the right
  resource or a human. Don't hedge, don't stonewall, don't "best effort."
- Refuse the **task**, keep helping the **person**. Declining *in-scope* work is
  a failure; declining *out-of-scope* work is correct.
- Scope is a **safety** property, not a helpfulness trade-off: the agent is
  outside its competence *and* its authorization, so a confident answer is
  worse than none.

### Structured handoff to a human
- The human **won't see the transcript.** Pass a **self-contained structured
  brief: customer ID + root cause + amount + recommended action.**
- Not: verbatim complaint + raw tool excerpts (no synthesis), not the full
  transcript (buries the answer), not diagnosis-only (human can't locate/act).

### Error handling & tool-result metadata
- Uniform `"Operation failed"` gives the agent nothing to differentiate → it
  guesses (over-retries, premature escalation, wrong clarification).
- Fix in the **tool's own response**: return **`errorCategory`
  (transient/validation/permission) + `isRetryable` boolean + cause
  description**, and a **customer-friendly message** the model can relay.
- Surface errors with **`isError`/`is_error = true` + a readable message** in
  `content`. Never return empty success, throw an uncaught exception, or use
  ad-hoc `status` fields.
- Connectors should **absorb transient infra errors with retry/backoff** rather
  than pushing them up to the agent.

### Graceful degradation & empathy
- Partial failure (e.g. `process_refund` times out but explanation + eligibility
  succeeded): **deliver the value you can, be honest about what failed, offer
  escalation or retry-later.** Don't escalate a transient blip immediately,
  don't loop retries, and never confirm an action that didn't happen.
- Frustrated customer, **no context yet**: one **empathetic, targeted question**
  beats immediate escalation (cold handoff) or silent investigation.
- Frustrated customer, but you **can resolve now**: acknowledge the feeling,
  state the fast path, and **keep the choice with the customer** (don't queue,
  don't unilaterally override their request).

### Long multi-topic sessions
- Nearing context limits across resolved topics: **compact resolved threads into
  summaries, keep the active thread verbatim.** Don't slide a fixed window
  (drops the topic being asked about); don't bolt on a vector DB.
- Verbose tool outputs (40+ fields) dominating context: **prune to the
  task-relevant fields** (structured, lossless) before more calls — don't
  paraphrase (loses exact values), don't add a vector store.

---

## Decision rules

| Trigger | Answer |
|---|---|
| Returning customer + stale tool result keeps being cited | New session + structured summary + fresh tool calls |
| "Compliance / cannot be left to model discretion" | **Hook intercepts the tool call** (deterministic) |
| Block a tool until a precondition is met | **PreToolUse hook** |
| Normalize/log tool output after the fact | **PostToolUse hook** |
| Design "needs a human" trigger | **Clear NL criteria** (explicit request / policy exception / no progress) |
| Hand off to a human who can't see the transcript | Structured brief: **ID + root cause + amount + recommended action** |
| Errors handled inconsistently (over-retry, mis-escalate) | Structured metadata: **category + isRetryable + cause** in the tool result |
| Communicate a backend error to the agent | `isError=true` (API field: `is_error`; `isError` is MCP/TS camelCase) + readable message in `content` |
| A tool times out mid-flow but you have partial value | **Graceful degradation**: deliver value, be honest, offer options |
| Agent "forgot" earlier verification answers | You aren't re-sending the **full `messages` array** |
| Long multi-issue session near context limit | Compact resolved threads; keep active thread verbatim |
| Verbose tool outputs flooding context | **Prune to task-relevant fields** before more calls |
| Frustrated customer, no context yet | One empathetic, **targeted question** |
| Customer requests legal / medical / tax advice | **Out of scope** — state the limit, route to a resource or human |

---

## Anti-patterns

- **Prompt-only** enforcement of a compliance rule (use a hook).
- Returning a **tool error string** and hoping the model escalates (decision
  still at the model layer).
- Escalation on **sentiment**, **failure count**, or an **issue×segment rules engine**.
- **Deleting tool_result** from the middle of a transcript (orphans `tool_use`).
- Handing a human the **full transcript** or a **raw dump** instead of a brief.
- **Confirming** an action (refund) that didn't actually process.
- **Sliding a fixed window** that drops the topic being asked about.
- **Paraphrasing** verbose tool output (loses exact dates/amounts).

---

## Key phrases to recognize

"stateless / re-send the messages array" · "stale tool_result poisons reasoning" ·
"cannot be left to model discretion → hook" · "PreToolUse / PostToolUse" ·
"structured handoff brief" · "errorCategory + isRetryable" · "isError (API field: is_error)" ·
"graceful degradation" · "compact resolved threads, keep active verbatim" ·
"prune to task-relevant fields."

## Sources
- Customer support use-case guide — https://platform.claude.com/docs/en/about-claude/use-case-guides/customer-support-chat
- Context windows (statelessness, compaction) — https://platform.claude.com/docs/en/build-with-claude/context-windows
- Tool use — handling errors — https://platform.claude.com/docs/en/agents-and-tools/tool-use/handle-tool-calls
- Building effective agents — https://www.anthropic.com/engineering/building-effective-agents
