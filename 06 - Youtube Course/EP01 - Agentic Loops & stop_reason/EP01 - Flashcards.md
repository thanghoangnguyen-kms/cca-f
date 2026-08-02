---
tags:
  - CCA-F
  - domain-1
  - flashcards
  - youtube-course
date: 2026-08-02
status: done
domain: "1 of 5"
source: "Peace Of Code — Claude Certified Architect Full Course Ep 01"
---

# 🃏 EP01 Flashcards — Agentic Loops & `stop_reason`

> [!NOTE] How to Use This Deck
> Active-recall cards drawn from [[EP01 - Agentic Loops & stop_reason]]. Cover the `A:` line and answer before revealing. This deck is **self-contained** — it covers the episode in full, so some cards overlap with the vault-wide [[Flashcards]] deck by design. Study either on its own.
>
> **Related:** [[D1 - Agentic Architecture & Orchestration]] · [[D2 - Tool Design & MCP Integration]] · [[Critical Terms Glossary]] · [[CCA-F Study Roadmap]]

---

## Domain 1 — Agentic Architecture

**Q: What are the two `stop_reason` values that drive a client-side tool loop, and what does each instruct you to do?**
A: `tool_use` — Claude wants to act; execute the requested tools, append results, continue the loop. `end_turn` — Claude has finished this turn; extract the text blocks and break. `end_turn` is the only valid primary exit.

**Q: The Claude API is stateless. What does that force you to do on every request?**
A: Resend the **entire conversation history** from the first message onward — nothing is stored server-side between calls. The messages array *is* the agent's memory.

**Q: Why is `stop_reason` the only safe loop-termination signal?**
A: Everything else measures the wrong thing. Claude's wording varies between calls, a `tool_use` turn may carry no text block at all, content type describes payload shape rather than intent, and an iteration cap describes elapsed effort rather than completion. Only `stop_reason` reports why Claude actually stopped.

**Q: What are the three message roles, and which is set only once per conversation?**
A: `system`, `user`, `assistant`. The `system` role — the persona/instruction block — is set once at the start, not re-sent per message.

**Q: Which role do you use when appending a tool result to the messages array?**
A: `user` — even though no human wrote it. Role tracks *direction of travel*, not authorship: anything flowing **into** Claude is `user`, anything flowing **out** is `assistant`.

**Q: Within a single tool-use turn, in what order do you append the two messages?**
A: **Assistant first, then user.** The assistant message carries Claude's `tool_use` blocks (`response.content`); the user message carries the tool results. The first message in the array overall is always `user`.

**Q: Does Claude execute tools itself?**
A: No. Claude only *requests* a tool call via a `tool_use` block. Your code executes the function and appends the result back — the model chooses, your runtime performs.

**Q: Does `stop_reason: "end_turn"` mean the task succeeded?**
A: No — it means Claude has finished *this turn*. A request for clarification (e.g. missing a required tool parameter) also returns `end_turn`. Never infer success from the stop reason alone.

**Q: Why is parsing Claude's text for a "done" signal crash-prone, not just unreliable?**
A: Beyond varying wording, a `tool_use` turn may contain **no text block at all** — indexing `content[0].text` then raises a fatal index/attribute error and kills the agent.

**Q: Why can't content-type inspection substitute for `stop_reason`?**
A: They measure entirely different things. Content type describes the *shape* of a payload; `stop_reason` describes Claude's *intent*. A turn can carry a text block and still be `tool_use`.

**Q: What is the correct role of an iteration cap in an agentic loop?**
A: A runaway safety valve only. Hitting the cap is an **error condition** that should raise or log — never a success path or the primary exit.

**Q: What links a tool result back to the request that produced it?**
A: The `tool_use_id` — it must match exactly between the `tool_use` block in the assistant message and the `tool_result` block in the following user message.

**Q: How many `stop_reason` values actually drive agentic-loop control flow?**
A: **Three, not two.** `end_turn` exits, `tool_use` executes-and-continues, and **`pause_turn` continues** — it fires when a *server-side* tool loop hits its iteration limit (default 10). Re-send the response as-is; do **not** append a "Continue." user message. The remaining values (`max_tokens`, `stop_sequence`, `refusal`, `model_context_window_exceeded`) signal truncation or a halt.

**Q: Claude returns three `tool_use` blocks in one message. How do you send the results back?**
A: Execute them concurrently, then return **all three `tool_result` blocks in a single `user` message**. Splitting them across separate messages silently trains Claude to stop making parallel calls. Every `tool_use` id needs a matching result — for a failure, return `tool_result` with `is_error: true` rather than dropping it.

---

## Domain 2 — Tool Design

**Q: What are the three required fields of a tool definition?**
A: `name` (what Claude calls it in `tool_use` blocks), `description` (how Claude decides *when* to use it), and `input_schema` (JSON Schema defining its parameters).

---

*Back to: [[EP01 - Agentic Loops & stop_reason]]*
