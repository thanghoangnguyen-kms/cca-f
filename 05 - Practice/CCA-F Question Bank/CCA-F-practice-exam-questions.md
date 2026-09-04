---
tags:
  - CCA-F
  - practice-exam
date: 2026-08-24
status: not-started
---

# Anthropic Claude Certified Architect Foundations (CCA-F) — Practice Exam

_Source: certificationpractice.com — practice exam #2564. 60 of 60 questions captured. Answers not marked._

> [!TIP] Sit these closed-book first
> Worked answers live in [Answer Key.md](Answer%20Key.md) — it opens with a full answer grid, so record your 60 before you open it. Each question then ends with an **Answer:** link jumping straight to its worked entry, for grading one item at a time.
>
> One caveat on those links: the key's headings carry the answer letter (`## Q1 — … → **C**`), so each link's *target* contains it too. Rendered, you see only the link text — but in **source/edit mode the letter is visible in the URL**. Read this file in reading view while you sit it.

## Question 1

A CI pipeline needs to run Claude Code to analyze pull requests and produce machine-parseable review findings that a downstream script posts as inline comments. Which combination of CLI flags ensures the output conforms to a predefined JSON structure?

- **A.** --output-format structured combined with --schema-file
- **B.** --format json-strict combined with --validate-output
- **C.** --output-format json combined with --json-schema
- **D.** -p combined with --json-schema only

**Answer:** [↳ Q1 in the answer key](Answer%20Key.md#Q1%20—%20CI%20flags%20for%20schema-conforming%20output%20→%20C)

## Question 2

After submitting 200 document extraction requests to the Message Batches API, you discover that 15 requests failed because the source documents exceeded context limits. You need to resubmit only the failed requests after chunking those documents into smaller sections. How should you identify which specific documents need resubmission?

- **A.** Use the custom_id field assigned to each request to correlate failures back to the specific source documents
- **B.** Compare the count of successful results against the original ordered submission list to determine which entries are missing
- **C.** Parse the error response bodies to extract document filenames from the original prompt text
- **D.** Query the batch status endpoint with the batch_id to retrieve an ordered index list of failed requests

**Answer:** [↳ Q2 in the answer key](Answer%20Key.md#Q2%20—%20Correlating%2015%20batch%20failures%20back%20to%20source%20documents%20→%20A)

## Question 3

A document analysis subagent encounters a timeout when accessing one of three external data sources, but it successfully retrieves data from the other two. Which error handling approach represents a best practice for this situation?

- **A.** Return the results from the two successful sources as if all three queries succeeded, omitting any indication that one source failed
- **B.** Terminate the entire analysis workflow and report the timeout failure to the user
- **C.** Queue the failed query for background retry and block the coordinator from proceeding until the retry completes or times out
- **D.** Attempt local recovery for the transient failure, and if unresolved, propagate structured error context with partial results to the coordinator

**Answer:** [↳ Q3 in the answer key](Answer%20Key.md#Q3%20—%20Subagent%20times%20out%20on%201%20of%203%20sources%20→%20D)

## Question 4

A team added "only report high-confidence findings" to their CI code review prompt after developers complained about too many low-value findings. The false positive rate, however, has not improved. What is the most likely reason this instruction failed to reduce false positives?

- **A.** The instruction conflicts with the default tool_choice setting, which forces the model to report all detected issues
- **B.** The instruction does not define specific categories of issues to report or skip, so the model has no actionable criteria for filtering
- **C.** The CI pipeline's non-interactive mode prevents the model from processing system prompt instructions
- **D.** The model always treats every finding as high-confidence because it cannot calibrate certainty without labeled training data

**Answer:** [↳ Q4 in the answer key](Answer%20Key.md#Q4%20—%20"Only%20report%20high-confidence%20findings"%20didn't%20help%20→%20B)

## Question 5

Your team needs to connect Claude Code to both Jira for issue tracking and a proprietary internal approval workflow system that is unique to your organization. A developer proposes building custom MCP servers for both integrations to keep the codebase consistent. What is the recommended approach?

- **A.** Use community MCP servers for both integrations by adapting the proprietary approval workflow API to match an existing community server's interface
- **B.** Build custom MCP servers for both integrations to ensure consistent implementation patterns and full control over tool behavior
- **C.** Use a community MCP server for Jira and build a custom MCP server only for the proprietary approval workflow
- **D.** Build a single custom MCP server that consolidates both Jira and approval workflow interactions behind a unified interface

**Answer:** [↳ Q5 in the answer key](Answer%20Key.md#Q5%20—%20Jira%20+%20a%20proprietary%20approval%20system%20→%20C)

## Question 6

Your invoice extraction pipeline processes documents from multiple international vendors. Dates appear in varied formats across vendors, including "15 March 2024," "03/15/2024," and "2024.03.15." The JSON schema enforces ISO 8601 format for date fields via tool_use, yet extracted dates occasionally retain the vendor's original format. What is the most effective way to ensure consistent date normalization across all vendor documents?

- **A.** Adding a regex pattern constraint to the date field in the JSON schema to enforce the YYYY-MM-DD format
- **B.** Creating a separate extraction schema for each vendor's known date format
- **C.** Including explicit format normalization rules in the extraction prompt and also enforcing the ISO 8601 date format in the output/tool schema (e.g., using format: "date" or strict tool use) so the model both normalizes and is validated
- **D.** Implementing a validation-retry loop that rejects extractions containing any non-ISO 8601 date

**Answer:** [↳ Q6 in the answer key](Answer%20Key.md#Q6%20—%20Vendor%20dates%20still%20arriving%20unnormalized%20→%20C)

## Question 7

A team needs to integrate Claude's output with a downstream inventory management system that requires strictly valid JSON conforming to a predefined schema. They are evaluating different approaches to ensure the output never contains JSON syntax errors such as missing brackets, trailing commas, or unescaped characters. Which approach provides the strongest guarantee of schema-compliant, syntax-error-free output?

- **A.** Appending few-shot examples of correctly formatted JSON to every extraction prompt
- **B.** Defining the required structure as a tool's JSON schema input parameters and extracting data from the tool_use response
- **C.** Parsing the model's freeform text response with a JSON validator and requesting corrections when syntax errors are detected
- **D.** Providing a detailed JSON template in the system prompt with instructions to replicate the exact structure

**Answer:** [↳ Q7 in the answer key](Answer%20Key.md#Q7%20—%20Strongest%20guarantee%20of%20valid%20JSON%20→%20B)

## Question 8

A developer productivity agent has access to a generic fetch_url tool that can retrieve content from any URL on the internet. During testing, the agent occasionally fetches unrelated external websites when it should only load internal API documentation hosted on the company's documentation server. Which approach most reliably prevents this misuse?

- **A.** Implement a PostToolUse hook that checks the fetched content and discards results from non-documentation domains
- **B.** Add a system prompt instruction specifying that fetch_url should only be used for internal documentation URLs
- **C.** Set tool_choice to force fetch_url on every turn so the agent always uses the tool in a predictable manner
- **D.** Replace fetch_url with a load_internal_docs tool that validates URLs against the internal documentation domain before making the request

**Answer:** [↳ Q8 in the answer key](Answer%20Key.md#Q8%20—%20Generic%20`fetch_url`%20reaching%20the%20open%20internet%20→%20D)

## Question 9

During a prolonged codebase exploration session, an agent begins referencing "typical patterns" and giving vague descriptions instead of citing the specific classes and method signatures it discovered in earlier turns. What technique most effectively counteracts this context degradation?

- **A.** Restart the exploration session from scratch whenever the agent's responses become vague to ensure a clean context
- **B.** Use /compact repeatedly throughout the session to free up context space for new discoveries
- **C.** Have the agent maintain a scratchpad file that records key findings such as class names, method signatures, and file paths, and reference it when answering subsequent questions
- **D.** Increase the max_tokens parameter so the agent can generate longer, more detailed responses

**Answer:** [↳ Q9 in the answer key](Answer%20Key.md#Q9%20—%20Exploration%20going%20vague%20after%20many%20turns%20→%20C)

## Question 10

Your customer support agent inconsistently decides when to escalate cases to human agents. Adding instructions like "only escalate high-confidence cases" and "be conservative about escalation" to the system prompt has not improved consistency. What approach would most effectively produce reliable escalation behavior?

- **A.** Implement sentiment analysis on customer messages and trigger escalation when negative sentiment exceeds a defined threshold
- **B.** Add explicit escalation criteria and include few-shot examples in the prompt (for example, in the first user message) that demonstrate specific scenarios where the agent should escalate versus resolve autonomously
- **C.** Require the agent to attempt autonomous resolution for at least three turns before allowing any escalation
- **D.** Have the agent self-report a confidence score on each turn and escalate whenever it falls below 60%

**Answer:** [↳ Q10 in the answer key](Answer%20Key.md#Q10%20—%20Inconsistent%20escalation,%20vague%20instructions%20didn't%20fix%20it%20→%20B)

## Question 11

Your support agent calls a lookup_order MCP tool that returns over 40 fields per order, including shipping carrier details, warehouse codes, and internal tracking metadata. The agent only needs 5 fields to process a return. After several order lookups in one session, response quality noticeably declines. Which approach best addresses this issue?

- **A.** Summarize the entire conversation history periodically using progressive summarization to reclaim token budget
- **B.** Limit the agent to a maximum of three order lookups per session to prevent excessive context accumulation
- **C.** Trim the tool output to include only return-relevant fields before appending the result to conversation context
- **D.** Switch to a model with a larger context window so verbose tool outputs can be accommodated without impacting quality

**Answer:** [↳ Q11 in the answer key](Answer%20Key.md#Q11%20—%2040-field%20tool%20output,%20quality%20declining%20→%20C)

## Question 12

Your team's CI/CD pipeline is configured to run Claude Code for automated code review on every pull request. During the first test run, the pipeline job hangs indefinitely and eventually times out without producing any output. What is the most likely cause of this behavior?

- **A.** The pull request diff exceeded the context window limit, causing Claude Code to fail silently
- **B.** Claude Code is waiting for interactive input because the -p flag was not included in the command
- **C.** The --output-format flag was not specified, preventing Claude Code from writing output to stdout
- **D.** The CLAUDE.md file is missing review criteria, causing Claude Code to loop indefinitely while searching for instructions

**Answer:** [↳ Q12 in the answer key](Answer%20Key.md#Q12%20—%20CI%20job%20hangs%20and%20times%20out%20with%20no%20output%20→%20B)

## Question 13

Your automated code review agent flags too many false positives when checking whether code comments are accurate. Developers have started ignoring the agent's output entirely. Which prompt modification would most effectively reduce false positives in the comment accuracy checks?

- **A.** Adding "only report high-confidence findings about comment accuracy" to the system prompt
- **B.** Specifying "flag comments only when the described behavior directly contradicts the actual code logic"
- **C.** Instructing the agent to "be conservative and avoid flagging minor comment issues"
- **D.** Including a general instruction to "prioritize precision over recall when reviewing code comments"

**Answer:** [↳ Q13 in the answer key](Answer%20Key.md#Q13%20—%20Reducing%20false%20positives%20on%20comment-accuracy%20checks%20→%20B)

## Question 14

Your team's automated code review pipeline generates hundreds of findings weekly, but developers report that many are low-value. With limited reviewer bandwidth, which approach best ensures human reviewers focus on the findings most likely to be actionable?

- **A.** Run a verification pass where the model self-reports a confidence score alongside each finding, then use confidence thresholds to route uncertain findings to human reviewers first
- **B.** Filter findings by source file directory and route only findings in security-critical paths to human reviewers
- **C.** Aggregate all findings by category and present only one representative example per category to reduce total reviewer volume
- **D.** Count the lines of code affected by each finding and prioritize findings with the largest code surface area for review

**Answer:** [↳ Q14 in the answer key](Answer%20Key.md#Q14%20—%20🔶%20Prioritizing%20findings%20for%20scarce%20reviewer%20bandwidth%20→%20A)

## Question 15

Your CI code review system generates structured JSON findings for pull requests, and developers can dismiss findings they consider incorrect. Over the past quarter, the dismissal rate has risen to 40%, but you cannot determine which types of code constructs are triggering the most dismissed findings. What should you add to each structured finding to enable systematic analysis of why developers are dismissing specific results?

- **A.** A detected_pattern field describing the specific code construct that triggered the finding, enabling correlation between dismissed findings and recurring pattern types
- **B.** A review_instance_id field linking each finding to the specific Claude session that generated it
- **C.** A confidence_score field with a numeric value so dismissed findings can be filtered by the model's self-reported certainty
- **D.** A timestamp field recording when the finding was generated to correlate dismissals with time of day

**Answer:** [↳ Q15 in the answer key](Answer%20Key.md#Q15%20—%2040%%20dismissal%20rate,%20no%20idea%20why%20→%20A)

## Question 16

In a multi-agent research system, the synthesis subagent is responsible for combining findings from other agents into a cohesive report. During testing, you observe that this subagent frequently initiates its own web searches and document retrievals instead of synthesizing the provided findings. The synthesis agent currently has access to all 18 tools in the system. What is the most effective way to resolve this behavior?

- **A.** Add detailed prompt instructions telling the synthesis agent to focus only on combining findings and not to use search tools
- **B.** Restrict the synthesis agent's allowedTools to only those relevant to its synthesis role, removing search and retrieval tools
- **C.** Increase the amount of context provided to the synthesis agent so it has less reason to perform its own searches
- **D.** Configure tool_choice: "any" so the synthesis agent is forced to call a tool rather than returning text, reducing off-task behavior

**Answer:** [↳ Q16 in the answer key](Answer%20Key.md#Q16%20—%20🔶%20Synthesis%20subagent%20doing%20its%20own%20searches%20→%20B)

## Question 17

You are building an invoice extraction pipeline where Claude extracts line items and totals from scanned invoices. Occasionally, extracted line item amounts do not add up to the extracted total, but both values exist in the source document. Which schema design most effectively enables automatic detection of these arithmetic discrepancies?

- **A.** Include both a "calculated_total" field for the sum of extracted line items and a "stated_total" field for the document's printed total, then compare them programmatically to flag mismatches
- **B.** Add a required "total_verified" boolean that the model must set to true after confirming the total matches the line items
- **C.** Include a "confidence_score" field for the total amount and reject any extraction where the confidence falls below 0.9
- **D.** Require the model to extract the total amount in two separate fields and average the two values to improve accuracy

**Answer:** [↳ Q17 in the answer key](Answer%20Key.md#Q17%20—%20Line%20items%20not%20summing%20to%20the%20stated%20total%20→%20A)

## Question 18

A customer-facing support agent built with the Claude Agent SDK handles multi-issue sessions where customers raise several complaints in one conversation. After many turns, the agent begins confusing order details between different issues, such as applying the wrong refund amount to the wrong order. What is the most effective approach to prevent this problem?

- **A.** Instruct the agent in the system prompt to carefully track all issue details throughout the conversation
- **B.** Summarize the full conversation every five turns to keep context usage low
- **C.** Limit multi-issue sessions to a maximum of two issues and require the customer to start new sessions for additional complaints
- **D.** Extract structured issue data such as order IDs, amounts, and statuses into a persistent case facts block included in each prompt

**Answer:** [↳ Q18 in the answer key](Answer%20Key.md#Q18%20—%20Refunds%20applied%20to%20the%20wrong%20order%20in%20multi-issue%20sessions%20→%20D)

## Question 19

A team provides four few-shot examples showing Claude how to distinguish acceptable defensive null-check patterns from genuine error-handling bugs during code review. After deployment, the agent correctly identifies a problematic error-handling pattern in a codebase written in a different language than any of the examples. What best explains this behavior?

- **A.** The model recognized the specific language syntax from its pre-training data and applied language-specific review rules
- **B.** The few-shot examples taught the model the underlying judgment criteria for evaluating error handling, which it generalized to the structurally similar but previously unseen pattern
- **C.** The model defaulted to flagging the unfamiliar pattern because it did not match any known-acceptable examples
- **D.** The model decomposed the novel pattern into exact sub-patterns that matched elements from the few-shot examples

**Answer:** [↳ Q19 in the answer key](Answer%20Key.md#Q19%20—%20Few-shot%20examples%20generalizing%20to%20an%20unseen%20language%20→%20B)

## Question 20

A developer asks Claude to generate a complex recursive algorithm, and then within the same conversation asks Claude to review the generated code for correctness. Claude reports no issues, but a peer reviewer later finds a subtle logic error. What best explains why the same-session review failed to catch this bug?

- **A.** The model defaults to positive assessments to maintain conversational coherence with the user
- **B.** The review prompt did not include explicit criteria for checking recursive boundary conditions
- **C.** The generated code consumed most of the context window, leaving insufficient tokens for a thorough review
- **D.** The model retains its reasoning context from generation, making it less likely to question its own prior decisions in the same session

**Answer:** [↳ Q20 in the answer key](Answer%20Key.md#Q20%20—%20Same-session%20self-review%20missed%20the%20bug%20→%20D)

## Question 21

Your document extraction pipeline uses a two-step process: first, it calls extract_metadata to determine the document type, then calls extract_fields with a type-specific schema. You plan to migrate this pipeline to the Message Batches API to reduce costs on a nightly run of 500 documents. What limitation of the batch API requires you to redesign this workflow?

- **A.** The Message Batches API requires all requests in a batch to share the same system prompt and tool definitions
- **B.** The Message Batches API does not support forced tool selection, allowing only tool_choice set to auto
- **C.** The Message Batches API does not support multi-turn tool calling within a single request, so you cannot execute a tool and return its result mid-request for a second tool call
- **D.** The Message Batches API limits each submission to a maximum of 100 requests per batch

**Answer:** [↳ Q21 in the answer key](Answer%20Key.md#Q21%20—%20🔶%20Batch%20API%20limitation%20forcing%20a%20redesign%20→%20C)

## Question 22

Your multi-agent system queries a documentation database containing thousands of technical articles organized by product area and topic. Agents currently make numerous exploratory tool calls to discover what documentation is available before performing their targeted searches, consuming significant tokens and increasing latency. What is the recommended approach to reduce these exploratory calls?

- **A.** Expose a content catalog as an MCP resource that provides a browsable hierarchy of available documentation topics and article summaries
- **B.** Cache the results of exploratory tool calls in a shared database that all agents query before initiating new searches
- **C.** Increase each agent's max_tokens allocation to accommodate the additional exploratory tool call results
- **D.** Pre-load complete summaries of all documentation articles into the system prompt for every agent invocation

**Answer:** [↳ Q22 in the answer key](Answer%20Key.md#Q22%20—%20Agents%20burning%20tokens%20on%20exploratory%20discovery%20calls%20→%20A)

## Question 23

Your multi-agent research system produces a synthesis report that combines findings from web search and document analysis subagents. Two credible sources provide conflicting market size statistics, and the synthesis subagent must produce the final output. How should the report handle this conflict?

- **A.** Present both statistics with full source attribution in a section that explicitly distinguishes contested findings from well-established ones, preserving each source's methodological context
- **B.** Average the two statistics to produce a single balanced figure and cite both sources
- **C.** Omit the market size data point entirely to avoid presenting potentially inaccurate information
- **D.** Select the statistic from the most authoritative source based on publication recency and discard the conflicting value

**Answer:** [↳ Q23 in the answer key](Answer%20Key.md#Q23%20—%20Two%20credible%20sources,%20conflicting%20market-size%20figures%20→%20A)

## Question 24

A customer reaches your support agent and immediately says, "I would like to speak with a real person, please." The agent has access to tools that could likely resolve the customer's underlying billing issue quickly. What is the correct agent behavior in this situation?

- **A.** Inform the customer that billing issues can typically be resolved faster by the automated agent and proceed with investigation
- **B.** Ask the customer to describe their issue so the agent can attempt a quick resolution before transferring
- **C.** Investigate the billing issue silently and then escalate, providing the human agent with a complete resolution summary
- **D.** Acknowledge the customer's request and immediately escalate to a human agent

**Answer:** [↳ Q24 in the answer key](Answer%20Key.md#Q24%20—%20"I%20would%20like%20to%20speak%20with%20a%20real%20person,%20please"%20→%20D)

## Question 25

Your extraction system has the model output field-level confidence scores to route low-confidence extractions to human review. However, reviewers report that many supposedly high-confidence extractions contain errors, while some flagged low-confidence ones are correct. What is the most effective way to improve the accuracy of this routing?

- **A.** Lower the confidence threshold significantly so that nearly all extractions are routed to human review
- **B.** Calibrate the confidence score thresholds using a labeled validation set of known-correct extractions to align reported confidence with actual accuracy
- **C.** Remove confidence scoring entirely and instead route all extractions from specific document types known to be error-prone to human review
- **D.** Replace field-level confidence scores with a single document-level confidence score to simplify the routing logic

**Answer:** [↳ Q25 in the answer key](Answer%20Key.md#Q25%20—%20Confidence%20scores%20that%20don't%20track%20accuracy%20→%20B)

## Question 26

Your customer service agent connects to fetch_customer and get_order through MCP. The fetch_customer tool returns created_date as a Unix timestamp (e.g., 1718200000), while get_order returns order_date in ISO 8601 format (e.g., "2024-06-12T15:00:00Z"). The agent sometimes misinterprets these inconsistent formats when reasoning about order timelines. What is the recommended approach to ensure consistent date handling before the agent processes these results?

- **A.** Implement a PostToolUse hook that normalizes date formats from both tools into a consistent representation before the agent processes the results
- **B.** Modify each MCP tool server's internal implementation to always return dates in the same format
- **C.** Add instructions to the system prompt telling the agent to mentally convert all dates to a single format before reasoning about timelines
- **D.** Use few-shot examples in the prompt showing the agent how to correctly interpret both Unix timestamps and ISO 8601 dates

**Answer:** [↳ Q26 in the answer key](Answer%20Key.md#Q26%20—%20Unix%20timestamps%20from%20one%20MCP%20tool,%20ISO%208601%20from%20another%20→%20A)

## Question 27

Your company policy requires that any refund exceeding $500 must be approved by a human supervisor before processing. During testing, you discover that prompt instructions alone occasionally fail to prevent the agent from calling issue_refund for amounts above the threshold. Which implementation guarantees this business rule is enforced without exception?

- **A.** Include three few-shot examples in the prompt demonstrating correct escalation for high-value refunds
- **B.** Set the issue_refund tool's JSON schema to define a maximum value constraint of 500 on the amount field
- **C.** Strengthen the system prompt by adding explicit instructions with bold emphasis: "NEVER issue refunds above $500 without human approval"
- **D.** Implement a tool call interception hook that inspects the amount parameter on issue_refund calls, blocks those exceeding $500, and redirects the workflow to handoff_to_human

**Answer:** [↳ Q27 in the answer key](Answer%20Key.md#Q27%20—%20Refunds%20over%20$500%20must%20never%20bypass%20a%20supervisor%20→%20D)

## Question 28

A customer contacts your agent with a vague complaint: "Something is wrong with my account. I've been overcharged and I think someone else has accessed it." The scope of each concern is unclear until initial lookups are performed. Which task decomposition strategy is most appropriate for handling this type of open-ended, multi-concern request?

- **A.** A single comprehensive prompt that includes all available customer data and asks the agent to resolve every concern in one turn
- **B.** A fixed sequential pipeline that always runs fetch_customer, then get_order for the last 10 orders, then issue_refund for any billing discrepancy found
- **C.** A predefined decision tree that maps each keyword in the customer's message to a specific tool call sequence
- **D.** Dynamic adaptive decomposition that investigates each concern based on what is discovered at each step, generating follow-up subtasks as findings emerge

**Answer:** [↳ Q28 in the answer key](Answer%20Key.md#Q28%20—%20Vague%20multi-concern%20complaint,%20scope%20unknown%20up%20front%20→%20D)

## Question 29

Your team wants the customer service agent to audit the resolution quality of 50 past support tickets. Each ticket involves different product categories, refund amounts, and escalation decisions. You need the agent to evaluate each ticket's handling individually and then identify systemic patterns across the full set. Which prompt chaining pattern best supports this workflow?

- **A.** Run per-ticket analysis passes that evaluate each ticket individually, then run a separate cross-ticket synthesis pass to identify systemic patterns across all evaluations
- **B.** Send all 50 tickets in a single prompt and instruct the agent to evaluate quality and identify patterns in one pass
- **C.** Have the agent process tickets in pairs, comparing each pair for similarities before aggregating all pair comparisons at the end
- **D.** Randomly sample five tickets, evaluate them in a single prompt, and extrapolate the findings to the remaining 45 tickets

**Answer:** [↳ Q29 in the answer key](Answer%20Key.md#Q29%20—%20Auditing%2050%20tickets%20individually,%20then%20finding%20systemic%20patterns%20→%20A)

## Question 30

You spent yesterday investigating a bug in the customer service agent's refund flow. You named that investigation session "refund-trace" and identified several promising leads. Today you want to continue exactly where you left off, preserving the full conversation history from yesterday. Which command correctly resumes your named session?

- **A.** claude --load refund-trace
- **B.** claude --session refund-trace --continue
- **C.** claude --fork refund-trace
- **D.** claude --resume refund-trace

**Answer:** [↳ Q30 in the answer key](Answer%20Key.md#Q30%20—%20Resuming%20yesterday's%20named%20session%20→%20D)

## Question 31

After completing an initial analysis of your customer service agent's escalation logic, you want to evaluate two alternative strategies: one using threshold-based triggers and another using policy-gap detection. Both strategies should build on the same baseline understanding of the current codebase without repeating the initial analysis. Which session management approach allows you to explore both independently from the shared baseline?

- **A.** Start two new sessions from scratch and re-run the codebase analysis in each before exploring the respective strategy
- **B.** Copy the session transcript into two new prompts manually and start fresh sessions with the pasted context
- **C.** Use fork_session to create two independent branches from the shared analysis baseline, exploring each strategy in its own branch
- **D.** Resume the original session with --resume and explore both approaches sequentially, using /compact between them to clear context

**Answer:** [↳ Q31 in the answer key](Answer%20Key.md#Q31%20—%20Two%20alternative%20strategies%20from%20one%20shared%20baseline%20→%20C)

## Question 32

The agent in your customer service system has a single MCP tool called manage_account that handles profile updates, password resets, subscription changes, and account deactivation. During testing, the agent frequently sends incorrect parameters because it conflates these distinct operations. What is the recommended approach to improve the agent's tool selection reliability?

- **A.** Use tool_choice forced selection to always call manage_account and add a required "operation_type" enum parameter to disambiguate the intended action
- **B.** Add a comprehensive description to manage_account that lists all four operations with their respective required parameters and usage conditions
- **C.** Implement a PostToolUse hook that validates the parameters after each manage_account call and retries with corrected parameters if the operation type was wrong
- **D.** Split manage_account into purpose-specific tools such as update_profile, reset_password, change_subscription, and deactivate_account, each with clearly defined input/output contracts

**Answer:** [↳ Q32 in the answer key](Answer%20Key.md#Q32%20—%20One%20`manage_account`%20tool%20doing%20four%20unrelated%20jobs%20→%20D)

## Question 33

Within your customer service system, the agent has four well-described MCP tools: fetch_customer, get_order, issue_refund, and handoff_to_human. Despite clear tool descriptions, whenever a customer mentions the word "refund," the agent consistently calls handoff_to_human instead of issue_refund, even for straightforward cases within policy. After reviewing the configuration, you find the system prompt includes: "When a customer mentions a refund, always ensure a human is involved." What is the most likely cause of this behavior and how should it be resolved?

- **A.** The issue_refund tool description needs to explicitly state that it should be selected over handoff_to_human for standard refund requests
- **B.** The agent's tool_choice setting should be changed to forced selection of issue_refund whenever refund-related keywords are detected in the input
- **C.** The keyword-sensitive instruction in the system prompt creates an unintended association that overrides the tool descriptions, so the prompt should be revised to specify precise conditions requiring human involvement
- **D.** The handoff_to_human tool description overlaps with issue_refund, so both tools should be renamed to more distinctive names

**Answer:** [↳ Q33 in the answer key](Answer%20Key.md#Q33%20—%20Every%20mention%20of%20"refund"%20triggers%20`handoff_to_human`%20→%20C)

## Question 34

Currently, your customer service agent has access to fetch_customer and get_order, but both tools have minimal one-line descriptions: "Fetches customer data" and "Gets order information." When a customer asks about a recent purchase, the agent inconsistently alternates between calling fetch_customer and get_order. What is the primary reason for this unreliable tool selection?

- **A.** The model requires tool_choice to be set to forced selection before it can reliably distinguish between any two tools
- **B.** The agent's context window is exhausted by other content, leaving insufficient space to load both tool definitions simultaneously
- **C.** The tool names are too similar in length, causing the model to confuse them regardless of their descriptions
- **D.** Tool descriptions are the primary mechanism the model uses for tool selection, and the minimal descriptions do not provide enough information to differentiate when each tool should be used

**Answer:** [↳ Q34 in the answer key](Answer%20Key.md#Q34%20—%20Thin%20one-line%20descriptions,%20agent%20alternates%20between%20two%20tools%20→%20D)

## Question 35

The get_order tool in your customer service agent sometimes fails in two distinct ways: a temporary database timeout that resolves on retry, and a business policy violation when a customer requests a refund on a non-returnable item. Currently, both failures return a generic message: "Operation failed." Why is returning structured error metadata with distinct error categories critical for these two scenarios?

- **A.** Structured error metadata reduces token usage in the conversation history by replacing verbose error messages with compact error codes
- **B.** Structured error metadata is primarily for logging and observability purposes and does not change how the agent responds to the customer
- **C.** The MCP protocol requires every tool error to include a specific error category before the agent is allowed to continue the conversation
- **D.** Without structured metadata distinguishing transient errors from business rule violations, the agent cannot determine whether to retry the call or explain the policy to the customer, leading to wasted retries or poor customer communication

**Answer:** [↳ Q35 in the answer key](Answer%20Key.md#Q35%20—%20Why%20structured%20error%20metadata%20matters%20for%20two%20failure%20kinds%20→%20D)

## Question 36

Your structured data extraction system processes real estate listing documents. When listings describe property size with informal terms like "spacious" or "generous open-plan living area" instead of exact measurements, the model frequently fabricates numeric square footage values. Which approach would most effectively reduce these hallucinated values while preserving extraction accuracy for documents that contain explicit measurements?

- **A.** Making the square_footage field required in the schema and adding a post-extraction validation step that rejects non-numeric values
- **B.** Adding the instruction "only extract values you are certain about" to the system prompt
- **C.** Providing few-shot examples that demonstrate returning null for square footage when documents use informal descriptions, alongside examples that correctly extract explicit numeric measurements
- **D.** Removing the square_footage field from the extraction schema entirely to eliminate the possibility of fabrication

**Answer:** [↳ Q36 in the answer key](Answer%20Key.md#Q36%20—%20Fabricated%20square%20footage%20from%20"spacious"%20→%20C)

## Question 37

You need to locate all migration script files across a large monorepo. These files follow the naming convention YYYYMMDD_description.sql and are distributed across multiple service directories at various nesting levels. Which built-in tool is the most appropriate choice for this task?

- **A.** Grep, searching file contents for SQL migration keywords like CREATE TABLE or ALTER TABLE
- **B.** Bash, running a custom script that parses directory listings and filters by file extension
- **C.** Glob, using a pattern like **/*_*.sql to match migration file paths across all directories
- **D.** Read, loading each service directory to manually scan for migration files

**Answer:** [↳ Q37 in the answer key](Answer%20Key.md#Q37%20—%20Finding%20`YYYYMMDD_description.sql`%20across%20a%20monorepo%20→%20C)

## Question 38

When a web search subagent in a multi-agent research pipeline encounters a database timeout and returns the generic message "search unavailable" to the coordinator, what is the primary problem this creates?

- **A.** It prevents the coordinator from distinguishing between a transient timeout and a permanent access restriction, limiting its ability to choose an appropriate recovery strategy
- **B.** It causes the coordinator to immediately terminate the entire research workflow
- **C.** It causes all other subagents to halt their processing until the error is resolved
- **D.** It forces the coordinator to retry the same query indefinitely until the service becomes available

**Answer:** [↳ Q38 in the answer key](Answer%20Key.md#Q38%20—%20Subagent%20returns%20"search%20unavailable"%20to%20the%20coordinator%20→%20A)

## Question 39

While implementing the orchestrator agent's agentic loop for the research platform, a developer decides to check whether the assistant's response text contains the phrase "research complete" to determine when the loop should terminate. Why is this approach considered an anti-pattern?

- **A.** It prevents the model from generating tool_use content blocks during subsequent loop iterations
- **B.** It forces the model to always output the phrase before it can use any tools, adding unnecessary latency to every iteration
- **C.** It causes the API to return an error because response text cannot be inspected until the full conversation is complete
- **D.** It relies on parsing non-deterministic natural language output instead of using the reliable stop_reason field, which may produce inconsistent termination behavior

**Answer:** [↳ Q39 in the answer key](Answer%20Key.md#Q39%20—%20Terminating%20the%20loop%20on%20the%20phrase%20"research%20complete"%20→%20D)

## Question 40

In the research automation platform, the orchestrator agent runs an agentic loop that sends requests to Claude, receives responses, and decides whether to continue or stop. What mechanism does the loop use to determine whether it should execute another tool call or present the final response?

- **A.** The loop counts the number of tool calls made and stops after reaching a predefined maximum
- **B.** The system prompt includes a termination keyword that the model outputs when it has finished processing
- **C.** The model includes a boolean "continue" field in its JSON response body that the loop evaluates after each iteration
- **D.** The stop_reason field in the API response indicates "tool_use" when the model wants to call a tool and "end_turn" when it considers the task complete

**Answer:** [↳ Q40 in the answer key](Answer%20Key.md#Q40%20—%20What%20the%20agentic%20loop%20actually%20checks%20→%20D)

## Question 41

After the web search agent executes a tool and receives results during its agentic loop, the developer appends the tool results to the conversation history before sending the next API request. What is the primary purpose of including these results in the conversation?

- **A.** To enable the API to cache the tool results server-side for faster processing of subsequent requests
- **B.** To satisfy an API validation rule that requires strictly alternating message roles in the conversation array
- **C.** To allow the API to deduplicate repeated tool calls and reduce unnecessary computation
- **D.** To enable the model to incorporate the new information into its reasoning and determine the appropriate next action in the loop

**Answer:** [↳ Q41 in the answer key](Answer%20Key.md#Q41%20—%20Why%20append%20tool%20results%20to%20the%20conversation%20→%20D)

## Question 42

Within the research automation platform, the web search agent discovers a set of URLs that the document analysis agent needs to process. Which approach correctly follows the hub-and-spoke orchestration pattern?

- **A.** The coordinator receives the URLs from the web search agent and includes them in the prompt when delegating work to the document analysis agent
- **B.** The web search agent invokes the document analysis agent directly through a peer-to-peer call, passing the URLs without coordinator involvement
- **C.** The web search agent writes URLs to a shared memory store that the document analysis agent reads concurrently during its own execution
- **D.** Both agents independently poll a shared message queue where the web search agent deposits URLs for the document analysis agent to consume

**Answer:** [↳ Q42 in the answer key](Answer%20Key.md#Q42%20—%20Hub-and-spoke%20handoff%20of%20discovered%20URLs%20→%20A)

## Question 43

After the coordinator collects web search results, it invokes the findings synthesis agent to combine all research. However, the synthesis output shows no awareness of the previously gathered search findings, even though they are clearly present in the coordinator's conversation history. What is the most likely cause?

- **A.** The coordinator's context window exceeded its limit, causing the search results to be silently dropped before the synthesis agent was invoked
- **B.** The search results were returned in an encoding format that the synthesis agent cannot process
- **C.** The synthesis agent's system prompt contains an instruction that explicitly excludes externally sourced data
- **D.** Subagents do not automatically inherit the coordinator's conversation history, so the search findings were never part of the synthesis agent's context

**Answer:** [↳ Q43 in the answer key](Answer%20Key.md#Q43%20—%20Synthesis%20output%20oblivious%20to%20findings%20in%20the%20coordinator's%20history%20→%20D)

## Question 44

A user submits a simple factual question to the research automation platform that can be fully answered with a single web lookup. The coordinator has access to the web search, document analysis, synthesis, and report generation subagents. How should the coordinator handle this query differently than a complex multi-faceted research topic?

- **A.** Forward the question to the user interface without involving any subagents since it is a simple query
- **B.** Route the query to the synthesis agent first so it can assess whether additional subagents are needed
- **C.** Always invoke the full pipeline of all four subagents to ensure consistent and thorough output regardless of query complexity
- **D.** Analyze the query requirements and invoke only the web search agent, skipping document analysis, synthesis, and report generation when they are unnecessary

**Answer:** [↳ Q44 in the answer key](Answer%20Key.md#Q44%20—%20Simple%20factual%20question%20hitting%20a%20four-agent%20pipeline%20→%20D)

## Question 45

The coordinator agent in the research platform is about to invoke the findings synthesis agent using a stateless agent call (so subagents do not inherit prior conversation context). Both the web search agent and the document analysis agent have already returned their results. How should the coordinator provide these prior findings to the synthesis agent?

- **A.** Pass only a brief thematic summary to keep the synthesis agent's context lean and avoid exceeding token limits
- **B.** Include the complete findings from both agents directly in the synthesis agent's prompt so it has full access to all gathered information
- **C.** Store the findings in an external database and give the synthesis agent credentials to query the results on its own
- **D.** Instruct the synthesis agent to re-invoke the web search and document analysis agents independently to collect the information it needs

**Answer:** [↳ Q45 in the answer key](Answer%20Key.md#Q45%20—%20Getting%20prior%20findings%20into%20a%20stateless%20synthesis%20call%20→%20B)

## Question 46

The document analysis agent returns its findings as narrative text that blends source citations into flowing paragraphs. When the coordinator passes these results to the synthesis agent, the final report frequently contains misattributed or missing source references. Which change to the inter-agent data format would best address this problem?

- **A.** Instruct the synthesis agent to search the original documents again to independently verify all citations before generating the report
- **B.** Have the document analysis agent return findings in a structured format that separates each claim from its metadata, including source URLs, document names, and page numbers
- **C.** Add a post-processing regular expression step that extracts citations from the narrative paragraphs after synthesis is complete
- **D.** Increase the synthesis agent's context window budget so it can process longer narrative passages without losing citation details

**Answer:** [↳ Q46 in the answer key](Answer%20Key.md#Q46%20—%20Citations%20misattributed%20after%20narrative%20handoff%20→%20B)

## Question 47

In the research platform, the report generation agent must only produce a final report after the synthesis agent has confirmed that all research areas have adequate coverage. A developer enforces this ordering with a system prompt instruction telling the report agent to wait for synthesis confirmation. Why is a programmatic prerequisite gate a better choice?

- **A.** Programmatic gates run faster than prompt-based instructions, reducing overall pipeline latency
- **B.** Prompt instructions are only evaluated at the start of a session and are ignored during subsequent tool calls
- **C.** Programmatic prerequisite gates provide deterministic enforcement, whereas prompt-based instructions have a non-zero failure rate and cannot guarantee compliance
- **D.** System prompts cannot reference other agents, so the report agent has no way to know the synthesis step exists

**Answer:** [↳ Q47 in the answer key](Answer%20Key.md#Q47%20—%20Prompt-enforced%20ordering%20vs%20a%20programmatic%20gate%20→%20C)

## Question 48

A user sends a single request to the research platform asking it to investigate three distinct subtopics: market size projections, regulatory developments, and competitive landscape. Which strategy should the coordinator agent use to handle this multi-faceted request efficiently?

- **A.** Forward the entire request to the web search agent and have it address all three subtopics sequentially within a single invocation
- **B.** Decompose the request into three distinct research items, delegate each to appropriate subagents for parallel investigation, then synthesize the results into a unified response
- **C.** Route the full request to the synthesis agent, which determines what information it needs and delegates research accordingly
- **D.** Ask the user to resubmit the request as three separate queries so each can be routed to the correct subagent independently

**Answer:** [↳ Q48 in the answer key](Answer%20Key.md#Q48%20—%20One%20request,%20three%20distinct%20subtopics%20→%20B)

## Question 49

A customer support agent calls the process_refund MCP tool, which fails because the requested refund amount exceeds the customer's original order total. Currently the tool returns a generic "Operation failed" message, preventing the agent from explaining the issue to the customer. How should the tool's error response be restructured to enable appropriate handling?

- **A.** Return an error with errorCategory: "transient", isRetryable: true, and a description suggesting the agent retry with the same parameters after a delay
- **B.** Return a successful empty result with a warning field embedded in the response content indicating the amount was too high
- **C.** Return an error with errorCategory: "permission", isRetryable: true, and a description indicating the operation requires supervisor-level access
- **D.** Return an error with errorCategory: "validation", isRetryable: false, and a description explaining the refund amount exceeds the order total

**Answer:** [↳ Q49 in the answer key](Answer%20Key.md#Q49%20—%20🔶%20Refund%20exceeds%20the%20order%20total%20→%20D)

## Question 50

Your team's project-level CLAUDE.md has grown to over 500 lines, covering testing conventions, API design standards, deployment procedures, and security policies. Engineers find it difficult to maintain, and Claude Code occasionally overlooks relevant guidelines. What is the recommended approach to improve organization and reliability of these configuration instructions?

- **A.** Move all content to user-level ~/.claude/CLAUDE.md so each engineer can maintain a personal copy of the instructions
- **B.** Duplicate the full CLAUDE.md into every subdirectory of the project to ensure Claude Code always finds nearby instructions
- **C.** Split the content into focused topic-specific files in .claude/rules/, such as testing.md, api-conventions.md, and deployment.md
- **D.** Consolidate all guidelines into the system prompt configuration of the project's MCP servers

**Answer:** [↳ Q50 in the answer key](Answer%20Key.md#Q50%20—%20500-line%20`CLAUDE.md`%20nobody%20can%20maintain%20→%20C)

## Question 51

A new engineer on the team reports that Claude Code is not following the project's API naming conventions during code generation, even though other engineers on the same repository see the conventions applied correctly. What is the most effective first diagnostic step to identify the cause of this inconsistency?

- **A.** Move all project configuration from .claude/rules/ into a single root-level CLAUDE.md to simplify the file structure
- **B.** Reinstall Claude Code on the engineer's machine to clear any corrupted cached configurations
- **C.** Ask the engineer to run the /memory command to verify which memory files are currently loaded in their session
- **D.** Add the API naming conventions as inline comments in every source file so Claude Code reads them directly

**Answer:** [↳ Q51 in the answer key](Answer%20Key.md#Q51%20—%20🔶%20One%20engineer's%20conventions%20not%20applying%20→%20C)

## Question 52

Your team wants to enforce a universal rule that all generated TypeScript code must use named exports instead of default exports. This standard should apply automatically every time Claude Code writes or modifies any TypeScript file in the project. Where should this convention be defined?

- **A.** In the project-level CLAUDE.md or a .claude/rules/ file so it is always loaded for every interaction
- **B.** In a user-scoped command under ~/.claude/commands/ that each developer runs at the start of their session
- **C.** In a custom slash command stored in .claude/commands/ that developers must remember to call before each task
- **D.** In a skill file under .claude/skills/ with a SKILL.md that engineers invoke on demand before writing code

**Answer:** [↳ Q52 in the answer key](Answer%20Key.md#Q52%20—%20A%20TypeScript%20convention%20that%20must%20always%20apply%20→%20A)

## Question 53

One of your engineers creates a slash command that scaffolds a new REST API endpoint with standardized error handling and validation. The team agrees this command should be available to all developers working on the project without requiring any manual setup on individual machines. Which configuration achieves this goal?

- **A.** Place the command file in the project's .claude/commands/ directory and commit it to version control
- **B.** Add the command as a SKILL.md file in ~/.claude/skills/ with argument-hint frontmatter configured
- **C.** Place the command file in ~/.claude/commands/ on the engineer's machine and share the file path in a wiki
- **D.** Define the command inline within the root CLAUDE.md file using @import syntax

**Answer:** [↳ Q53 in the answer key](Answer%20Key.md#Q53%20—%20Sharing%20a%20scaffolding%20slash%20command%20with%20the%20team%20→%20A)

## Question 54

Your project enforces specific conventions for all Jest test files, including fixture usage patterns and assertion styles. These test files are distributed across dozens of directories throughout the repository (e.g., src/components/, src/services/, src/utils/, lib/helpers/). You need these conventions to load automatically only when Claude Code edits a test file. Which configuration correctly implements this?

- **A.** Create a file in .claude/rules/ with YAML frontmatter containing paths: ["**/*.test.ts", "**/*.test.tsx"] to target test files across all directories
- **B.** Place the conventions in a user-level ~/.claude/CLAUDE.md file and instruct each developer to add them manually
- **C.** Add the testing conventions to the project-level CLAUDE.md so they are loaded during every interaction regardless of file type
- **D.** Create a CLAUDE.md file inside each directory that contains test files, repeating the conventions in every location

**Answer:** [↳ Q54 in the answer key](Answer%20Key.md#Q54%20—%20Jest%20conventions%20loading%20only%20when%20editing%20test%20files%20→%20A)

## Question 55

Your repository contains Python database migration files in three unrelated directories: db/migrations/, services/auth/migrations/, and tools/data/migrations/. All migration files must follow identical conventions for transaction handling and rollback patterns. What is the most maintainable approach to enforce these conventions?

- **A.** Place a CLAUDE.md file with identical content in each of the three migration directories
- **B.** Create a shared migration-rules.md file and use @import in three separate subdirectory CLAUDE.md files to reference it
- **C.** Add all migration conventions to the root CLAUDE.md so they are always available, even when editing non-migration files
- **D.** Create a single file in .claude/rules/ with a glob pattern like globs: ["**/migrations/**/*.py"] that matches migration files across all locations

**Answer:** [↳ Q55 in the answer key](Answer%20Key.md#Q55%20—%20🔶%20Migration%20conventions%20in%20three%20unrelated%20directories%20→%20D)

## Question 56

Your team must replace the project's logging library across 60+ source files. The migration involves auditing current usage patterns, selecting appropriate replacement APIs for different log levels, and applying consistent changes. What is the recommended workflow for completing this task in Claude Code?

- **A.** Start 60 separate Claude Code sessions in parallel, one per file, each using direct execution independently
- **B.** Use plan mode for the full duration of the migration, including both the investigation phase and every individual file modification
- **C.** Start with plan mode to audit usage patterns and design the migration strategy, then switch to direct execution to apply the changes according to the plan
- **D.** Use direct execution for the entire migration, processing each file one at a time without any upfront investigation

**Answer:** [↳ Q56 in the answer key](Answer%20Key.md#Q56%20—%20Logging-library%20migration%20across%2060+%20files%20→%20C)

## Question 57

An engineer asks Claude Code to fix a bug where a single function throws an error when receiving a null input. The stack trace clearly identifies the file and line number, and the fix requires adding one conditional check. Which mode should the engineer use for this task?

- **A.** Plan mode, because every code change benefits from an investigation phase before implementation
- **B.** Direct execution combined with the Explore subagent to verify no other functions have the same issue before making the change
- **C.** Plan mode, because null handling requires evaluating multiple valid implementation strategies across the codebase
- **D.** Direct execution, because the task is well-scoped with a clear fix in a single file and does not require architectural exploration

**Answer:** [↳ Q57 in the answer key](Answer%20Key.md#Q57%20—%20One-line%20null%20check,%20stack%20trace%20already%20points%20at%20it%20→%20D)

## Question 58

Your team asks Claude Code to transform legacy configuration files from an INI format into YAML. Despite detailed prose instructions describing the mapping rules, Claude Code produces inconsistent key naming and indentation across different files. What is the most effective technique to resolve this inconsistency?

- **A.** Increase the length of the prose instructions by adding more detailed paragraphs explaining each mapping rule
- **B.** Add a general instruction in CLAUDE.md that says "always be consistent when transforming configuration formats"
- **C.** Provide 2-3 concrete input/output examples showing the exact transformation from specific INI sections to the expected YAML output
- **D.** Switch to plan mode and ask Claude Code to outline its transformation approach before applying any changes

**Answer:** [↳ Q58 in the answer key](Answer%20Key.md#Q58%20—%20Inconsistent%20INI%20→%20YAML%20transformations%20→%20C)

## Question 59

Your team is using Claude Code to implement a new CSV parsing module that must handle complex edge cases, including quoted delimiters, multiline fields, and malformed rows. Requirements are well-defined but numerous. Which approach best supports iterative refinement of the implementation?

- **A.** Generate the implementation with Claude Code, manually test each edge case by running the code, and file separate bug reports for each failure
- **B.** Describe all edge cases in a single prompt and request that Claude Code generate both the implementation and tests in one pass
- **C.** Implement the module first without tests, then ask Claude Code to review its own output for issues in the same session
- **D.** Write a comprehensive test suite covering expected behavior, edge cases, and malformed input handling first, then iterate by sharing test failures with Claude Code to guide corrections

**Answer:** [↳ Q59 in the answer key](Answer%20Key.md#Q59%20—%20Iterative%20refinement%20of%20a%20CSV%20parser%20with%20many%20edge%20cases%20→%20D)

## Question 60

While updating a large configuration file, Claude Code's Edit tool fails with an error indicating the target text appears in multiple locations within the file. The specific section that needs modification contains boilerplate text identical to several other sections. What is the recommended fallback approach?

- **A.** Split the configuration file into smaller files so that each section contains unique text for Edit to match
- **B.** Use Bash to run a sed command that targets the specific line number for replacement
- **C.** Retry the Edit tool with a larger context window setting to improve text matching precision
- **D.** Use Read to load the full file contents, apply the modification, and then use Write to save the complete updated file

**Answer:** [↳ Q60 in the answer key](Answer%20Key.md#Q60%20—%20🔶%20`Edit`%20fails%20because%20the%20target%20text%20isn't%20unique%20→%20D)
