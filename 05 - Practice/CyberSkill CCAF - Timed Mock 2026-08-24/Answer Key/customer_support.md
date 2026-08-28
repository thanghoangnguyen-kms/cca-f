---
tags:
  - CCA-F
  - practice-exam
  - answer-key
  - customer-support
date: 2026-08-24
status: done
---

# Timed Mock 2026-08-24 — `customer_support` Answer Key

**14 answers** — Q1, 3, 16, 18, 19, 20, 23, 26, 33, 34, 36, 38, 41, 60. Scored **8/14 (57%)** — the weakest domain of this sitting.

Question numbers are this sitting's own and interleave with the other domains; they match [../Questions.md](../Questions.md). Back to [../README.md](../README.md). Every entry now reproduces its stem verbatim from [../Questions.md](../Questions.md) above the answer, so you can read this file without switching.

> [!NOTE] Scope and provenance
> Every answer here is the site's own `correct_key` as rendered on the review page, not a reasoned reconstruction — where an item was answered correctly, the correct answer *is* the recorded selection. **Distractors are not recoverable:** the page renders only your selection and the correct option, so this key says why the right answer wins but not why *each* wrong option fails. See [../README.md](../README.md) § Fidelity.

---

## ❌ Missed — work these first

### Q18 — How the loop picks the next tool

> When the agent calls `lookup_order` and receives order details showing the item was purchased 45 days ago, how does the agentic loop determine whether to call `process_refund` or `escalate_to_human` next?

- **Correct: C.** The order details are added to the conversation and the model reasons about which action to take.
- **You answered B.** "The agent follows a pre-configured decision tree mapping order attributes to specific tool calls."

**Why C wins.** The agentic loop has exactly one decision mechanism: the tool result is appended to the message list as a `tool_result` block, the whole conversation is resent, and the *model* chooses the next `tool_use`. There is no branching engine in the loop. A decision tree is orchestration you'd have to build *outside* the loop — which is a different architecture, and not what the question describes.

> [!WARNING] The trap you fell for
> "Pre-configured decision tree" sounds like the responsible, deterministic engineering answer, and D2/D5 items *do* often reward determinism. Distinguish the two families: deterministic **guardrails** (schemas, `isError` metadata, hooks, authorization limits) are rewarded; deterministic **replacements for model reasoning** are not. The loop reasons; guardrails constrain what the reasoning is allowed to do.

**Takeaway.** Tool result → context → model reasons → next tool call. Any answer that removes the model from the decision is describing a different system.

---

### Q19 — Out-of-scope request (legal advice)

> A user asks a support agent for specific legal advice about a contract dispute. The right behavior is to:

- **Correct: B.** Say plainly this is outside what support can advise on, and point the user to the right resource or a human.
- **You answered A.** "Give the best legal opinion the agent can produce."

**Why B wins.** Scope boundaries are a safety property, not a helpfulness trade-off. A support agent giving specific legal advice on a live dispute is acting outside its competence and its authorization, and a confident answer is worse than no answer. The correct behavior names the limit plainly and routes onward — it does not hedge, and it does not stonewall.

This is the easiest question in the sitting and a free mark. Any option that has the agent produce regulated professional advice — legal, medical, tax, immigration — is wrong on every CCA-F item, without exception. Refuse the *task*, keep helping the *person*: name the boundary, then hand off.

**Takeaway.** Out of scope → say so plainly + route to the right resource. Never "best effort" on regulated advice.

---

### Q23 — Angry customer demanding a human immediately

> A customer sends: "This is frustrating. I've explained my issue twice and nothing is being resolved. I want to talk to a real person NOW." The agent has not yet called any tools to investigate their account.
>
> What should the agent do?

- **Correct: A.** Acknowledge the frustration and ask one targeted question to understand the specific issue before escalating.
- **You answered D.** "First call `get_customer` and `lookup_order` to gather account context, then escalate to a human agent."

**Why A wins.** Nothing is known yet about *what* the issue is — so tool calls have no target. `lookup_order` needs an order; you don't have one. Gathering "account context" before you know the problem produces exactly the 40-field dumps that Q33 punishes, and it makes the customer wait while the agent works on the wrong thing. One targeted question costs a single turn and makes both the tool calls and the eventual handoff useful.

> [!WARNING] Two traps stacked in one item
> D is tempting because Q16 rewards escalating *with structured context* — so "gather context, then escalate" pattern-matches to a rule you actually know. The difference is **sequencing**: Q16 has 25 turns of investigation already done. Here you have zero. Context you haven't scoped isn't context, it's noise.
>
> The second trap is that A doesn't escalate at all, which feels like ignoring an explicit request for a human. It isn't — asking one question then escalating still honours it. Compare **Q38**: "customer requests a human" *is* a valid escalation trigger. Both are true. One clarifying question does not override the request; skipping straight to a blind handoff wastes the human's time too.

**Takeaway.** Acknowledge → one targeted question → *then* tools or escalation. Never call tools before you know what you're looking for.

---

### Q38 — Designing the escalation trigger

> You're implementing the escalation logic for when the agent should call `escalate_to_human`. Your team proposes four different approaches for triggering escalation.
>
> Which approach will most reliably identify cases that genuinely require human intervention?

- **Correct: A.** Instruct the agent to escalate when the customer requests a human, when the issue requires policy exceptions, or when the agent cannot make meaningful progress.
- **You answered B.** "Escalate after three consecutive tool calls that fail to resolve the customer's stated issue, ensuring a reasonable attempt before involving a human."

**Why A wins.** A names the three conditions that actually correlate with needing a human — explicit request, policy exception (i.e. beyond the agent's authority), and lack of progress — and lets the model judge them. B substitutes a proxy metric for the real condition: three failed tool calls is neither necessary (a policy exception needs a human on turn one, with zero failures) nor sufficient (three transient network errors need a retry, not a human).

> [!IMPORTANT] Same failure mode as your Q18 miss
> You chose the mechanical counter over model judgment twice in this sitting. The rule that resolves both: **judgment criteria beat counters** when the thing you're detecting is semantic. Counters are right only for genuinely mechanical limits — retry budgets, loop budgets (**Q35**), token ceilings. "Does this customer need a human?" is semantic. "Have I retried too many times?" is mechanical.

**Takeaway.** Escalate on: customer asks · policy exception / beyond authority · no meaningful progress. Not on a call counter.

---

### Q41 — Three issues, one session, near the context limit

> A customer raises three separate issues during one session: a refund inquiry (turns 1-15), a subscription question (turns 16-30), and a payment method update (turns 31-45). At turn 48, the customer asks "What happened with my refund?" The conversation is approaching context limits.
>
> What strategy best maintains the agent's ability to address all issues throughout the session?

- **Correct: C.** Summarize earlier turns into a narrative description, preserving full message history only for the active issue.
- **You answered A.** "Extract and persist structured issue data (order IDs, amounts, statuses) into a separate context layer."

**Why C wins.** The question is a **context-window management** question, and C is the standard compaction answer: compress the inactive spans to summaries, keep full fidelity where the conversation actually is. A builds a parallel storage mechanism — it saves the *facts* but discards the conversational thread (what was promised, what the customer already rejected, what tone the exchange has taken), which is what a support agent needs at turn 48. It also invents infrastructure the question never asked for.

> [!WARNING] "Structured data" is not always the right answer
> It is the right answer for **provenance and attribution** (Q15, Q49, Q53) and for **cross-agent handoff** (Q16, Q51), where the receiver is a machine or a stranger with no transcript. It is the *wrong* answer for **in-session context pressure**, where the fix is summarize-and-keep-active. Ask what's scarce: fidelity across a boundary → structure it; window space in one conversation → compact it.

**Takeaway.** In-session context pressure → summarize inactive spans, keep the active issue verbatim.

---

### Q60 — MCP tool error contract

> When implementing your `lookup_order` MCP tool, the backend sometimes returns errors (e.g., "Order not found" or temporary database failures).
>
> What is the correct pattern for communicating these errors back to the agent?

- **Correct: B.** Return the error message in the tool result content with the `isError` flag set to `true`.
- **You answered D.** "Return a success response with a `status` field indicating the error type."

**Why B wins.** MCP has a first-class error channel: `isError: true` on the tool result, with the human-readable cause in `content`. That flag is what tells the model an error occurred *as a protocol fact* rather than as text it has to interpret. Burying the failure in a `status` field of a success response means every caller must know to inspect that field, and the model has no signal that anything went wrong.

> [!IMPORTANT] Q20 and Q60 are the same topic, and you split them
> You got **Q20** right — the fix there was *enriching* the error with `errorCategory` / `isRetryable` metadata. So you know errors should carry structure. What you missed is that structure goes **alongside** `isError: true`, never **instead of** it. Q20's rich metadata sits inside an `isError` result. Read them as one rule:
>
> ❌ success response with a `status: "error"` field
> ✅ `isError: true` + message in `content` + structured metadata (`errorCategory`, `isRetryable`)

> [!IMPORTANT] Two layers, two spellings — the exam tests both
> **MCP tool result** → **`isError`** (camelCase), message in `content`. MCP also separates *protocol* errors (a JSON-RPC `error` object: unknown tool, bad arguments) from *tool execution* errors (`isError: true` in the result: API failures, bad input data, business-rule violations).
> **Messages API `tool_result` block** → **`is_error`** (snake_case), alongside `tool_use_id` and `content`.
>
> Same contract, different casing. Read the stem: "MCP tool" → `isError`; "`tool_result` block" → `is_error`. Q60 says "your `lookup_order` **MCP tool**", so `isError` is correct here.
> Sources: <https://modelcontextprotocol.io/specification/2025-06-18/server/tools> · <https://platform.claude.com/docs/en/agents-and-tools/tool-use/handle-tool-calls>

**Takeaway.** `isError: true` is the error channel for an MCP tool (`is_error` in a Messages API `tool_result`). Enrich it; don't replace it.

---

## ✅ Answered correctly

Recorded for completeness and re-drilling — the option shown is the keyed correct answer.

### Q1 — Stale tool data contradicting the customer

> A support agent order-status tool returns data that looks stale and contradicts what the customer sees. The agent should:

**B.** Tell the customer the system shows a possibly outdated status, and verify or escalate before committing to it.
*Takeaway: surface uncertainty rather than asserting stale data as fact. Pairs with Q34.*

### Q3 — Simple question answerable from the knowledge base

> A customer asks a simple question that the agent can answer directly from the knowledge base. The agent should:

**B.** Answer the question directly and clearly, and offer escalation only if the customer needs more.
*Takeaway: escalation is not a hedge. Don't route what you can resolve.*

### Q16 — Handing off a $847 refund beyond a $500 limit

> After investigating a billing dispute over 25+ turns, you've identified that duplicate charges occurred due to a payment gateway timeout triggering retry logic. The required refund ($847) exceeds your $500 authorization limit. You need to call `escalate_to_human`, and the human agent won't have access to your conversation transcript.
>
> What context should you pass to enable effective resolution?

**B.** A structured summary: customer ID, root cause, refund amount, and recommended action.
*Takeaway: the human has no transcript. Hand off structured conclusions, not raw history — and include the recommendation.*

### Q20 — Uniform error responses defeating error handling

> Production logs reveal inconsistent error handling: when `lookup_order` fails, the agent sometimes retries 5+ times (wasteful when the order ID doesn't exist), sometimes escalates immediately (premature for temporary network issues), and sometimes asks users for clarification (inappropriate when the issue is a backend permission error). Investigation shows your MCP tool returns uniform error responses: `{"isError": true, "content": [{"type": "text", "text": "Operation failed"}]}`. The agent cannot distinguish between error types.
>
> What's the most effective improvement?

**A.** Enhance error responses with structured metadata: `errorCategory` (transient/validation/permission), `isRetryable` boolean, and a description of what caused the failure.
*Takeaway: the agent can only branch on distinctions the tool actually reports. Read with **Q60**.*

### Q26 — Agent re-asks for a name mid-verification

> The agent verifies customer identity through a multi-step process before resetting passwords. During testing, you notice that after the customer answers the third verification question, the agent asks them to provide their name again, as if the earlier exchange never happened.
>
> What's the most likely cause of this behavior?

**C.** The conversation history isn't being passed in subsequent API requests.
*Takeaway: the API is stateless. Amnesia between turns is almost always the caller failing to resend full history — not a model or context-limit problem.*

### Q33 — Tool outputs dominating the context

> Your agent has called `lookup_order` multiple times while investigating a customer's return requests. Each response includes 40+ fields (items, shipping details, payment info, status history). Tool outputs now represent the majority of the conversation's context. The customer mentions two more orders they want to discuss.
>
> What's the most effective approach before making additional lookups?

**A.** Extract only return-relevant fields (items, purchase date, return window, status) from each existing order response, removing verbose details.
*Takeaway: prune tool results to the fields the task needs before adding more calls.*

### Q34 — Customer returns 4 hours later; stale tool results

> A customer returns 4 hours after their initial session about the same billing dispute. The previous 32-turn session contains `lookup_order` results showing "Status: PENDING, Expected resolution: 24-48 hours." In testing, you observe that when resuming sessions with stale tool results, the agent often references the outdated data in responses (e.g., "I see your refund is still being processed") even after subsequent fresh tool calls return different information.
>
> What approach most reliably handles returning customers?

**B.** Start a new session, inject a structured summary of the previous interaction (issue type, actions taken, resolution status), then make fresh tool calls before engaging.
*Takeaway: resuming a session resurrects stale tool results, which then compete with fresh ones. Fresh session + summary + re-query.*

### Q36 — Three failed attempts on a billing issue

> An agent has tried three times to resolve a billing issue and the customer is still stuck. The right next step is to:

**B.** Escalate to a human with the full history and what has been tried, so the customer does not start over.
*Takeaway: "no meaningful progress" is a genuine escalation trigger (**Q38**), and the handoff must carry what was already tried.*

---

## Domain pattern summary

| Rule | Items |
|---|---|
| The model reasons over tool results; the loop has no branching engine | Q18 |
| Judgment criteria beat counters for semantic conditions | Q18, Q38 |
| Escalate with structured context, never a bare handoff | Q16, Q36 |
| Don't act before you've scoped the problem | Q23 |
| `isError: true` + enriched metadata is the error contract | Q20, Q60 |
| Stateless API — resend full history | Q26 |
| Context pressure → prune tool results, compact inactive spans | Q33, Q41 |
| Stale data → flag it or re-query; never assert it | Q1, Q34 |
| Out-of-scope / regulated advice → name the limit, route on | Q19 |

**Related:** [[Weak Areas Deep Dive]] · [[Answer Patterns Index]] · [[D2 - Tool Design & MCP Integration]] · [[D5 - Context Management & Reliability]]
