---
tags:
  - CCA-F
  - practice-exam
date: 2026-08-24
status: done
---

# CCAF Timed Mock 2026-08-24 — Questions

All 60 question stems from the CyberSkill timed mock taken **2026-08-24** (sitting `7a03a635`), in sitting order. **Answers are deliberately not marked here** — work through this file, then grade against [README.md](README.md) § Answer grid or the domain files in [Answer Key/](Answer%20Key/).

> [!WARNING] Lower fidelity than the other two sittings — options were not captured
> The site's review page renders only **your selected option and the correct one**; the two unchosen distractors per item are never sent to the browser, and no endpoint exposes them. So this file has **stems only, no A–D lists**.
>
> Practical effect: you cannot drill this set as multiple choice. Use it as **open-response** — read the stem, state the principle and the action you'd take, then compare against the keyed answer's full text. That is arguably the harder drill.
>
> For the multiple-choice version of ~⅔ of these items, use [../CyberSkill CCAF - New Mock Exam/Questions.md](../CyberSkill%20CCAF%20-%20New%20Mock%20Exam/Questions.md), which quotes all four options.

> [!NOTE] Currency symbols reconstructed
> Text extraction dropped `$` from the page. In Q16 and Q21 the amounts are written back as `$847`, `$500`, `$50B`, `$35B (±$7B)`. Nothing else was altered — stems are otherwise verbatim.

---

## Q1 · customer_support

A support agent order-status tool returns data that looks stale and contradicts what the customer sees. The agent should:

## Q2 · extraction_pipeline

Your extraction pipeline processes restaurant menus and must output structured JSON with fields for item names, descriptions, prices, and dietary tags. Some menus use inconsistent formatting—prices as "$12" vs "12.00", dietary info as icons vs text.

What's the most reliable approach?

## Q3 · customer_support

A customer asks a simple question that the agent can answer directly from the knowledge base. The agent should:

## Q4 · code_exploration

An agent must find why a specific error message is thrown in a large service. The most context-efficient first step is to:

## Q5 · extraction_pipeline

Your extraction system implements automatic retries when validation fails. On each retry, the specific validation error is appended to the prompt. This retry-with-error-feedback approach resolves most failures within 2-3 attempts.

For which failure pattern would additional retries be LEAST effective?

## Q6 · extraction_pipeline

Your pipeline uses a tool called `extract_metadata` with a JSON schema for paper details. You've also defined `lookup_citations` and `verify_doi` tools for enrichment. During testing, you notice that when users include requests like "extract the metadata and tell me how cited it is," Claude sometimes calls `lookup_citations` first, which fails because it needs the DOI that `extract_metadata` would provide.

What's the most effective way to ensure structured metadata extraction happens first?

## Q7 · extraction_pipeline

Your extraction system processes two document types: standard monthly reports (archived after processing) and urgent exception reports (must trigger business alerts within 30 minutes of receipt). Both use the same JSON schema. You want to minimize API costs while meeting latency requirements.

How should you architect the processing pipeline?

## Q8 · research_pipeline

The coordinator agent has `AgentDefinition`s configured for all four specialized subagents, each with appropriate descriptions, prompts, and tool restrictions. During testing, you notice the coordinator correctly reasons about when to delegate—it generates messages like "I'll ask the web search agent to find sources on this topic"—but no subagent execution ever occurs. The coordinator then proceeds as if the delegation happened and continues with incomplete information. Logs show no errors.

What is the most likely cause?

## Q9 · code_exploration

Your agent needs to insert a new helper function into the middle of a 150-line utility module, between two existing functions. The `Edit` tool fails because its `old_string` parameter cannot find unique text to match — the file has repetitive docstrings, variable names, and structural patterns.

What's the most reliable way to complete this insertion?

## Q10 · extraction_pipeline

An extractor must label each support ticket with one of five priority levels. To stop the model from inventing new labels, you should:

## Q11 · code_exploration

An engineer asks your agent to identify untested code paths in a legacy payment processing module spanning 45 files. After reading the first 8 source files, the agent's responses are becoming noticeably less accurate—it's forgetting previously discussed code patterns and hasn't yet located all test files or traced critical payment flows.

What's the most effective approach to complete this investigation?

## Q12 · extraction_pipeline

A contract is too long to fit in one context window, and you need fields from across the whole document. The dependable approach is to:

## Q13 · extraction_pipeline

Your extraction pipeline processes invoices and extracts line items, subtotals, tax amounts, and grand totals. During evaluation, you discover that in 18% of extractions, the sum of extracted line item amounts doesn't match the extracted grand total—sometimes due to OCR errors in the source document, sometimes due to extraction mistakes by the model. Downstream accounting systems reject records with mismatched totals.

What's the most effective approach to improve extraction reliability?

## Q14 · extraction_pipeline

After implementing tool use with strict schema definitions, JSON syntax errors are eliminated, but 5% of extractions still have valid JSON with empty arrays or null values for required fields like citations and methodology. Spot-checking reveals that source documents contain this information, but in varied formats—inline citations vs. bibliographies, methodology sections vs. details embedded in introductions.

What's the most effective way to address these failures?

## Q15 · research_pipeline

In production, final reports frequently contain claims without proper source attribution. Investigation shows that while the web search and document analysis agents correctly attach citations to their outputs, the synthesis agent loses track of which sources support which conclusions when combining findings.

What's the most effective architectural change?

## Q16 · customer_support

After investigating a billing dispute over 25+ turns, you've identified that duplicate charges occurred due to a payment gateway timeout triggering retry logic. The required refund ($847) exceeds your $500 authorization limit. You need to call `escalate_to_human`, and the human agent won't have access to your conversation transcript.

What context should you pass to enable effective resolution?

## Q17 · extraction_pipeline

An extractor pulls line items and an invoice total from a receipt. The strongest integrity check before accepting the output is to:

## Q18 · customer_support

When the agent calls `lookup_order` and receives order details showing the item was purchased 45 days ago, how does the agentic loop determine whether to call `process_refund` or `escalate_to_human` next?

## Q19 · customer_support

A user asks a support agent for specific legal advice about a contract dispute. The right behavior is to:

## Q20 · customer_support

Production logs reveal inconsistent error handling: when `lookup_order` fails, the agent sometimes retries 5+ times (wasteful when the order ID doesn't exist), sometimes escalates immediately (premature for temporary network issues), and sometimes asks users for clarification (inappropriate when the issue is a backend permission error). Investigation shows your MCP tool returns uniform error responses: `{"isError": true, "content": [{"type": "text", "text": "Operation failed"}]}`. The agent cannot distinguish between error types.

What's the most effective improvement?

## Q21 · research_pipeline

Production reviews reveal inconsistent handling of uncertainty in final reports. Sometimes conflicting subagent findings are synthesized into a single confident statement (losing nuance), while other times reports over-hedge with excessive qualifications (becoming unhelpful). When the web search agent returns "industry analysts estimate $50B market size (methodology varies)" and the document analysis agent returns "peer-reviewed study estimates $35B (±$7B, 95% CI)," the coordinator either picks one arbitrarily or produces vague statements like "the market may be $35B–$50B depending on factors."

What systematic approach best addresses this?

## Q22 · code_exploration

An engineer used `Claude Code` yesterday to investigate authentication flows in a legacy monolith, building up significant context over a 2-hour session. Today she wants to continue that specific investigation. She's worked on three other codebases since then and knows the session was named "auth-deep-dive".

How should she resume?

## Q23 · customer_support

A customer sends: "This is frustrating. I've explained my issue twice and nothing is being resolved. I want to talk to a real person NOW." The agent has not yet called any tools to investigate their account.

What should the agent do?

## Q24 · extraction_pipeline

A field the schema expects is simply not present in the source document. The extractor should:

## Q25 · extraction_pipeline

Your system extracts event metadata (date, location, organizer, `attendee_count`) from news articles using a JSON schema with all nullable fields. During evaluation, you observe the model frequently generates plausible but incorrect values for fields not mentioned in the article—for example, outputting "500" for `attendee_count` when the source contains no attendance information.

What's the most effective way to reduce these false extractions?

## Q26 · customer_support

The agent verifies customer identity through a multi-step process before resetting passwords. During testing, you notice that after the customer answers the third verification question, the agent asks them to provide their name again, as if the earlier exchange never happened.

What's the most likely cause of this behavior?

## Q27 · extraction_pipeline

Documents arrive continuously throughout business hours and need structured data extracted. To reduce costs, you want to use the `Message Batches API` (50% discount, up-to-24-hour processing window). Your SLA specifies that extraction results must be available within 30 hours of document arrival with 99.9% reliability.

Which batching strategy is most appropriate?

## Q28 · code_exploration

A single source file is thousands of lines long and the agent needs one function from it. The agent should:

## Q29 · code_exploration

A `README` says the auth check happens in one module, but the agent must be sure before changing it. The agent should:

## Q30 · research_pipeline

Two sub-agents return conflicting figures for the same metric, each with moderate confidence. Before the coordinator writes the final answer, the best move is to:

## Q31 · extraction_pipeline

Your extraction system parses e-commerce product descriptions to extract specifications like dimensions, weight, and materials into JSON. Despite having a well-defined schema, the model inconsistently extracts the "materials" field—sometimes returning "cotton blend", other times "Cotton/Polyester mix", and occasionally omitting the field when material information is clearly present in the source.

What's the most effective way to improve extraction consistency?

## Q32 · research_pipeline

The web search agent has gathered several relevant sources for a research topic. The document analysis agent now needs to examine these sources.

How does information typically flow between these two specialized subagents?

## Q33 · customer_support

Your agent has called `lookup_order` multiple times while investigating a customer's return requests. Each response includes 40+ fields (items, shipping details, payment info, status history). Tool outputs now represent the majority of the conversation's context. The customer mentions two more orders they want to discuss.

What's the most effective approach before making additional lookups?

## Q34 · customer_support

A customer returns 4 hours after their initial session about the same billing dispute. The previous 32-turn session contains `lookup_order` results showing "Status: PENDING, Expected resolution: 24-48 hours." In testing, you observe that when resuming sessions with stale tool results, the agent often references the outdated data in responses (e.g., "I see your refund is still being processed") even after subsequent fresh tool calls return different information.

What approach most reliably handles returning customers?

## Q35 · research_pipeline

A research agent keeps spawning follow-up searches and the run is not converging. The most reliable way to prevent an endless loop is to:

## Q36 · customer_support

An agent has tried three times to resolve a billing issue and the customer is still stuck. The right next step is to:

## Q37 · code_exploration

An engineer used the agent yesterday to analyze a legacy authentication module, identifying two distinct refactoring approaches: extracting a microservice versus refactoring in-place. Today, they want to explore both approaches in depth—having the agent propose specific code changes for each—before deciding which to implement.

What's the most effective way to structure this exploration?

## Q38 · customer_support

You're implementing the escalation logic for when the agent should call `escalate_to_human`. Your team proposes four different approaches for triggering escalation.

Which approach will most reliably identify cases that genuinely require human intervention?

## Q39 · code_exploration

Your codebase exploration tool stores session IDs to allow engineers to continue investigations across work sessions. An engineer spent an hour yesterday analyzing a legacy authentication module, building context about its architecture and dependencies. They want to continue today. The session ID is valid, but version control shows 3 of the 12 files the agent previously read were modified overnight by a teammate's merge.

What approach best balances efficiency and accuracy?

## Q40 · code_exploration

Your agent has spent 25 minutes exploring a game engine's rendering subsystem—reading shader code, buffer management, and frame synchronization logic. An engineer now asks it to understand how the physics engine integrates with rendering for collision debug overlays. You notice recent responses reference "typical rendering patterns" rather than the specific `VulkanPipeline` and `FrameGraph` classes it discovered earlier.

What's the most effective approach?

## Q41 · customer_support

A customer raises three separate issues during one session: a refund inquiry (turns 1-15), a subscription question (turns 16-30), and a payment method update (turns 31-45). At turn 48, the customer asks "What happened with my refund?" The conversation is approaching context limits.

What strategy best maintains the agent's ability to address all issues throughout the session?

## Q42 · extraction_pipeline

After deployment, you find that 12% of extractions contain semantic errors that pass JSON schema validation (e.g., a duration like "30 minutes" incorrectly placed in an ingredient quantity field). Human reviewers have capacity to check only 20% of extractions.

Which approach most effectively allocates reviewer attention?

## Q43 · code_exploration

Before renaming a widely used function, an agent needs to know what a change would break. The right move is to:

## Q44 · research_pipeline

A research agent must gather facts from eight independent web sources and produce one synthesis. None of the sources depend on each other. Which dispatch pattern stays fast without flooding the coordinator context?

## Q45 · code_exploration

An engineer's exploration subagent spent 30 minutes analyzing a legacy payment system, reading 47 files and documenting data flows. The session was interrupted when the engineer's connection dropped. While away, a teammate merged a PR that renamed two utility functions. The engineer wants to continue the same exploration.

What's the most effective approach?

## Q46 · research_pipeline

The document analysis agent has a single `analyze_document` tool that takes a document and a free-text instruction parameter. During evaluation, requests like "extract the key financial metrics" often return narrative summaries, while "summarize the methodology" sometimes returns raw data tables. The synthesis agent reports that 35% of analysis results require re-requests with clarified instructions.

What's the most effective way to improve reliability?

## Q47 · research_pipeline

When researching "renewable energy adoption," the web search agent returns recent statistics (2024: 35% adoption) while the document analysis agent extracts data from internal reports (2022: 18% adoption). The synthesis agent incorrectly flags these as contradictory sources rather than recognizing the data shows growth over time.

What change would best enable the synthesis agent to correctly interpret such temporal differences?

## Q48 · research_pipeline

Three sub-agents searched overlapping territory and several findings repeat across their reports. Before synthesis, the coordinator should:

## Q49 · research_pipeline

The synthesis agent receives summarized findings from the web search and document analysis agents, then passes a consolidated summary to the report generator. During testing, you discover the generated reports make factual claims without proper citations—the report generator cannot attribute statements to their original sources because that metadata was lost during the summarization steps.

What's the most effective approach to ensure proper source attribution in the final reports?

## Q50 · research_pipeline

The coordinator provides detailed step-by-step instructions to the web search subagent, specifying exact search queries, source priorities, and date filters. Production monitoring reveals three issues: (1) the subagent reports "insufficient results" rather than trying alternative approaches when pre-specified searches fail, (2) research quality drops for emerging topics that don't match expected patterns, and (3) the subagent rarely surfaces valuable tangential sources.

What's the most effective way to improve subagent adaptability?

## Q51 · research_pipeline

Your multi-agent research pipeline crashed after processing 12 of 28 documents. The web search agent had identified relevant sources, the document analysis agent had partially completed extraction, and the synthesizer had begun pattern identification. You need to resume processing without repeating work or losing fidelity of prior findings.

What state management approach best balances information fidelity with context efficiency when restoring agent state?

## Q52 · code_exploration

An engineer asks the agent to understand how the caching layer works before adding a new cache invalidation trigger. After initial `Grep` searches, the agent has identified that caching logic spans 15 files including decorators, middleware, and service classes (~8,000 lines total).

What's the most effective next step for building understanding while managing context constraints?

## Q53 · research_pipeline

You are designing how sub-agents report findings so the final research output can be audited later. Each finding should travel with:

## Q54 · research_pipeline

A user is expanding the research system beyond its single web search agent by adding specialized data sources. They add a financial API agent that returns structured JSON with revenue, margins, and growth rates; a news monitoring agent that returns prose summaries of recent developments; and a patent analysis agent that returns structured lists of technology areas. The synthesis agent combines these into executive briefings. Currently, it converts everything to bullet points, causing financial comparisons to lose tabular clarity and news summaries to lose narrative flow.

What change would most improve briefing quality?

## Q55 · code_exploration

An engineer who just joined the team asks the agent to help them understand the authentication and authorization architecture before making security improvements. The codebase has 800+ files across multiple services.

What exploration strategy will most effectively build understanding, given Claude built-in tools and context limits?

## Q56 · code_exploration

A developer asks the agent to investigate why a specific API endpoint intermittently returns 500 errors. The codebase has 200+ files and the developer doesn't know which components are involved. The agent must trace the error through routing, middleware, business logic, and database layers.

What task decomposition approach would be most effective?

## Q57 · code_exploration

An agent is dropped into an unfamiliar repository and asked to add a feature. The best way to orient without burning context is to:

## Q58 · code_exploration

During testing, you observe that in extended exploration sessions (30+ minutes), the agent starts giving inconsistent answers about code structure it discussed earlier. Engineers report having to repeat context about modules they've already explored.

What's the most effective approach to address this?

## Q59 · extraction_pipeline

Your extraction uses tool use with a JSON schema where `property_type` is defined as an enum: `['house', 'apartment', 'condo', 'townhouse']`. After deployment, 8% of extractions fail schema validation. Investigation reveals listings mention many uncommon property types—"studio", "loft", "duplex", "mobile home", "tiny house", "converted warehouse"—and new types continue appearing regularly.

What's the most effective long-term solution?

## Q60 · customer_support

When implementing your `lookup_order` MCP tool, the backend sometimes returns errors (e.g., "Order not found" or temporary database failures).

What is the correct pattern for communicating these errors back to the agent?

---

**Back to:** [README.md](README.md) · [[Weak Areas Deep Dive]] · [[Answer Patterns Index]]
