---
tags:
  - CCA-F
  - handbook
  - domain-1
  - domain-2
date: 2026-08-24
status: done
domain: "1 of 5"
---

# 01 — Agentic Loop & Tool Design

Covers: the tool-use loop, tool descriptions as routing, structured errors,
tool distribution, and structured output. (Maps to concept chapters 1, 4, 5, 6, 10.)

---

## Core concepts (memorize)

### The agentic loop — the exact flow
1. Send request to Claude.
2. Check **`stop_reason`**.
3. If `stop_reason == "tool_use"` → execute the tool(s).
4. **Append BOTH the assistant message AND the tool results** to the conversation.
5. Repeat until `stop_reason == "end_turn"`.

- **`stop_reason` is the most important control signal in the loop.**
- The primary stop condition is **`end_turn`**, NOT a loop counter.
- `MAX_ITERATIONS` is only a **safety ceiling**, never the main termination.
- If a response has **multiple `tool_use` blocks**, you must handle **all** of
  them and return **all** matching `tool_result`s before continuing the loop.
- How the loop "decides" the next tool: the tool result is appended to
  `messages` and the model is re-invoked; **the model chooses the next action
  from accumulated context** — no external router, no pre-committed plan, no
  decision tree.

### Tool descriptions ARE the routing logic
- When you pass `tools`, the API builds a system prompt from the tool
  definitions; **the model selects tools purely from the descriptions it sees.**
- Detailed descriptions are **"by far the most important factor in tool performance."**
- A specialized tool with a thin description **loses to a built-in** (e.g. Grep)
  with a rich one. Fix = **rewrite the description**, not add prompt routing or
  delete the competing tool.
- A good description states: **when to use / when NOT to use**, expected
  **input**, what it **returns**, and how it **differs** from similar tools.

### Structured error responses & `isError`
- Tools should NOT return vague error strings. Return **structured errors** so
  the agent knows what to do next.
- Set **`isError` / `is_error = true`** so the model knows it's a real failure,
  not an empty success. Put a **readable message in the `content`** too.
- "Order not found" may be a **valid empty result**, not necessarily an error.
- Timeout = `transient`. Permission denied = `permission`. (See categories below.)

### Tool distribution (limit per agent)
- Too many tools (e.g. **18**) degrades tool-selection quality.
- Rule of thumb: **~4–5 tools per agent**; beyond that, split into specialized agents.
- Isolate domain tools by role. Shared cross-role tools (logging/audit) must be **scoped**.
- Avoid the **"god agent."**

### Structured output (reliable JSON)
- Telling the model "return JSON" in the system prompt is **not reliable enough.**
- Correct approach: **define the output as a tool `input_schema` → force it with
  `tool_choice` → validate → on failure, feed the error into a retry loop.**
- Schema guarantees **structure, not factual correctness** → still need
  **semantic validation** (and reasoning/scratchpad for computed fields).

---

## Decision rules

| Trigger | Answer |
|---|---|
| Loop never terminates / stops early | Terminate on `stop_reason == "end_turn"`; cap is safety only |
| Model narrates a tool call but nothing runs, no errors | The tool isn't in **`allowedTools`** (e.g. `Task` missing) — can't invoke what isn't enabled |
| Agent keeps choosing the wrong/general tool | **Enrich the specialized tool's description** (capabilities, when-to-use, I/O) |
| Two similar tools confuse the model | Differentiate descriptions (e.g. `get_customer` = profile/account; `lookup_order` = order/shipment) |
| Need guaranteed JSON shape | Tool schema + `tool_choice` + validation + retry |
| Need tool B to run only after tool A | Force A with `tool_choice:{"type":"tool","name":"A"}`, then `auto` |
| One agent with 15+ tools underperforms | Split into specialized agents, ~4–5 tools each |

---

## Quick reference

### `tool_choice`
| Mode | Text reply? | Must use a tool? | Specific tool? |
|---|---|---|---|
| `auto` | Yes | No | No |
| `any` | No | Yes | No |
| `tool` | No | Yes | **Yes (named)** |
| `none` | Yes | No | No tools offered |

### Error categories
`transient` → retry · `validation` → fix+retry · `business` → tell user, no retry · `permission` → escalate/stop, no retry.

---

## Anti-patterns

- Stopping the loop by **iteration count** instead of `stop_reason`.
- **Not appending** the assistant turn or the tool results before looping.
- Returning tool errors as **ambiguous strings** or as **empty success**.
- Throwing an **uncaught exception** from a tool (breaks the protocol).
- Fixing wrong-tool selection with **system-prompt routing** instead of the description.
- Trusting **"return JSON please"** in the prompt for structured output.
- Piling **many tools onto one agent**.

---

## Key phrases to recognize

"stop_reason" · "end_turn" · "safety ceiling" · "tool description is the most
important factor" · "isError / is_error" · "tool_choice" · "input_schema" ·
"allowedTools" · "specialized agents" · "~4–5 tools per agent" · "god agent."

## Sources
- Building effective agents — https://www.anthropic.com/engineering/building-effective-agents
- Tool use (define tools, best practices, errors) — https://docs.claude.com/en/docs/agents-and-tools/tool-use/implement-tool-use · https://docs.claude.com/en/docs/agents-and-tools/tool-use/handling-errors
