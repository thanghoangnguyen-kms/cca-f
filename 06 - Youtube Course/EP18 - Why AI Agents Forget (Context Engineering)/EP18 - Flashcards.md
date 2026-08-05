---
tags:
  - CCA-F
  - domain-5
  - context-management
  - escalation
  - reliability
  - flashcards
  - youtube-course
date: 2026-08-05
status: done
domain: "5 of 5"
source: "Peace Of Code — Claude Certified Architect Ep 18"
---

# 🃏 EP18 Flashcards — Why AI Agents Forget (Context Engineering)

> [!NOTE] How to Use This Deck
> Active-recall cards drawn from [[EP18 - Why AI Agents Forget (Context Engineering)]]. Cover the `A:` line and answer before revealing. This deck is **self-contained** — it covers the episode in full, so some cards overlap with the vault-wide [[Flashcards]] deck by design. Study either on its own.
>
> **Related:** [[D5 - Context Management & Reliability]] · [[D1 - Agentic Architecture & Orchestration]] · [[EP05 - PreToolUse, PostToolUse Hooks & Task Decomposition]] · [[EP17 - Flashcards]] · [[Critical Terms Glossary]] · [[CCA-F Study Roadmap]]

---

## Domain 5 — The Context Window and Statelessness

**Q: An agent that passed testing quotes a customer the wrong refund amount three weeks into production. The refund math is verifiably correct. What happened?**
A: The amount **fell out of the context window** — the detail was established ~20 messages earlier and is no longer in the input being sent. Not a calculation error; a memory failure that raises no exception.

**Q: Name the four things that occupy the context window.**
A: The **system prompt**, **every message** in the conversation, **every tool call**, and **every tool result**. All four draw on the same token budget.

**Q: Why does a "forgetting" agent return a confident wrong answer rather than an error?**
A: Because nothing failed. Content outside the window simply is not in the input, so the model reasons normally over what remains. There is no absence for it to detect — hence the blue-pen analogy: eventually you forget the pen existed.

**Q: A colleague says "upgrade to the Max plan so the agent has a bigger context window." What is wrong with that?**
A: The window is a property of the **model** (200K on Haiku 4.5, 1M on current Opus/Sonnet), not of a subscription. Plans only gate *access to Claude Code's 1M extended-context option* — Max/Team/Enterprise include it for Opus, Pro needs usage credits, and **API callers have full access**.

**Q: What does it mean that the Claude Messages API is stateless, and what is the architectural consequence?**
A: There is **no server-side memory and no persistent sessions** — you re-send the full conversation on every call. Consequence: **context management is architecturally required**. If a fact is not in the array you send, the model cannot know it.

**Q: Two mechanisms both reduce context server-side. One clears, one summarizes. Name each and its strategy type.**
A: **Context editing** (`clear_tool_uses_20250919`) clears the oldest **tool results**, replacing each with placeholder text and keeping the conversation structure. **Compaction** (`compact_20260112`) summarizes earlier history and replaces it with the summary. Your client keeps the full unmodified history either way.

**Q: Which tool gives Claude state that survives the end of a session entirely?**
A: The **memory tool** (`memory_20250818`) — client-executed; Claude reads and writes files that persist across sessions. Context editing and compaction operate only *within* a conversation.

---

## Domain 5 — Progressive Summarization

**Q: Summarizing history to fit the window drops a $149 refund amount and an order date. Is that an efficiency problem or a reliability problem, and why does the distinction matter?**
A: A **reliability failure mode**. It matters because it is the exam's framing and reverses the intuitive read: you did not merely spend fewer tokens, you made the agent capable of being wrong. Token economy is secondary to keeping the facts.

**Q: Which four categories of information does progressive summarization destroy first?**
A: **Numerical values** (amounts, percentages) · **specific dates and timestamps** · **identifiers** (customer ID, order ID, reference numbers) · **policy-critical thresholds** (the $500 ceiling, the 30-day window). The vault adds a fifth: **customer-stated expectations**.

**Q: Why is a plausible-sounding summary more dangerous than an obviously truncated one?**
A: Because it is **confidently incomplete**. "Customer reported delivery and product issues requesting a refund" reads as sufficient context, so nothing triggers a re-lookup — yet the order number needed to process the refund and the exact amount owed are already gone.

**Q: `/compact` is fine in a Claude Code session but risky in a production support agent. What is the difference?**
A: Coding work is **qualitative** — a generalized summary of what was written and what to change next is enough. A transactional agent needs to know *which* customer, *which* order, *which* amount, and a generalized summary is exactly where those specifics disappear.

---

## Domain 5 — The Attention U-Curve

**Q: State the attention U-curve.**
A: LLMs attend most strongly to the **beginning** and the **end** of a long input, and least to the **middle** — the "lost in the middle" effect.

**Q: You place the customer ID and refund amount in the middle of a long prompt. What is the predicted failure, and is it a hard failure?**
A: The facts get **buried** — deprioritized or missed. It is a soft failure: the model may still find them but spends effort hunting, and may simply not process them. Nothing errors, which is what makes it hard to catch.

**Q: The middle of the prompt is the low-attention zone. Should you leave it empty?**
A: **No** — it is a resource, not a hazard. Park **trimmed tool results** and other low-stakes content there deliberately. You are not deleting data, you are arranging it so attention lands where the stakes are.

**Q: Name the three official mitigations for the lost-in-the-middle effect.**
A: (1) Place **key findings at the beginning** of aggregated input. (2) Use **explicit section headers** to organize detailed results. (3) Put **long documents at the top, the query at the bottom** — up to a **30% quality improvement**.

**Q: Facts-first placement helps attention. Why does it also help cost — and what is the one caveat?**
A: Prompt caching is a **prefix match**, so stable content early maximizes cache hits. Caveat: an *always-fresh* case-facts block changes every turn, so putting it ahead of the system prompt would invalidate the cached prefix. Keep the frozen system prompt as the cached prefix and inject the changing facts **after** it, at the top of the message history.

---

## Domain 5 — Tool Results and Trimming

**Q: What is the "silent context eater," and why is it silent?**
A: **Tool results.** Silent because each individual result is reasonable and appending it feels correct — the cost only shows up as an aggregate across many loop iterations, with no single call looking wrong.

**Q: Four tools return ~2,500 tokens of results per iteration over a 20-iteration loop. Why is "50,000 tokens" an underestimate?**
A: Because the API is **stateless**: iteration *k* re-sends the results of iterations 1…*k*, so the cost is **quadratic**, not linear — $T \cdot N(N+1)/2 = 2500 \times 210 = 525{,}000$ processed input tokens, over 10× the linear figure.

**Q: Which hook can trim a verbose tool result, and which field replaces the output the model sees?**
A: **`PostToolUse`**, using **`updatedToolOutput`** to *replace* the result (`additionalContext` *appends* instead). Return `{}` to pass through unchanged.

**Q: Why can a `PreToolUse` hook not filter a tool's output, and what is it actually for?**
A: It fires **before the tool executes** — the result does not exist yet. It receives `tool_name` and `tool_input`, and can rewrite the **input** via `updatedInput` or gate the call via `permissionDecision` (`"allow"` / `"deny"` / `"ask"` / `"defer"`). Use it to sanitize or validate what goes *into* a tool.

**Q: Complete the exam rule: trim verbose tool outputs ______ — because cleaning up afterwards is ______.**
A: Trim them **before they accumulate, at the hook layer** — because cleaning up an already-bloated context is a **failed strategy**.

**Q: Both a `PostToolUse` hook and a later compaction pass reduce tokens. Why is only one of them a design?**
A: The hook is **architectural** — it runs on every tool result forever, so bloat never enters. Compaction is **remedial**: by then you paid the tokens, paid them again on every stateless re-send, and the summarizer must now choose between your facts and accumulated noise.

**Q: When exactly does `PostToolUse` fire, and why does that timing make output replacement possible?**
A: After the tool **succeeds** but before the **next model call**. It is "post" relative to the *tool*, not the *model* — which is precisely why it can rewrite what lands in the context before Claude ever sees it.

---

## Domain 5 — The Case Facts Block

**Q: Name the six fields of the lecture's case-facts model.**
A: `customer_id` · `order_id` · `item` · `refund_amount` · `order_date` · `policy_window_days`.

**Q: State the four rules governing a case-facts block.**
A: **Top placement** (highest-attention zone) · **always fresh** (dynamically updated as the conversation grows) · **verified only** (confirmed tool data) · **compact** (~200 tokens).

**Q: Why must the case-facts block contain only verified tool data rather than model inference?**
A: Because anything in the block is treated as ground truth in every subsequent prompt. Admitting an inferred value would **promote a hallucination to a fact** and then re-assert it on every turn.

**Q: The block is capped at roughly 200 tokens. Why is a cap needed at all when the window holds hundreds of thousands?**
A: Because it rides in **every** prompt. Under statelessness that cost is re-paid on every call, so bloat here is multiplied by the length of the entire conversation.

**Q: One structural choice makes the case-facts block a remedy for two separate failure modes. What is it?**
A: It lives **outside summarized history, at the top of the prompt**. Being exempt from summarization defeats summarization loss; being at the top defeats lost-in-the-middle.

**Q: What populates the case-facts block, and does it need a model call?**
A: A **`PostToolUse` hook** extracts the fields from the tool's return value — **no model call needed**; a regex is sufficient. Cheap, deterministic, and it runs on every tool result.

**Q: In the rendered case-facts pattern, what does the `---` divider separate?**
A: The **verified facts above** (never summarized) from the **conversation summary below** (may be condensed). The divider is the structural expression of "outside summarized history."

---

## Domain 5 — Prompt Structure

**Q: Name the three prompt zones and what belongs in each.**
A: **Start** — the case-facts block. **Middle** — trimmed tool results, lean and relevant to the immediate decision only. **End** — critical recap (policy thresholds) plus the final question.

**Q: Why does the recap go at the *end* rather than next to the facts at the start?**
A: Because both ends of the U-curve are high-attention, and the closing zone is where the model's ask and its constraints should meet. Facts frame the case at the top; the recap plus the question close the loop at the bottom.

---

## Domain 5 — Escalation

**Q: Name the three valid escalation triggers.**
A: **Explicit human request** · **policy gap or exception** · **agent stuck** (genuine inability to progress after reasonable, exhausted attempts).

**Q: Name the three invalid escalation triggers — the exam traps.**
A: **Negative sentiment alone** · **task complexity alone** · **model confidence scores**.

**Q: State the golden rule of escalation and the two actions it forbids.**
A: When the customer explicitly asks for a human, **escalate immediately**. Forbidden: **attempting to resolve first**, and **asking why they want a human**.

**Q: A customer writes in all caps with three exclamation marks. The request is inside policy and under the authorization ceiling. Escalate or resolve?**
A: **Resolve.** Sentiment alone is an invalid trigger — frustrated tone is not case complexity. It only becomes trigger 1 if they explicitly state they want a person.

**Q: A request is genuinely complicated — many tool calls, several conditions — but every condition matches and every tool resolves. Escalate or resolve, and what would change the answer?**
A: **Resolve** — complexity alone is invalid. Two things change it: the agent actually **getting stuck** mid-attempt (trigger 3), or an **explicit request** for a human (trigger 1).

**Q: Why is self-reported model confidence unusable as an escalation gate?**
A: Because it is **not calibrated to actual accuracy** — the model can report 95% and be wrong, or 60% and be right. The gate therefore misfires in both directions. Routing thresholds, if used at all, must be calibrated against **labeled validation sets**, which is a review-workflow concern, not an escalation one.

**Q: A case combines a warranty claim and a shipping-damage claim; each policy covers its own scenario but none covers the combination. Which trigger, and why is it not "complexity"?**
A: **Trigger 2 — policy gap.** It escalates for missing **authorization**, not for difficulty: no policy grants the agent authority over the combined case, so a human must make the call.

**Q: A $750 refund reaches an agent with a $500 authorization ceiling. Which trigger fires, and what must happen before the handoff?**
A: **Policy gap** (trigger 2), surfacing as an authority limit. Before handing off the agent must still **verify the customer and the order** — "it cannot just hand over to a human without verifying the customer."

**Q: What must accompany an escalation, and why?**
A: A **structured handoff summary** — verified customer ID, root cause, amount at stake, what was attempted, recommended action — because the human receiving the case **usually cannot see the conversation transcript**.

**Q: The two halves of this episode — context management and escalation — are presented as one topic. Why?**
A: Because **"an agent that can't keep its memory straight can't make good escalation decisions."** Classifying a policy gap requires the order amount, date, and applicable threshold — exactly what summarization destroys. Context management is a **precondition** for correct escalation.

**Q: Sarah's damage and late delivery are both confirmed. She is highly frustrated and says "I just want to speak to an actual person now." What should the agent do, and what makes the confirmed details a distractor?**
A: **Transfer to a human immediately, without further resolution attempts.** The confirmed details prove the case *is* resolvable — which is precisely what stops mattering once the request is explicit. Resolvability is never a reason to override the golden rule.

**Q: Where in a tool definition does escalation policy actually get encoded for the model to act on?**
A: In the **tool description** — e.g. "Call this immediately when the customer explicitly asks to speak to a human, to a manager, or to a real person… Also call this when a request exceeds your authority." The description is what the model reads when deciding whether to route there.

---

*Back to: [[EP18 - Why AI Agents Forget (Context Engineering)]]*
