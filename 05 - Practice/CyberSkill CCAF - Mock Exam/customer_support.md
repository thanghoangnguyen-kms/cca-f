---
tags:
  - CCA-F
  - practice-exam
  - answer-key
  - customer-support
date: 2026-08-24
status: done
---

with# customer_support — CCAF Study Guide

This guide covers the 15 `customer_support` questions (Q31–Q45) from the CCAF mock exam. Each entry restates the scenario, defends the keyed answer against Anthropic guidance, explains every distractor, and gives a portable takeaway.

> Note on sources: the `claude-api` skill named in the build instructions is not installed in this environment, so all citations point to official Anthropic / Claude documentation and engineering posts that were actually retrieved. Citation markers `[n]` map to URLs listed per question.

---

### Q31 — customer_support
**Question:** A customer returns 4 hours after a 32-turn billing-dispute session. The prior session contains a stale `lookup_order` result ("Status: PENDING, Expected resolution: 24–48 hours"). In testing, when resuming with stale tool results, the agent keeps citing the outdated data ("your refund is still being processed") even after fresh tool calls return different values. What most reliably handles returning customers?

**Correct answer: B** — Start a new session, inject a structured summary of the prior interaction, then make fresh tool calls before engaging.

**Why it's correct:** The Claude API is stateless: every request re-sends the entire `messages` array, so any stale `tool_result` you carry forward stays in context and competes for the model's attention against newer results [1][2]. A fresh session seeded with a compact, structured summary (issue type, actions taken, resolution status) preserves narrative continuity while guaranteeing no outdated `tool_result` blocks are present to bias reasoning. This mirrors Anthropic's compaction guidance: condense the durable signal and drop the noisy raw history [2][3].

**Why the others are wrong:**
- **A:** Deleting `tool_result` messages from the middle of a transcript leaves orphaned `tool_use` blocks referencing results that no longer exist, producing an internally inconsistent conversation that can error or confuse the model [1].
- **C:** A system-prompt instruction to "prefer the most recent tool result" is a soft suggestion; the prompt cannot guarantee behavior, and the scenario states this exact failure was already observed in testing.
- **D:** Auto-re-calling every previously used tool is wasteful and slow, and—critically—the stale results still sit in context alongside the new ones, so the confusion remains.

**Key takeaway:** Stale `tool_result` blocks poison reasoning; for returning users, prefer a clean session + structured summary + fresh fetches over editing the middle of a transcript.

**Sources:** [1] https://docs.claude.com/en/docs/about-claude/use-case-guides/customer-support-chat · [2] https://docs.claude.com/en/docs/build-with-claude/context-windows · [3] https://www.anthropic.com/engineering/claude-code-best-practices

---

### Q32 — customer_support
**Question:** You're designing escalation logic for `escalate_to_human`. Which trigger approach most reliably identifies cases that genuinely need a human?

**Correct answer: A** — Instruct the agent to escalate on explicit human requests, policy-exception needs, or when it cannot make meaningful progress.

**Why it's correct:** Anthropic recommends using LLM judgment for open-ended, hard-to-codify decisions and reserving deterministic code for narrow, well-specified rules [1][4]. "Does this customer need a human?" is an intent-and-progress judgment that spans a long tail of phrasings; clear natural-language criteria let the model generalize across that tail far better than brittle rules [4]. This is the "give the model clear criteria, not a rulebook" pattern.

**Why the others are wrong:**
- **B:** A fixed three-failed-calls counter fires too late on obvious first-turn policy issues and too early on legitimate retries; failure count is a poor proxy for "needs a human."
- **C:** Sentiment thresholds over-escalate on stylistic language (caps, exclamation marks) and miss calm customers who nonetheless require a policy exception or human authority.
- **D:** A rules engine mapping issue type × segment × product breaks on exactly the unanticipated cases that dominate support, removing the model judgment that handles them.

**Key takeaway:** Codify hard compliance limits in code, but route genuinely judgment-based decisions (like "needs a human") through clear natural-language criteria the model can reason over.

**Sources:** [1] https://www.anthropic.com/engineering/building-effective-agents · [4] https://docs.claude.com/en/docs/about-claude/use-case-guides/customer-support-chat

---

### Q33 — customer_support
**Question:** After a 25+ turn investigation you found duplicate charges from a gateway-timeout retry. The needed refund ($847) exceeds your $500 limit. You must call `escalate_to_human`, and the human won't see your transcript. What context should you pass?

**Correct answer: B** — A structured summary: customer ID, root cause, refund amount, and recommended action.

**Why it's correct:** Effective agent handoffs forward synthesized findings, not raw history—Anthropic's orchestrator patterns emphasize passing each stage a clear objective and the relevant distilled output rather than replaying everything [1][5]. A structured brief (identifiers + diagnosis + amount + recommendation) lets the human act immediately without re-running the 25-turn investigation, and the explicit customer ID/recommended action make it actionable.

**Why the others are wrong:**
- **A:** Verbatim complaint plus raw `tool_result` excerpts hands over artifacts without synthesis, forcing the human to redo the diagnosis you already completed.
- **C:** The full transcript buries the answer; the human must wade through 25 turns instead of reading a one-screen brief.
- **D:** Diagnosis + amount only omits the customer identifiers and the recommended action, so the human literally cannot locate or action the case.

**Key takeaway:** Escalation context should be a structured, self-contained brief (who / root cause / amount / recommended action)—synthesis over raw replay.

**Sources:** [1] https://www.anthropic.com/engineering/building-effective-agents · [5] https://www.anthropic.com/engineering/multi-agent-research-system

---

### Q34 — customer_support
**Question:** Compliance requires refunds over $500 to automatically escalate—"cannot be left to model discretion." Despite clear prompt instructions, the agent processes high-value refunds directly 3% of the time. How do you achieve guaranteed compliance?

**Correct answer: C** — Implement a hook that intercepts the tool call; when the refund amount exceeds $500, block it and invoke human escalation.

**Why it's correct:** Any prompt-based control is probabilistic—the model may still deviate. A deterministic guardrail outside the model (a hook/middleware on the tool invocation) fires every time, independent of the model's reasoning [1][6]. Anthropic guidance is to keep agents constrained by hard, code-level checks for safety/compliance-critical actions rather than relying on instructions alone [1]. The phrase "cannot be left to model discretion" rules out every prompt-only option.

**Why the others are wrong:**
- **A:** Returning a tool error still depends on the model interpreting that error and choosing to escalate—decision remains at the model layer.
- **B:** Few-shot examples shift the output distribution but cannot drive the failure rate to zero; 3% non-compliance persists.
- **D:** Emphatic/capitalized prompt language reduces but never eliminates misuse; still model discretion.

**Key takeaway:** Compliance-grade rules belong in deterministic code (hooks/interceptors), not prompts—prompts influence probability, code guarantees outcome.

**Sources:** [1] https://www.anthropic.com/engineering/building-effective-agents · [6] https://docs.claude.com/en/docs/test-and-evaluate/strengthen-guardrails/reduce-hallucinations

---

### Q35 — customer_support
**Question:** During a billing dispute, `get_customer` and `lookup_order` succeed but `process_refund` returns a timeout. The agent can explain charges and verify eligibility but can't process the refund. What best balances first-contact resolution with error handling?

**Correct answer: C** — Explain the billing, confirm eligibility, acknowledge the system issue blocking immediate processing, and offer escalation or retry-later.

**Why it's correct:** This is graceful degradation: deliver all the value you can (explanation + eligibility), be transparent about the failure, and preserve customer choice. The customer-support guide frames the agent's job as resolving what it can and handing off cleanly when it cannot, rather than stalling or over-promising [4]. A timeout is transient, so honesty plus options beats both silent retry loops and premature escalation.

**Why the others are wrong:**
- **A:** Escalating immediately punts a transient backend blip to a human when the agent can still deliver explanation, eligibility, and options.
- **B:** Open-ended exponential-backoff retries trap the customer waiting on an outage that may never clear—poor UX.
- **D:** Confirming a refund that didn't actually process misleads the customer—worse than the original timeout.

**Key takeaway:** On partial failure, degrade gracefully—deliver the value you can, be honest about what failed, and let the customer choose the next step.

**Sources:** [4] https://docs.claude.com/en/docs/about-claude/use-case-guides/customer-support-chat

---

### Q36 — customer_support
**Question:** A customer says they've been going "back and forth for days" and "just want to speak to someone who can actually help." `lookup_order` confirms the return is within policy and immediately processable. What should the agent do?

**Correct answer: A** — Acknowledge the frustration, tell them it's resolvable now, and offer to complete it or escalate.

**Why it's correct:** The agent already has the dispositive fact (resolvable now), so the best move honors the emotion, surfaces the fast path in writing, and preserves the customer's choice—the core customer-respect posture in Anthropic's support-agent guidance [4]. It leverages the agent's capability without overriding the customer's stated wish.

**Why the others are wrong:**
- **B:** Escalating immediately queues a customer behind a human handoff when resolution is one tool call away—adds delay to a simple case.
- **C:** Unilaterally processing the refund after the customer explicitly asked to speak to someone overrides their stated preference.
- **D:** Interrogating a frustrated customer about prior failures is the opposite of the empathy they requested.

**Key takeaway:** When you can resolve instantly, acknowledge the feeling, state the fast path, and keep the choice with the customer—don't queue and don't override.

**Sources:** [4] https://docs.claude.com/en/docs/about-claude/use-case-guides/customer-support-chat

---

### Q37 — customer_support
**Question:** During multi-step identity verification before a password reset, after the customer answers the third question the agent asks for their name again, as if earlier exchanges never happened. Most likely cause?

**Correct answer: C** — The conversation history isn't being passed in subsequent API requests.

**Why it's correct:** The Messages API is stateless—each request must include the full `messages` array, and the reference chatbot explicitly appends every user/assistant/`tool_result` turn before re-sending [1][2]. If only the latest turn is sent, the model has no record of earlier answers, producing exactly the "ask for the name again" symptom. Memory across turns is a function of what you re-send, not an internal model feature.

**Why the others are wrong:**
- **A:** Tools don't clear agent state; conversation state lives entirely in the `messages` array the caller maintains.
- **B:** A prompt instruction to "remember" cannot create memory—memory comes from passing prior turns back in.
- **D:** There is no two-turn default limit; history length is bounded only by the context window and is under your control.

**Key takeaway:** The API is stateless—if the agent "forgets," you almost certainly aren't re-sending the full conversation history each request.

**Sources:** [1] https://docs.claude.com/en/docs/about-claude/use-case-guides/customer-support-chat · [2] https://docs.claude.com/en/docs/build-with-claude/context-windows

---

### Q38 — customer_support
**Question:** `lookup_order` failures are handled inconsistently—5+ retries on nonexistent IDs, premature escalation on transient network issues, inappropriate user clarification on backend permission errors. The MCP tool returns a uniform `{"isError": true, "content":[{"type":"text","text":"Operation failed"}]}`. Most effective improvement?

**Correct answer: A** — Return structured error metadata: `errorCategory` (transient/validation/permission), `isRetryable` boolean, and a description of the cause.

**Why it's correct:** The agent can only act on information it receives; a uniform "Operation failed" string gives it nothing to differentiate behavior, so it guesses. Adding category + retryability + cause turns guessing into deterministic policy ("retry only when `isRetryable`; escalate on permission") [4][7]. Tool results are how the agent reasons about next actions, so the fix belongs in the tool's response payload, not the prompt.

**Why the others are wrong:**
- **B:** A separate `analyze_error` tool adds a round-trip for information the original call already has—put the metadata in the original response.
- **C:** Blanket server-side retry-with-backoff for all errors wastes time on permanent failures (order not found) and hides useful distinctions from the agent.
- **D:** Few-shot examples can't extract category information that simply isn't present in "Operation failed."

**Key takeaway:** Give the model structured, machine-actionable error metadata (category + retryable + cause) instead of opaque strings—and put it in the tool's own response.

**Sources:** [4] https://docs.claude.com/en/docs/about-claude/use-case-guides/customer-support-chat · [7] https://docs.claude.com/en/docs/agents-and-tools/tool-use/handling-errors

---

### Q39 — customer_support
**Question:** When the agent calls `lookup_order` and learns the item was purchased 45 days ago, how does the agentic loop decide whether to call `process_refund` or `escalate_to_human` next?

**Correct answer: C** — The order details are added to the conversation and the model reasons about which action to take.

**Why it's correct:** The agentic loop works by executing the tool, appending its result as a `tool_result` block to the `messages` array, and re-invoking the model, which then decides the next step on each turn [1][2]. There is no external router or committed plan—tool selection is model-driven based on the accumulated context, so the 45-day fact flows in and the model chooses refund vs. escalate [1].

**Why the others are wrong:**
- **A:** There is no implicit orchestration layer picking tools from a status field; the model drives selection.
- **B:** Agentic loops are not decision-tree-driven; a hardcoded tree is the opposite of the agent pattern's purpose.
- **D:** No upfront committed plan exists—each next step is chosen from the latest context, not pre-sequenced.

**Key takeaway:** The agentic loop = append `tool_result` to the messages, re-run the model, let it choose the next action. Tool selection is model-driven, per turn.

**Sources:** [1] https://docs.claude.com/en/docs/about-claude/use-case-guides/customer-support-chat · [2] https://docs.claude.com/en/docs/agents-and-tools/tool-use/implement-tool-use

---

### Q40 — customer_support
**Question:** A customer writes: "This is frustrating. I've explained my issue twice and nothing is resolved. I want to talk to a real person NOW." The agent has not yet called any tools. What should it do?

**Correct answer: A** — Acknowledge the frustration and ask one targeted question to understand the specific issue before escalating.

**Why it's correct:** The agent has zero account context yet, so a single empathetic, focused question gives a genuine shot at first-contact resolution without dismissing the customer or stalling a possible handoff [4]. This balances customer respect with the agent's resolution capability—the recommended support posture when investigation hasn't started.

**Why the others are wrong:**
- **B:** Listing capabilities at an already-frustrated customer who asked for a human reads as dismissive.
- **C:** Escalating with zero tool context produces a cold handoff where the human also starts from scratch.
- **D:** Silently running `get_customer`/`lookup_order` first adds latency and ignores the customer's request; one acknowledging question is faster and more respectful.

**Key takeaway:** With no context yet, one empathetic, targeted question beats both immediate escalation and silent investigation—respect the feeling while attempting fast resolution.

**Sources:** [4] https://docs.claude.com/en/docs/about-claude/use-case-guides/customer-support-chat

---

### Q41 — customer_support
**Question:** Mid-dispute, after `get_customer` and `lookup_order`, the agent finds a promotional-pricing error requiring manager approval—beyond its authorization. How should the workflow handle this mid-process escalation?

**Correct answer: B** — Compile a structured handoff (customer details, order info, identified issue) before calling `escalate_to_human`.

**Why it's correct:** Same handoff principle as Q33: forward synthesized, identifier-rich context so the human can act immediately, consistent with Anthropic's orchestration guidance on passing distilled outputs between stages [1][5]. The brief captures who, which order, what issue, and why it exceeds authorization.

**Why the others are wrong:**
- **A:** Passing only the original message drops the tool-derived context just gathered, forcing the human to re-investigate.
- **C:** Attempting the refund despite knowing it exceeds authorization is a deliberate policy violation, not a safe "let the system catch it" move.
- **D:** Persisting the full history to a DB and passing a reference ID adds infrastructure and an extra lookup when an inline structured brief is simpler and faster.

**Key takeaway:** Mid-process escalation = inline structured brief (customer + order + issue + why escalating), not a raw dump and not a policy-violating attempt.

**Sources:** [1] https://www.anthropic.com/engineering/building-effective-agents · [5] https://www.anthropic.com/engineering/multi-agent-research-system

---

### Q42 — customer_support
**Question:** One session covers three issues—refund (turns 1–15), subscription (16–30), payment-method update (31–45). At turn 48 the customer asks "What happened with my refund?" and the conversation nears context limits. Best strategy to keep all issues addressable?

**Correct answer: C** — Summarize earlier turns into a narrative description, keeping full message history only for the active issue.

**Why it's correct:** Progressive/server-side compaction is Anthropic's primary strategy for long-running conversations: condense stable, resolved topics into a summary while preserving the active thread verbatim [2]. This keeps the refund details (turns 1–15) available in compressed form while staying under the context limit—the canonical multi-issue long-conversation pattern.

**Why the others are wrong:**
- **A:** A separate structured "context layer" adds engineering and doesn't preserve the conversational narrative the customer expects.
- **B:** On-demand re-fetching addresses data freshness, not the context-length problem, and loses what was actually said between customer and agent.
- **D:** A fixed 30-turn sliding window silently drops turns 1–15—exactly the refund the customer is asking about.

**Key takeaway:** For long multi-topic sessions, compact resolved threads into summaries and keep the active thread verbatim—don't blindly slide a fixed window.

**Sources:** [2] https://docs.claude.com/en/docs/build-with-claude/context-windows

---

### Q43 — customer_support
**Question:** When `lookup_order` hits backend errors ("Order not found" or transient DB failures), what is the correct pattern for communicating them back to the agent?

**Correct answer: B** — Return the error message in the tool result content with the `isError`/`is_error` flag set to true.

**Why it's correct:** This is the designed tool-use error pattern: put a readable error message in the `content` and set the error flag, so the model sees both that a failure occurred and a human-readable reason to reason over and recover from [7]. It keeps the agent in the loop with actionable information rather than hiding the failure.

**Why the others are wrong:**
- **A:** Returning an empty success makes the agent conclude "no data exists" rather than "something failed"—a worse, different confusion.
- **C:** Throwing an uncaught exception breaks the tool protocol and gives the model nothing to reason with.
- **D:** Ad-hoc `status` fields vary per tool and have no standard interpretation; the error flag is the standard mechanism.

**Key takeaway:** Surface tool errors via the error flag + a readable message in the content block—never via empty results, exceptions, or non-standard status fields.

**Sources:** [7] https://docs.claude.com/en/docs/agents-and-tools/tool-use/handling-errors

---

### Q44 — customer_support
**Question:** `process_refund` returns transient technical errors (503, timeout; 5%) and permanent business errors ("exceeds 30-day window", "already refunded"; 12%). Both return only plain text, and the agent wastes 3–4 turns retrying business errors. Most effective fix that also improves customer-facing replies?

**Correct answer: A** — Return structured error responses with `retryable: false` for business errors plus a customer-friendly explanation for Claude to use.

**Why it's correct:** A `retryable` flag deterministically tells the model not to retry permanent failures (eliminating the wasted turns), while a ready-made customer-friendly message improves the outgoing reply—solving both problems in one response payload [4][7]. Like Q38, the cure is richer tool-result metadata, not prompt heuristics.

**Why the others are wrong:**
- **B:** Few-shot text-parsing of error strings is exactly the fragile behavior causing today's instability.
- **C:** A mandatory `check_refund_eligibility` precheck adds a round-trip to every refund to guard 12% of cases and still won't prevent other business failures at `process_refund` time.
- **D:** Tool-level auto-retry for technical errors hides latency and removes the model from recovery decisions, and business errors still arrive as a plain string—so the customer-facing response doesn't improve.

**Key takeaway:** Encode retryability and a customer-ready message in the structured error response—fix wasted retries and reply quality at the source, not with prompt parsing.

**Sources:** [4] https://docs.claude.com/en/docs/about-claude/use-case-guides/customer-support-chat · [7] https://docs.claude.com/en/docs/agents-and-tools/tool-use/handling-errors

---

### Q45 — customer_support
**Question:** The agent has called `lookup_order` several times; each response has 40+ fields and tool outputs now dominate the context. The customer mentions two more orders to discuss. Most effective approach before more lookups?

**Correct answer: A** — Extract only return-relevant fields (items, purchase date, return window, status) from each existing order response, removing verbose details.

**Why it's correct:** This is targeted context pruning—keep task-relevant fields, drop the rest—directly addressing context bloat before adding two more large responses. Anthropic's context-management guidance includes tool-result clearing/pruning as a strategy for keeping the window focused on what matters [2]. Structured pruning preserves exact values (dates, amounts) the model may still need, unlike paraphrase.

**Why the others are wrong:**
- **B:** Prose summaries paraphrase away precise fields (exact dates/amounts) the model may need later; structured pruning is more faithful.
- **C:** A vector DB with semantic retrieval is heavy infrastructure for what is fundamentally a pruning problem.
- **D:** Proceeding without pruning does nothing about the bloat and heads straight toward context exhaustion.

**Key takeaway:** When verbose tool outputs dominate context, prune to the task-relevant fields (structured, lossless) before issuing more calls—don't paraphrase and don't bolt on a vector store.

**Sources:** [2] https://docs.claude.com/en/docs/build-with-claude/context-windows

---

## Source index
- [1] Building effective agents — https://www.anthropic.com/engineering/building-effective-agents
- [2] Context windows (statelessness, compaction, context editing) — https://docs.claude.com/en/docs/build-with-claude/context-windows
- [3] Claude Code best practices (compaction/summarization) — https://www.anthropic.com/engineering/claude-code-best-practices
- [4] Customer support agent use-case guide — https://docs.claude.com/en/docs/about-claude/use-case-guides/customer-support-chat
- [5] How we built our multi-agent research system (delegation/handoff) — https://www.anthropic.com/engineering/multi-agent-research-system
- [6] Reduce hallucinations / strengthen guardrails — https://docs.claude.com/en/docs/test-and-evaluate/strengthen-guardrails/reduce-hallucinations
- [7] Tool use — handling errors (is_error pattern) — https://docs.claude.com/en/docs/agents-and-tools/tool-use/handling-errors
