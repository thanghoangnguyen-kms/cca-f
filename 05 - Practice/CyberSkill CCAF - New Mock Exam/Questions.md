---
tags:
  - CCA-F
  - practice-exam
date: 2026-08-23
status: not-started
---

# New Mock Exam — Questions

> [!NOTE] What this is
> A 60-question CCA-F sitting drawn from the [CyberSkill practice bank](https://practice.cyberskill.world/practice/ccaf/practice) on 2026-08-23 (sitting `7039f634`). Questions and **all** answer options are reproduced here; the correct answer is deliberately **not** marked. Work through this file first, then check the answer key — split by domain under [Answer Key/](README.md#files), indexed in [README.md](README.md).
>
> Domain mix: `code_exploration` 15 · `customer_support` 14 · `extraction_pipeline` 16 · `research_pipeline` 15. Each question's domain line links to the answer file that covers it — **don't click through until you've committed to an answer.**

> [!WARNING] Independently numbered
> `Q1`–`Q60` here are **this sitting's** numbering. They do **not** line up with the sibling `CyberSkill CCAF - Mock Exam/` sitting (same bank, 42/60 of these questions recur there under different numbers) or with the unrelated [[CCA-F-practice-exam-questions]] bank. Never carry a question number between sets.

---

## Question 1

_Domain: [`code_exploration`](Answer%20Key/code_exploration.md)_

Your agent has spent 25 minutes exploring a game engine's rendering subsystem—reading shader code, buffer management, and frame synchronization logic. An engineer now asks it to understand how the physics engine integrates with rendering for collision debug overlays. You notice recent responses reference "typical rendering patterns" rather than the specific VulkanPipeline and FrameGraph classes it discovered earlier.
What's the most effective approach?

- **A.** Spawn a sub-agent to explore physics independently, then manually synthesize its findings with the rendering knowledge accumulated in the main conversation.
- **B.** Continue in the current context with more targeted prompts referencing the specific classes by name.
- **C.** Summarize key rendering findings, then spawn a sub-agent for physics exploration with that summary in its initial context.
- **D.** Use /clear to reset context completely, then start fresh with physics exploration using file paths from the project's CLAUDE.md.

---

## Question 2

_Domain: [`customer_support`](Answer%20Key/customer_support.md)_

You're implementing the escalation logic for when the agent should call `escalate_to_human`. Your team proposes four different approaches for triggering escalation.
Which approach will most reliably identify cases that genuinely require human intervention?

- **A.** Instruct the agent to escalate when the customer requests a human, when the issue requires policy exceptions, or when the agent cannot make meaningful progress.
- **B.** Configure the agent to escalate after three consecutive tool calls that fail to resolve the customer's stated issue, ensuring a reasonable attempt before involving a human.
- **C.** Implement sentiment analysis that monitors for frustration indicators (negative language, repeated questions, exclamation marks) and trigger escalation when the frustration score exceeds a configured threshold.
- **D.** Build a rules engine that maps specific issue types, customer segments, and product categories to escalation decisions, removing the need for model judgment calls.

---

## Question 3

_Domain: [`extraction_pipeline`](Answer%20Key/extraction_pipeline.md)_

An extractor pulls line items and an invoice total from a receipt. The strongest integrity check before accepting the output is to:

- **A.** Trust the total field because it is printed prominently.
- **B.** Verify that the line items sum to the extracted total, and on a mismatch retry or flag the record.
- **C.** Check only that the total is a number.
- **D.** Accept the first extraction without checking.

---

## Question 4

_Domain: [`extraction_pipeline`](Answer%20Key/extraction_pipeline.md)_

After your daily batch of 10,000 documents completes, 300 documents (3%) failed with "`context_length_exceeded`" errors. The results file identifies each failure by `custom_id`.
What's the most cost-effective approach to process these failures?

- **A.** Reprocess the entire batch with prompt caching enabled to reduce the cost of retrying requests with identical system prompts
- **B.** Resubmit only the 300 failed documents after chunking them into smaller pieces, then combine the partial extractions
- **C.** Resubmit the entire 10,000 document batch using a model tier with a larger context window
- **D.** Increase the `max_tokens` parameter for the 300 failed documents and resubmit them in a new batch

---

## Question 5

_Domain: [`research_pipeline`](Answer%20Key/research_pipeline.md)_

Three sub-agents searched overlapping territory and several findings repeat across their reports. Before synthesis, the coordinator should:

- **A.** Concatenate all three reports verbatim into the final answer.
- **B.** Keep the first report and discard the other two unread.
- **C.** Merge the reports, collapse duplicate findings, and keep one cited instance of each.
- **D.** Ask the user to remove the duplicates.

---

## Question 6

_Domain: [`customer_support`](Answer%20Key/customer_support.md)_

When the agent calls `lookup_order` and receives order details showing the item was purchased 45 days ago, how does the agentic loop determine whether to call `process_refund` or `escalate_to_human` next?

- **A.** The orchestration layer automatically routes to the next tool based on the order's status field.
- **B.** The agent follows a pre-configured decision tree mapping order attributes to specific tool calls.
- **C.** The order details are added to the conversation and the model reasons about which action to take.
- **D.** The agent executes the remaining steps in a tool sequence planned at the start of the request.

---

## Question 7

_Domain: [`code_exploration`](Answer%20Key/code_exploration.md)_

An agent is dropped into an unfamiliar repository and asked to add a feature. The best way to orient without burning context is to:

- **A.** Load every file into context so nothing is missed.
- **B.** Read the entry points and project structure, then search for the area the feature touches.
- **C.** Start editing the first file that looks related.
- **D.** Ask the user to explain every file.

---

## Question 8

_Domain: [`customer_support`](Answer%20Key/customer_support.md)_

When implementing your `lookup_order` MCP tool, the backend sometimes returns errors (e.g., "Order not found" or temporary database failures).
What is the correct pattern for communicating these errors back to the agent?

- **A.** Log the error server-side and return an empty result to avoid confusing the model
- **B.** Return the error message in the tool result content with the isError flag set to true
- **C.** Throw an exception from the tool handler so the agent framework can catch and log it
- **D.** Return a success response with a "status" field indicating the error type

---

## Question 9

_Domain: [`code_exploration`](Answer%20Key/code_exploration.md)_

A README says the auth check happens in one module, but the agent must be sure before changing it. The agent should:

- **A.** Trust the README and edit the module it names.
- **B.** Confirm in the current code where the auth check actually runs, then make the change there.
- **C.** Search the commit history for the original author and ask them.
- **D.** Assume the check moved and search at random.

---

## Question 10

_Domain: [`code_exploration`](Answer%20Key/code_exploration.md)_

Before renaming a widely used function, an agent needs to know what a change would break. The right move is to:

- **A.** Rename it and run the build to see what fails.
- **B.** Search the codebase for all references first, then plan the change across the call sites.
- **C.** Rename only the definition and assume callers will adapt.
- **D.** Add a second function and leave the old one untouched.

---

## Question 11

_Domain: [`code_exploration`](Answer%20Key/code_exploration.md)_

An engineer asks your agent to identify untested code paths in a legacy payment processing module spanning 45 files. After reading the first 8 source files, the agent's responses are becoming noticeably less accurate—it's forgetting previously discussed code patterns and hasn't yet located all test files or traced critical payment flows.
What's the most effective approach to complete this investigation?

- **A.** Document all current findings in a summary report, clear context completely, then use that report as the sole reference for continuing the investigation.
- **B.** Spawn subagents to investigate specific questions (e.g., "find all test files for payment processing", "trace refund flow dependencies") while the main agent coordinates findings and preserves high-level understanding.
- **C.** Clear context with /clear, then selectively re-read only the most critical files discovered so far, writing key findings to a scratchpad file that persists between context resets.
- **D.** Switch to using Grep to search for specific function names instead of reading full files, reducing the content loaded into context for remaining exploration.

---

## Question 12

_Domain: [`code_exploration`](Answer%20Key/code_exploration.md)_

After adding an MCP server with specialized code refactoring tools (`extract_function`, `rename_variable`, `inline_function`), you notice the agent still uses basic text manipulation via Write and Bash sed commands for refactoring tasks. The MCP server is connected and healthy. Examining the configuration, you find each MCP tool has a minimal description like "`extract_function`: extracts a function from code."
What's the most effective way to improve adoption of the MCP refactoring tools?

- **A.** Implement a request classifier that detects refactoring intent and automatically routes those requests to the MCP server before the agent processes them.
- **B.** Remove the Write tool from the agent's configuration for refactoring sessions so it must use the MCP tools for code modifications.
- **C.** Accept this as expected behavior since simpler tools like sed are more predictable than specialized refactoring tools.
- **D.** Enhance the MCP tool descriptions to explain when each tool is preferable to text manipulation and clarify expected inputs and outputs.

---

## Question 13

_Domain: [`extraction_pipeline`](Answer%20Key/extraction_pipeline.md)_

Your pipeline uses a tool called `extract_metadata` with a JSON schema for paper details. You've also defined `lookup_citations` and `verify_doi` tools for enrichment. During testing, you notice that when users include requests like "extract the metadata and tell me how cited it is," Claude sometimes calls `lookup_citations` first, which fails because it needs the DOI that `extract_metadata` would provide.
What's the most effective way to ensure structured metadata extraction happens first?

- **A.** Set `tool_choice` to "any" so Claude must use a tool, combined with system prompt instructions prioritizing `extract_metadata`.
- **B.** Set `tool_choice` to "auto" and reorder the tool definitions so `extract_metadata` appears first in the tools array, since Claude prioritizes earlier-listed tools.
- **C.** Set `tool_choice` to {"type": "tool", "name": "`extract_metadata`"} and process the enrichment requests in subsequent turns after receiving the extracted metadata.
- **D.** Set `tool_choice` to {"type": "tool", "name": "`extract_metadata`"} for every API call in the pipeline, ensuring Claude always extracts metadata before any enrichment can occur.

---

## Question 14

_Domain: [`code_exploration`](Answer%20Key/code_exploration.md)_

A single source file is thousands of lines long and the agent needs one function from it. The agent should:

- **A.** Read the entire file into context to be thorough.
- **B.** Search within the file for the function and read only that region and its immediate dependencies.
- **C.** Read the first few hundred lines and stop.
- **D.** Reformat the file so it is easier to scan.

---

## Question 15

_Domain: [`customer_support`](Answer%20Key/customer_support.md)_

Production logs reveal inconsistent error handling: when `lookup_order` fails, the agent sometimes retries 5+ times (wasteful when the order ID doesn't exist), sometimes escalates immediately (premature for temporary network issues), and sometimes asks users for clarification (inappropriate when the issue is a backend permission error). Investigation shows your MCP tool returns uniform error responses: {"isError": true, "content": [{"type": "text", "text": "Operation failed"}]}. The agent cannot distinguish between error types.
What's the most effective improvement?

- **A.** Enhance error responses with structured metadata: include errorCategory (transient/validation/permission), isRetryable boolean, and a description of what caused the failure.
- **B.** Create an `analyze_error` MCP tool the agent calls after any failure to determine the error category and recommended action.
- **C.** Implement retry logic with exponential backoff in your MCP server for all errors, returning to the agent only after retries are exhausted.
- **D.** Add few-shot examples to the system prompt demonstrating how to interpret error message patterns and select appropriate responses for each.

---

## Question 16

_Domain: [`customer_support`](Answer%20Key/customer_support.md)_

Your `process_refund` tool returns two types of errors: technical errors ("503 Service Unavailable", "Connection timeout") that are transient (5% of calls), and business errors ("Order exceeds 30-day return window", "Item already refunded") that are permanent (12% of calls). Monitoring shows the agent wastes 3-4 turns retrying business errors that can never succeed. Currently, both error types return only a plain text message to Claude.
What's the most effective way to reduce wasted retries while improving customer-facing response quality?

- **A.** Return structured error responses with retryable: false for business errors and a customer-friendly explanation for Claude to use.
- **B.** Add few-shot examples showing how to distinguish retryable from non-retryable errors by parsing error message text.
- **C.** Add a `check_refund_eligibility` tool that must be called before `process_refund` to prevent business rule violations.
- **D.** Implement automatic retry logic at the tool level for technical errors only, passing business errors to Claude without retries.

---

## Question 17

_Domain: [`research_pipeline`](Answer%20Key/research_pipeline.md)_

A research agent must gather facts from eight independent web sources and produce one synthesis. None of the sources depend on each other. Which dispatch pattern stays fast without flooding the coordinator context?

- **A.** Read all eight sources into the coordinator context, then write the synthesis in a single pass.
- **B.** Dispatch eight sub-agents in parallel, each returning a short structured summary with citations, then synthesize from the summaries.
- **C.** Process the sources one at a time in a single agent, appending each full page to the running prompt.
- **D.** Pick the two sources that look most promising and ignore the rest to save tokens.

---

## Question 18

_Domain: [`extraction_pipeline`](Answer%20Key/extraction_pipeline.md)_

Documents arrive continuously throughout business hours and need structured data extracted. To reduce costs, you want to use the `Message Batches API` (50% discount, up-to-24-hour processing window). Your SLA specifies that extraction results must be available within 30 hours of document arrival with 99.9% reliability.
Which batching strategy is most appropriate?

- **A.** Submit batches every 6 hours containing documents from that window
- **B.** Submit a single batch at end of day containing all documents from that day
- **C.** Submit batches every 4 hours containing documents from that window
- **D.** Use the real-time API for all documents instead of batch processing

---

## Question 19

_Domain: [`extraction_pipeline`](Answer%20Key/extraction_pipeline.md)_

Your extraction system processes two document types: standard monthly reports (archived after processing) and urgent exception reports (must trigger business alerts within 30 minutes of receipt). Both use the same JSON schema. You want to minimize API costs while meeting latency requirements.
How should you architect the processing pipeline?

- **A.** Submit all documents to the real-time Messages API to ensure consistent processing latency across document types.
- **B.** Submit all documents to the `Batch API` with `custom_ids` for tracking. When results arrive, immediately process urgent documents and trigger delayed alerts for exceptions.
- **C.** Queue all documents and submit hourly batches, flagging urgent documents for expedited handling when batch results return.
- **D.** Route standard reports to the `Batch API` for 50% cost savings, and route urgent exception reports to the real-time Messages API.

---

## Question 20

_Domain: [`code_exploration`](Answer%20Key/code_exploration.md)_

An engineer who just joined the team asks the agent to help them understand the authentication and authorization architecture before making security improvements. The codebase has 800+ files across multiple services.
What exploration strategy will most effectively build understanding, given Claude built-in tools and context limits?

- **A.** Read any CLAUDE.md and README files first, then ask the engineer to specify which 10-15 files are most important for understanding the auth system.
- **B.** Launch parallel subagents to explore different services simultaneously, then synthesize their findings into an architectural overview.
- **C.** Use Grep to find authentication entry points, read those files, then follow imports and function calls to map the auth flow incrementally.
- **D.** Read all files containing "auth", "login", "permission", or "token" in their content or filename.

---

## Question 21

_Domain: [`extraction_pipeline`](Answer%20Key/extraction_pipeline.md)_

Your extraction uses tool use with a JSON schema where `property_type` is defined as an enum: ['house', 'apartment', 'condo', 'townhouse']. After deployment, 8% of extractions fail schema validation. Investigation reveals listings mention many uncommon property types—"studio", "loft", "duplex", "mobile home", "tiny house", "converted warehouse"—and new types continue appearing regularly.
What's the most effective long-term solution?

- **A.** Continuously expand the enum to include newly observed property types and add monitoring for additional edge cases.
- **B.** Add an "other" value to your enum with a separate `property_type_detail` string field for specifics when "other" is selected.
- **C.** Change `property_type` from an enum to a free-form string and implement a normalization step in post-processing.
- **D.** Add few-shot examples to your prompt demonstrating how to map unexpected property types to the closest existing enum value.

---

## Question 22

_Domain: [`extraction_pipeline`](Answer%20Key/extraction_pipeline.md)_

A contract is too long to fit in one context window, and you need fields from across the whole document. The dependable approach is to:

- **A.** Truncate the document to what fits and extract from the first part.
- **B.** Chunk the document with slight overlap, extract per chunk, then merge and reconcile the fields.
- **C.** Summarize the document first, then extract from the summary.
- **D.** Raise the temperature so the model fills in the missing parts.

---

## Question 23

_Domain: [`customer_support`](Answer%20Key/customer_support.md)_

A customer asks a simple question that the agent can answer directly from the knowledge base. The agent should:

- **A.** Escalate every question to a human to be safe.
- **B.** Answer the question directly and clearly, and offer escalation only if the customer needs more.
- **C.** Ask the customer to confirm three times before answering.
- **D.** Give a long disclaimer and avoid answering.

---

## Question 24

_Domain: [`research_pipeline`](Answer%20Key/research_pipeline.md)_

You are designing how sub-agents report findings so the final research output can be audited later. Each finding should travel with:

- **A.** Only the claim text, to keep messages short.
- **B.** The claim plus a reference to its source (URL or document id and location).
- **C.** The full raw page the claim came from, inline in every message.
- **D.** A confidence score and nothing else.

---

## Question 25

_Domain: [`research_pipeline`](Answer%20Key/research_pipeline.md)_

A user is expanding the research system beyond its single web search agent by adding specialized data sources. They add a financial API agent that returns structured JSON with revenue, margins, and growth rates; a news monitoring agent that returns prose summaries of recent developments; and a patent analysis agent that returns structured lists of technology areas. The synthesis agent combines these into executive briefings. Currently, it converts everything to bullet points, causing financial comparisons to lose tabular clarity and news summaries to lose narrative flow.
What change would most improve briefing quality?

- **A.** Standardize all subagent outputs to prose summaries with inline citations.
- **B.** Add a format conversion layer between subagents and synthesis that transforms all outputs to a common intermediate representation.
- **C.** Update the synthesis agent to render each content type appropriately—financial data as tables, news as prose.
- **D.** Standardize all subagent outputs to JSON with fields for claim, evidence, source, and confidence.

---

## Question 26

_Domain: [`customer_support`](Answer%20Key/customer_support.md)_

A customer returns 4 hours after their initial session about the same billing dispute. The previous 32-turn session contains `lookup_order` results showing "Status: PENDING, Expected resolution: 24-48 hours." In testing, you observe that when resuming sessions with stale tool results, the agent often references the outdated data in responses (e.g., "I see your refund is still being processed") even after subsequent fresh tool calls return different information.
What approach most reliably handles returning customers?

- **A.** Resume with full history but filter out previous `tool_result` messages before resuming, keeping only the human/assistant turns so the agent must re-fetch needed data.
- **B.** Start a new session, inject a structured summary of the previous interaction (issue type, actions taken, resolution status), then make fresh tool calls before engaging.
- **C.** Resume with full history and add a system prompt instruction telling the agent to always prefer the most recent tool results when multiple calls to the same tool exist in context.
- **D.** Resume with full history and configure the agent to automatically re-call all previously-used tools at session start to ensure data freshness.

---

## Question 27

_Domain: [`extraction_pipeline`](Answer%20Key/extraction_pipeline.md)_

Your system extracts event metadata (date, location, organizer, `attendee_count`) from news articles using a JSON schema with all nullable fields. During evaluation, you observe the model frequently generates plausible but incorrect values for fields not mentioned in the article—for example, outputting "500" for `attendee_count` when the source contains no attendance information.
What's the most effective way to reduce these false extractions?

- **A.** Add a post-processing step using a second LLM call to verify each extracted value exists in the source document.
- **B.** Add prompt instructions to return null for any field where information is not directly stated in the source.
- **C.** Make all schema fields required (non-nullable) with strict validation rules to ensure the model only outputs verifiable data.
- **D.** Upgrade to a more capable model tier with improved instruction-following to reduce hallucination tendencies.

---

## Question 28

_Domain: [`research_pipeline`](Answer%20Key/research_pipeline.md)_

In production, final reports frequently contain claims without proper source attribution. Investigation shows that while the web search and document analysis agents correctly attach citations to their outputs, the synthesis agent loses track of which sources support which conclusions when combining findings.
What's the most effective architectural change?

- **A.** Maintain complete transcripts of all subagent interactions and add a citation-resolution agent to analyze logs and determine attributions before report generation.
- **B.** Require all subagents to output structured claim-source mappings that the synthesis agent must preserve and merge when combining findings from multiple sources.
- **C.** Add a verification step where the report generator uses semantic similarity matching against original sources to reconstruct which claims came from which documents.
- **D.** Have the coordinator inject source identifier prefixes into text before each handoff, then parse these prefixes at report generation to reconstruct citations.

---

## Question 29

_Domain: [`research_pipeline`](Answer%20Key/research_pipeline.md)_

The coordinator provides detailed step-by-step instructions to the web search subagent, specifying exact search queries, source priorities, and date filters. Production monitoring reveals three issues: (1) the subagent reports "insufficient results" rather than trying alternative approaches when pre-specified searches fail, (2) research quality drops for emerging topics that don't match expected patterns, and (3) the subagent rarely surfaces valuable tangential sources.
What's the most effective way to improve subagent adaptability?

- **A.** Remove procedural details entirely, delegating with simple goals like "research X thoroughly" and relying on the subagent's general capabilities.
- **B.** Add explicit fallback directives to the detailed instructions: "If specified searches yield fewer than N results, attempt alternative query formulations before reporting failure."
- **C.** Implement a topic classification step where the coordinator categorizes requests as "well-defined" or "exploratory" and uses different instruction styles for each category.
- **D.** Specify research goals and quality criteria (coverage breadth, source diversity, recency) rather than procedural steps, letting the subagent determine its search strategy.

---

## Question 30

_Domain: [`customer_support`](Answer%20Key/customer_support.md)_

The agent verifies customer identity through a multi-step process before resetting passwords. During testing, you notice that after the customer answers the third verification question, the agent asks them to provide their name again, as if the earlier exchange never happened.
What's the most likely cause of this behavior?

- **A.** The verification tool is clearing the agent's internal state after each successful validation step.
- **B.** The prompt lacks instructions telling Claude to remember information across multiple exchanges.
- **C.** The conversation history isn't being passed in subsequent API requests.
- **D.** Claude's memory retention is limited to two conversational turns by default, requiring explicit configuration to extend it.

---

## Question 31

_Domain: [`code_exploration`](Answer%20Key/code_exploration.md)_

An engineer used the agent yesterday to analyze a legacy authentication module, identifying two distinct refactoring approaches: extracting a microservice versus refactoring in-place. Today, they want to explore both approaches in depth—having the agent propose specific code changes for each—before deciding which to implement.
What's the most effective way to structure this exploration?

- **A.** Resume yesterday's session to explore the first approach, then start a new session for the second, manually recreating the original context.
- **B.** Start two fresh sessions, manually providing a summary of yesterday's analysis findings to establish context.
- **C.** Resume yesterday's session and explore both approaches sequentially within the same conversation thread.
- **D.** Use `fork_session` to create two branches from yesterday's analysis, exploring one approach in each fork.

---

## Question 32

_Domain: [`research_pipeline`](Answer%20Key/research_pipeline.md)_

In production, you observe that simple fact-checking queries (e.g., "What year was the Paris Climate Agreement signed?") traverse all four subagents sequentially, consuming 40+ seconds and significant tokens per query. Complex comparative research benefits from the full pipeline. Your query distribution is diverse and evolving as users discover new applications.
What's the most effective approach to optimize for varying query complexity?

- **A.** Implement pattern-based routing that categorizes queries by structure (single-fact vs. comparative vs. analytical) and maps each category to a predefined subagent combination.
- **B.** Create a fast-path for factual questions that bypasses subagents entirely, routing all other queries through the complete pipeline to ensure research thoroughness.
- **C.** Have the coordinator analyze each query and dynamically decide which subagents to invoke based on its assessment of query requirements.
- **D.** Train a query complexity classifier on labeled historical data to predict optimal subagent combinations, retraining periodically as query patterns evolve.

---

## Question 33

_Domain: [`extraction_pipeline`](Answer%20Key/extraction_pipeline.md)_

Your extraction pipeline processes restaurant menus and must output structured JSON with fields for item names, descriptions, prices, and dietary tags. Some menus use inconsistent formatting—prices as "$12" vs "12.00", dietary info as icons vs text.
What's the most reliable approach?

- **A.** Use separate extraction calls for each field to ensure consistent handling of each type.
- **B.** Extract data as-is and normalize formats in post-processing code after Claude returns.
- **C.** Request multiple extraction attempts per document and select the most common format.
- **D.** Define a strict output schema and include format normalization rules in your prompt.

---

## Question 34

_Domain: [`research_pipeline`](Answer%20Key/research_pipeline.md)_

A research agent keeps spawning follow-up searches and the run is not converging. The most reliable way to prevent an endless loop is to:

- **A.** Let it continue until it naturally stops.
- **B.** Give the task an explicit budget and a coverage check, and stop once the questions are answered or the budget is spent.
- **C.** Cut the run off at a random time.
- **D.** Add more sub-agents so it finishes sooner.

---

## Question 35

_Domain: [`extraction_pipeline`](Answer%20Key/extraction_pipeline.md)_

After implementing tool use with strict schema definitions, JSON syntax errors are eliminated, but 5% of extractions still have valid JSON with empty arrays or null values for required fields like citations and methodology. Spot-checking reveals that source documents contain this information, but in varied formats—inline citations vs. bibliographies, methodology sections vs. details embedded in introductions.
What's the most effective way to address these failures?

- **A.** Implement retry logic that re-sends requests when validation detects empty required fields.
- **B.** Build a regex-based post-processing layer that scans source documents for citation patterns and methodology keywords, populating empty fields when the model fails to extract.
- **C.** Modify your schema to make citations and methodology optional, and flag incomplete records for manual review rather than failing validation.
- **D.** Add few-shot examples demonstrating extractions from documents with varied structures—showing how to identify citations in different formats and locate methodology details across section types.

---

## Question 36

_Domain: [`extraction_pipeline`](Answer%20Key/extraction_pipeline.md)_

An invoice extractor reads dates like 03/04/2025 that could be March 4 or April 3. The design that avoids silent errors is to:

- **A.** Assume the United States month-first format everywhere.
- **B.** Require an ISO date in the output schema, and when the input is ambiguous, flag the field for review instead of guessing.
- **C.** Store the date as the raw string and sort it out later.
- **D.** Drop any date that is ambiguous.

---

## Question 37

_Domain: [`code_exploration`](Answer%20Key/code_exploration.md)_

After integrating a local MCP server providing code analysis tools (`analyze_dependencies`, `find_dead_code`, `calculate_complexity`), you verify the server is healthy and tools appear in the tools/list response. However, you observe that the agent consistently uses Grep to search for import statements instead of calling `analyze_dependencies`—even when users explicitly ask about "code dependencies." Examining tool definitions reveals:
MCP: `analyze_dependencies` - "Analyzes dependency graph"
Built-in: Grep - "Search file contents for a pattern using regular expressions. Returns matching lines with line numbers and surrounding context."
What's the most effective approach to improve the agent's selection of MCP tools?

- **A.** Remove Grep from available tools when the MCP server is connected to eliminate functional overlap.
- **B.** Add routing instructions to the system prompt specifying that dependency-related questions should use MCP tools rather than Grep.
- **C.** Split `analyze_dependencies` into granular tools (`list_imports`, `resolve_transitive_deps`, `detect_circular_deps`) so each has a focused purpose less likely to overlap with Grep.
- **D.** Expand MCP tool descriptions to detail capabilities and outputs—e.g., "Builds dependency graph showing direct imports, transitive dependencies, and cycles."

---

## Question 38

_Domain: [`code_exploration`](Answer%20Key/code_exploration.md)_

An engineer asks the agent to understand how the caching layer works before adding a new cache invalidation trigger. After initial Grep searches, the agent has identified that caching logic spans 15 files including decorators, middleware, and service classes (~8,000 lines total).
What's the most effective next step for building understanding while managing context constraints?

- **A.** Use the Read tool to sequentially load all 15 files, building complete understanding across the full caching implementation.
- **B.** Analyze imports and class hierarchies to identify the base cache class, Read that file to understand the interface, then trace specific invalidation implementations.
- **C.** Use Grep to search for "invalidate" and "expire" patterns across all files, then Read only those specific line ranges with minimal surrounding context.
- **D.** Use Glob to find files matching common caching patterns (cache.py, caching/), prioritize the largest files by reading them first, then check smaller files for gaps.

---

## Question 39

_Domain: [`code_exploration`](Answer%20Key/code_exploration.md)_

Your agent needs to insert a new helper function into the middle of a 150-line utility module, between two existing functions. The Edit tool fails because its `old_string` parameter cannot find unique text to match — the file has repetitive docstrings, variable names, and structural patterns.
What's the most reliable way to complete this insertion?

- **A.** Use Edit with an extremely long `old_string` capturing 30+ lines of context to guarantee uniqueness
- **B.** Use Edit's `replace_all` parameter to target a common pattern and embed the new function in the replacement text
- **C.** Use Bash to append the function definition to the end of the file using heredoc syntax
- **D.** Use Read to load the file, add the function at the appropriate location, then Write the updated file

---

## Question 40

_Domain: [`customer_support`](Answer%20Key/customer_support.md)_

A support agent order-status tool returns data that looks stale and contradicts what the customer sees. The agent should:

- **A.** Report the tool value confidently as the truth.
- **B.** Tell the customer the system shows a possibly outdated status, and verify or escalate before committing to it.
- **C.** Side with whatever the customer says without checking.
- **D.** Keep retrying the tool silently until it agrees with the customer.

---

## Question 41

_Domain: [`customer_support`](Answer%20Key/customer_support.md)_

Compliance requires that refunds exceeding $500 must automatically escalate to a human agent—this rule cannot be left to model discretion. Despite clear system prompt instructions, production logs show the agent occasionally processes high-value refunds directly (3% failure rate).
How should you achieve guaranteed compliance?

- **A.** Modify the refund tool to return an error with message "Amount exceeds policy limit—please escalate" when threshold is exceeded.
- **B.** Add few-shot examples to the prompt showing correct escalation behavior at various refund amounts ($400, $500, $600).
- **C.** Implement a hook to intercept tool calls; when the refund process amount exceeds $500, block it and invoke human escalation.
- **D.** Strengthen the system prompt with emphatic language: "CRITICAL POLICY: Refunds over $500 MUST trigger human escalation. NEVER process these directly."

---

## Question 42

_Domain: [`code_exploration`](Answer%20Key/code_exploration.md)_

Your codebase exploration tool stores session IDs to allow engineers to continue investigations across work sessions. An engineer spent an hour yesterday analyzing a legacy authentication module, building context about its architecture and dependencies. They want to continue today. The session ID is valid, but version control shows 3 of the 12 files the agent previously read were modified overnight by a teammate's merge.
What approach best balances efficiency and accuracy?

- **A.** Resume the session without informing the agent about the changed files
- **B.** Start a fresh session to ensure the agent works with current codebase state without stale assumptions
- **C.** Resume the session and inform the agent which specific files changed for targeted re-analysis
- **D.** Resume the session and immediately have the agent re-read all 12 previously analyzed files

---

## Question 43

_Domain: [`research_pipeline`](Answer%20Key/research_pipeline.md)_

When researching "renewable energy adoption," the web search agent returns recent statistics (2024: 35% adoption) while the document analysis agent extracts data from internal reports (2022: 18% adoption). The synthesis agent incorrectly flags these as contradictory sources rather than recognizing the data shows growth over time.
What change would best enable the synthesis agent to correctly interpret such temporal differences?

- **A.** Require subagents to include publication or data collection dates in their structured outputs.
- **B.** Add a conflict resolution agent that automatically discards older data when newer data exists for the same metric.
- **C.** Configure the web search agent to only return results from the past 6 months.
- **D.** Instruct the synthesis agent to always treat the most recent data as authoritative and place older findings in a separate historical appendix.

---

## Question 44

_Domain: [`code_exploration`](Answer%20Key/code_exploration.md)_

During testing, you observe that in extended exploration sessions (30+ minutes), the agent starts giving inconsistent answers about code structure it discussed earlier. Engineers report having to repeat context about modules they've already explored.
What's the most effective approach to address this?

- **A.** Have the agent maintain a scratchpad file that records key findings, referencing it for subsequent questions.
- **B.** Switch to a higher-capacity model tier to provide more context window space for accumulated exploration data.
- **C.** Implement automatic context clearing every 15 minutes to ensure the agent starts with fresh, uncontaminated context.
- **D.** Create summaries of all source files before exploration begins, loading only these compressed representations into context.

---

## Question 45

_Domain: [`customer_support`](Answer%20Key/customer_support.md)_

A frustrated customer demands a refund that the policy does not allow. The best response is to:

- **A.** Grant the refund anyway to calm them down.
- **B.** Acknowledge the frustration, state the policy plainly, and offer the options that do exist.
- **C.** Restate the policy firmly and end the conversation.
- **D.** Promise to escalate without intending to.

---

## Question 46

_Domain: [`extraction_pipeline`](Answer%20Key/extraction_pipeline.md)_

A field the schema expects is simply not present in the source document. The extractor should:

- **A.** Fill the field with a plausible value inferred from the rest of the document.
- **B.** Return null for that field and mark it as not found, leaving the rest of the extraction intact.
- **C.** Fail the entire extraction because one field is missing.
- **D.** Repeat the previous record value for that field.

---

## Question 47

_Domain: [`research_pipeline`](Answer%20Key/research_pipeline.md)_

After the web search agent and document analysis agent complete their tasks, the coordinator invokes the synthesis agent. However, the synthesis agent responds that it cannot complete the task because no research findings were provided.
What is the most likely cause of this issue?

- **A.** The synthesis agent's context window is not large enough to hold the combined outputs from both previous agents.
- **B.** The coordinator did not include the outputs from the previous agents in the synthesis agent's prompt.
- **C.** The subagents need to share a single API connection to enable automatic context sharing between invocations.
- **D.** The synthesis agent needs tools that can fetch results directly from the other agents' conversation histories.

---

## Question 48

_Domain: [`research_pipeline`](Answer%20Key/research_pipeline.md)_

Production reviews reveal inconsistent handling of uncertainty in final reports. Sometimes conflicting subagent findings are synthesized into a single confident statement (losing nuance), while other times reports over-hedge with excessive qualifications (becoming unhelpful). When the web search agent returns "industry analysts estimate $50B market size (methodology varies)" and the document analysis agent returns "peer-reviewed study estimates 35B(±7B, 95% CI)," the coordinator either picks one arbitrarily or produces vague statements like "the market may be 35B−50B depending on factors."
What systematic approach best addresses this?

- **A.** Configure subagents to only report findings meeting a high-confidence threshold, filtering uncertain information before it reaches the coordinator.
- **B.** Implement a confidence calibration layer that normalizes subagent uncertainty expressions to standardized probability scores (0.0-1.0), then weight-average findings by their calibrated confidence.
- **C.** Instruct the synthesis agent to structure reports with explicit sections distinguishing well-established findings from contested ones, preserving original source characterizations and methodological context.
- **D.** Add a verification subagent that cross-references findings across sources, only passing claims to synthesis that are corroborated by at least two independent sources.

---

## Question 49

_Domain: [`extraction_pipeline`](Answer%20Key/extraction_pipeline.md)_

Your extraction system implements automatic retries when validation fails. On each retry, the specific validation error is appended to the prompt. This retry-with-error-feedback approach resolves most failures within 2-3 attempts.
For which failure pattern would additional retries be LEAST effective?

- **A.** The model extracts keywords as a nested object organized by category when the schema requires a flat array of strings
- **B.** The model extracts citation counts as locale-formatted strings ("1,234") when the schema requires integers
- **C.** The model extracts dates as ISO 8601 datetime strings ("2023-03-15T00:00:00Z") when the schema requires only the date portion (YYYY-MM-DD)
- **D.** The model extracts "et al." for co-authors when the full list exists only in an external document not in the input

---

## Question 50

_Domain: [`code_exploration`](Answer%20Key/code_exploration.md)_

An engineer's exploration subagent spent 30 minutes analyzing a legacy payment system, reading 47 files and documenting data flows. The session was interrupted when the engineer's connection dropped. While away, a teammate merged a PR that renamed two utility functions. The engineer wants to continue the same exploration.
What's the most effective approach?

- **A.** Resume the subagent from its previous transcript without mentioning the changes—the architecture understanding remains valid.
- **B.** Launch a fresh subagent and include the prior transcript in the initial prompt for context.
- **C.** Launch a fresh subagent with a summary of prior findings.
- **D.** Resume the subagent from its previous transcript and inform it about the renamed functions.

---

## Question 51

_Domain: [`extraction_pipeline`](Answer%20Key/extraction_pipeline.md)_

After deployment, you find that 12% of extractions contain semantic errors that pass JSON schema validation (e.g., a duration like "30 minutes" incorrectly placed in an ingredient quantity field). Human reviewers have capacity to check only 20% of extractions.
Which approach most effectively allocates reviewer attention?

- **A.** Have the model output field-level confidence scores, then calibrate review thresholds using a labeled validation set.
- **B.** Randomly sample 20% of extractions for review, using corrections to track accuracy and identify error patterns.
- **C.** Prioritize review of all extractions where required fields are empty or explicitly marked as not found.
- **D.** Review all extractions from documents with formatting anomalies such as unusual layouts or mixed content types.

---

## Question 52

_Domain: [`research_pipeline`](Answer%20Key/research_pipeline.md)_

Two sub-agents return conflicting figures for the same metric, each with moderate confidence. Before the coordinator writes the final answer, the best move is to:

- **A.** Average the two numbers and move on.
- **B.** Take whichever sub-agent answered first.
- **C.** Run a focused check that re-fetches the metric from the primary source and resolves the conflict before synthesizing.
- **D.** Include both numbers in the final answer and let the reader decide.

---

## Question 53

_Domain: [`research_pipeline`](Answer%20Key/research_pipeline.md)_

Production monitoring shows that follow-up queries like "summarize what we learned about market trends" consistently take 40+ seconds. Investigation reveals the coordinator spawns the synthesis subagent for each summarization request, passing 80K+ tokens of accumulated findings. The coordinator already has these findings in its context from orchestrating the research.
What's the most effective way to improve response time for these follow-up summaries?

- **A.** Pre-generate and cache summaries at multiple granularities whenever new findings accumulate.
- **B.** Have the coordinator handle straightforward summarization requests directly using its existing context, reserving subagent spawning for complex analysis.
- **C.** Enable prompt caching on the synthesis subagent to reduce the overhead of repeatedly transferring the same research findings.
- **D.** Spawn the synthesis subagent with reduced context and have it request specific findings from the coordinator on-demand.

---

## Question 54

_Domain: [`customer_support`](Answer%20Key/customer_support.md)_

Your agent has called `lookup_order` multiple times while investigating a customer's return requests. Each response includes 40+ fields (items, shipping details, payment info, status history). Tool outputs now represent the majority of the conversation's context. The customer mentions two more orders they want to discuss.
What's the most effective approach before making additional lookups?

- **A.** Extract only return-relevant fields (items, purchase date, return window, status) from each existing order response, removing verbose details
- **B.** Have the model generate a natural language summary of each order's key details, replacing structured responses with prose descriptions
- **C.** Move all tool responses to a vector database with semantic indexing, retrieving relevant portions as the conversation continues
- **D.** Proceed with additional lookups without modifying the existing tool output context

---

## Question 55

_Domain: [`research_pipeline`](Answer%20Key/research_pipeline.md)_

When analyzing complex legal cases that cite multiple precedents, the document analysis subagent processes each sequentially. A landmark case citing 12 precedents takes over 3 minutes to analyze completely.
What's the most effective way to reduce this latency while preserving the coordinator's ability to monitor and debug the system?

- **A.** Implement a message queue where precedent analysis tasks are processed asynchronously by a pool of worker agents.
- **B.** Create a recursive agent hierarchy where analysis agents subdivide work among child agents until reaching single-precedent granularity.
- **C.** Have the coordinator spawn parallel document analysis subagents, each handling a subset of precedents, then aggregate results before synthesis.
- **D.** Enable the document analysis subagent to spawn its own specialized subagents dynamically when it encounters cases with many citations.

---

## Question 56

_Domain: [`extraction_pipeline`](Answer%20Key/extraction_pipeline.md)_

Your system has been operating with 100% human review for 3 months. Analysis shows that extractions with model confidence >90% have 97% accuracy overall. To reduce reviewer workload, you plan to automate high-confidence extractions. Before deploying, what validation step is most critical?

- **A.** Analyze accuracy by document type and field to verify high-confidence extractions perform consistently across all segments, not just in aggregate.
- **B.** Compare accuracy at different confidence thresholds (85%, 90%, 95%) to find the optimal cutoff that maximizes automation while minimizing errors.
- **C.** Run a two-week pilot routing 25% of high-confidence extractions directly to downstream systems and monitor error reports.
- **D.** Verify that 97% accuracy meets requirements for all downstream systems that consume the extracted data.

---

## Question 57

_Domain: [`extraction_pipeline`](Answer%20Key/extraction_pipeline.md)_

Your extraction system parses e-commerce product descriptions to extract specifications like dimensions, weight, and materials into JSON. Despite having a well-defined schema, the model inconsistently extracts the "materials" field—sometimes returning "cotton blend", other times "Cotton/Polyester mix", and occasionally omitting the field when material information is clearly present in the source.
What's the most effective way to improve extraction consistency?

- **A.** Make the "materials" field required instead of optional in the schema to force the model to always extract a value
- **B.** Switch to a more capable model tier since inconsistent extraction indicates insufficient model capability
- **C.** Set temperature to 0 to eliminate randomness and ensure deterministic outputs
- **D.** Add few-shot examples showing 2-3 complete input-output pairs with standardized material description formats

---

## Question 58

_Domain: [`customer_support`](Answer%20Key/customer_support.md)_

A user asks a support agent for specific legal advice about a contract dispute. The right behavior is to:

- **A.** Give the best legal opinion the agent can produce.
- **B.** Say plainly this is outside what support can advise on, and point the user to the right resource or a human.
- **C.** Answer vaguely so the agent does not commit to anything.
- **D.** Ignore the legal part and answer something easier.

---

## Question 59

_Domain: [`customer_support`](Answer%20Key/customer_support.md)_

An agent has tried three times to resolve a billing issue and the customer is still stuck. The right next step is to:

- **A.** Try the same resolution a fourth time.
- **B.** Escalate to a human with the full history and what has been tried, so the customer does not start over.
- **C.** Tell the customer to open a new ticket.
- **D.** Close the conversation as resolved.

---

## Question 60

_Domain: [`research_pipeline`](Answer%20Key/research_pipeline.md)_

The web search agent has gathered several relevant sources for a research topic. The document analysis agent now needs to examine these sources.
How does information typically flow between these two specialized subagents?

- **A.** The agents communicate through an event-driven message queue, with the document analysis agent subscribing to web search completion events.
- **B.** The web search agent directly invokes the document analysis agent, passing the discovered sources as parameters.
- **C.** The coordinator agent receives the web search agent's output and includes relevant findings in the prompt when invoking the document analysis agent.
- **D.** Both agents access a shared memory store where the web search agent writes findings and the document analysis agent reads them.

---

