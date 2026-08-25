---
tags:
  - CCA-F
  - blueprint
  - roadmap
  - certification
date: 2026-08-25
status: done
---

# 📋 Official Exam Blueprint

> [!IMPORTANT] This note outranks every other note in the vault on questions about the exam
> Transcribed from the **official Claude Certified Architect – Foundations Exam Guide, v1.0, effective July 2026** (Anthropic Partner Academy). Where any vault note disagrees with this one about *what is tested, how it is weighted, or how the exam is run*, **this note wins** — then fix the other note.
>
> The scope boundary matters: this guide is authoritative on **the exam**. Official Anthropic **docs** remain authoritative on **technical fact**. See `AGENTS.md` § Authority hierarchy.

**Back to:** [[00 - START HERE]] · [[CCA-F Study Roadmap]]
**Drill the official questions:** [Exam Guide - Sample Questions](../05%20-%20Practice/Exam%20Guide%20-%20Sample%20Questions/README.md)

---

## 1 · Exam facts

| | |
| --- | --- |
| **Credential** | Claude Certified Architect – Foundations |
| **Exam code** | **CCAR-F** |
| **Items** | **60** |
| **Item format** | Multiple-choice **and multiple-response** — each item states how many responses to select |
| **Structure** | **4 scenarios, drawn at random from a bank of 6** |
| **Time limit** | **120 minutes** |
| **Delivery** | Proctored: online-proctored and/or test center, *per program policy*. §11 names **Pearson VUE** as the delivery partner |
| **Registration** | Anthropic Partner Academy → Pearson VUE account |
| **Passing score** | **720** scaled, on a **100–1,000** scale |
| **Fee** | $125 USD (partner-tier discounts apply at checkout) |
| **Validity** | **12 months** from the award date |
| **Result reporting** | Pass/fail + scaled score, **plus percent-correct by domain** |

> [!NOTE] How scoring actually works
> The exam is **criterion-referenced**: you are measured against a fixed standard set by a formal standard-setting study, **not** graded on a curve against other candidates. Scaled scoring equates forms of slightly different difficulty.
> **Per-domain percentages appear on your score report but do not determine pass/fail** — only the total scaled score does. Don't budget your time as if each domain had its own cut score.

### Logistics that bite

| Rule | Detail |
| --- | --- |
| **Retakes** | **14 days** after the 1st fail · **30** after the 2nd · **90** after the 3rd. Max **4 attempts per rolling 12 months**, per exam. Fee applies each time |
| **Reschedule / cancel** | Free up to **24 hours** before. Inside 24 hours → fee forfeited |
| **No-show / late arrival** | Fee forfeited, must re-register |
| **ID** | Valid, unexpired, government-issued **photo** ID; name must match the registration **exactly**. Name corrections → `certifications-support@anthropic.com`, *before* scheduling |
| **Accommodations** | Requested and **approved by Pearson VUE before you schedule**. Do not book until approved |
| **NDA** | A confidentiality agreement is accepted before the exam starts. Decline it → session ends, no refund |
| **Renewal** | Free, **non-proctored** assessment on the Partner Academy while still valid. **Let it lapse → full exam, full fee.** Anthropic may require a full retake instead of the renewal assessment if exam content changes significantly |
| **Appeals** | Within **14 days**, via Pearson VUE. The standard-setting outcome and individual item content are **not** appealable |

> [!WARNING] Exam-day prohibitions
> No notes, books, phones, smart watches, headphones, secondary monitors, or recording devices. Stay in webcam view throughout if testing online. Reproducing exam content in any form can revoke the credential and ban future attempts.

---

## 2 · Domain weights

| # | Domain | Weight | ≈ items of 60 |
| --- | --- | --- | --- |
| 1 | [[D1 - Agentic Architecture & Orchestration]] | **27%** | ~16 |
| 2 | [[D2 - Tool Design & MCP Integration]] | **18%** | ~11 |
| 3 | [[D3 - Claude Code Configuration & Workflows]] | **20%** | ~12 |
| 4 | [[D4 - Prompt Engineering & Structured Output]] | **20%** | ~12 |
| 5 | [[D5 - Context Management & Reliability]] | **15%** | ~9 |

*Item counts are derived, not official — the guide states percentages of scored items.*

> [!TIP] What the weights should change about your study plan
> **D1 + D3 = 47%.** D1 is the single heaviest domain and D3 is joint-second — yet D3 is the vault's thinnest practice coverage (see §3). D5, the domain that *feels* hardest, is the lightest at 15%.

---

## 3 · The six scenarios

The exam presents **4 of these 6**, chosen at random. Each frames a set of questions.

| # | Scenario | Primary domains | Vault practice coverage |
| --- | --- | --- | --- |
| 1 | **Customer Support Resolution Agent** — Agent SDK agent for returns, billing disputes, account issues; MCP tools `get_customer`, `lookup_order`, `process_refund`, `escalate_to_human`; target 80%+ first-contact resolution | D1, D2, D5 | ✅ official Q1–Q3 · ✅ `customer_support` in all three CyberSkill sittings |
| 2 | **Code Generation with Claude Code** — team uses Claude Code for generation, refactoring, debugging, docs; custom slash commands, `CLAUDE.md` config, plan mode vs direct execution | D3, D5 | ✅ official Q4–Q6 · ❌ no third-party · ⚠️ vault-authored drills Q1–Q10 |
| 3 | **Multi-Agent Research System** — coordinator delegating to search / document-analysis / synthesis / report subagents; produces cited reports | D1, D2, D5 | ✅ official Q7–Q9 · ✅ `research_pipeline` in all three sittings |
| 4 | **Developer Productivity with Claude** — Agent SDK agent for exploring unfamiliar codebases, legacy systems, boilerplate; built-in tools (`Read`, `Write`, `Bash`, `Grep`, `Glob`) + MCP servers | D2, D3, D1 | ❌ **no official sample question** · ✅ `code_exploration` in all three sittings |
| 5 | **Claude Code for Continuous Integration** — CI/CD pipeline running automated code review, test generation, PR feedback; prompts that give actionable feedback and minimize false positives | D3, D4 | ✅ official Q10–Q12 · ❌ no third-party · ⚠️ vault-authored drills Q11–Q20 |
| 6 | **Structured Data Extraction** — extraction from unstructured documents, JSON-schema validation, high accuracy, graceful edge cases, downstream integration | D4, D5 | ❌ **no official sample question** · ✅ `extraction_pipeline` in all three sittings |

*"Official" = the guide's own [sample questions](../05%20-%20Practice/Exam%20Guide%20-%20Sample%20Questions/README.md), which cover four of the six scenarios. "Third-party" = the 240 CyberSkill / Question Bank items.*

> [!WARNING] The vault's biggest practice blind spot — and the odds
> All 240 **third-party** practice questions map to scenarios **1, 3, 4, 6**. No third-party bank tests scenarios **2** or **5**, the two Claude-Code-centric frames — the official guide supplies just 3 questions each for them, so drill volume there is thin rather than absent.
> With 4 of 6 drawn at random: **P(at least one of 2 or 5 appears) = 14/15 ≈ 93%**. **P(both appear) = 6/15 = 40%.** The only sitting that avoids them entirely is the single draw {1, 3, 4, 6} — 1 chance in 15.
> Mitigation: [[D3 - Claude Code Configuration & Workflows]], [[06-claude-code-operations]], the four Claude Code episodes ([[EP10 - CLAUDE.md Hierarchy & Config Rules]] · [[EP11 - Custom Slash Commands & Skills]] · [[EP12 - Plan Mode vs Execute]] · [[EP13 - Claude Code CI-CD Pipelines]]), and the [vault-authored drill set](../05%20-%20Practice/Vault-authored%20-%20Claude%20Code%20Scenario%20Drills/README.md).

---

## 4 · The 30 task statements

Every exam item is written against one of these. The vault's `03 - Domains/` notes mirror them **1:1** — heading `## N.M` in `D<N>` is task statement `N.M`.

> [!NOTE] Titles only — the knowledge and skill bullets are not transcribed here
> Each task statement in the source guide carries a *Knowledge of* and a *Skills in* list, often 3–6 bullets each. Those are **not** reproduced in this note; the `D1`–`D5` notes cover the same ground in the vault's own words. Where a practice answer key quotes the guide directly, it cites *the exam guide*, not this note — so don't expect to grep those quotes here.

### Domain 1 — Agentic Architecture & Orchestration (27%)

| # | Task statement | Note |
| --- | --- | --- |
| 1.1 | Design and implement agentic loops for autonomous task execution | [[D1 - Agentic Architecture & Orchestration]] §1.1 |
| 1.2 | Orchestrate multi-agent systems with coordinator-subagent patterns | §1.2 |
| 1.3 | Configure subagent invocation, context passing, and spawning | §1.3 |
| 1.4 | Implement multi-step workflows with enforcement and handoff patterns | §1.4 |
| 1.5 | Apply Agent SDK hooks for tool call interception and data normalization | §1.5 |
| 1.6 | Design task decomposition strategies for complex workflows | §1.6 |
| 1.7 | Manage session state, resumption, and forking | §1.7 |

### Domain 2 — Tool Design & MCP Integration (18%)

| # | Task statement | Note |
| --- | --- | --- |
| 2.1 | Design effective tool interfaces with clear descriptions and boundaries | [[D2 - Tool Design & MCP Integration]] §2.1 |
| 2.2 | Implement structured error responses for MCP tools | §2.2 |
| 2.3 | Distribute tools appropriately across agents and configure tool choice | §2.3 |
| 2.4 | Integrate MCP servers into Claude Code and agent workflows | §2.4 |
| 2.5 | Select and apply built-in tools (`Read`, `Write`, `Edit`, `Bash`, `Grep`, `Glob`) effectively | §2.5 |

### Domain 3 — Claude Code Configuration & Workflows (20%)

| # | Task statement | Note |
| --- | --- | --- |
| 3.1 | Configure `CLAUDE.md` files with appropriate hierarchy, scoping, and modular organization | [[D3 - Claude Code Configuration & Workflows]] §3.1 |
| 3.2 | Create and configure custom slash commands and skills | §3.2 |
| 3.3 | Apply path-specific rules for conditional convention loading | §3.3 |
| 3.4 | Determine when to use plan mode vs direct execution | §3.4 |
| 3.5 | Apply iterative refinement techniques for progressive improvement | §3.5 |
| 3.6 | Integrate Claude Code into CI/CD pipelines | §3.6 |

### Domain 4 — Prompt Engineering & Structured Output (20%)

| # | Task statement | Note |
| --- | --- | --- |
| 4.1 | Design prompts with explicit criteria to improve precision and reduce false positives | [[D4 - Prompt Engineering & Structured Output]] §4.1 |
| 4.2 | Apply few-shot prompting to improve output consistency and quality | §4.2 |
| 4.3 | Enforce structured output using tool use and JSON schemas | §4.3 |
| 4.4 | Implement validation, retry, and feedback loops for extraction quality | §4.4 |
| 4.5 | Design efficient batch processing strategies | §4.5 |
| 4.6 | Design multi-instance and multi-pass review architectures | §4.6 |

### Domain 5 — Context Management & Reliability (15%)

| # | Task statement | Note |
| --- | --- | --- |
| 5.1 | Manage conversation context to preserve critical information across long interactions | [[D5 - Context Management & Reliability]] §5.1 |
| 5.2 | Design effective escalation and ambiguity resolution patterns | §5.2 |
| 5.3 | Implement error propagation strategies across multi-agent systems | §5.3 |
| 5.4 | Manage context effectively in large codebase exploration | §5.4 |
| 5.5 | Design human review workflows and confidence calibration | §5.5 |
| 5.6 | Preserve information provenance and handle uncertainty in multi-source synthesis | §5.6 |

---

## 5 · In-scope checklist

The guide's own list of what is **explicitly tested**. Use it as a self-audit — if you can't explain a line, open the linked note.

- [ ] **Agentic loop implementation** — control flow on `stop_reason`, tool result handling, termination conditions → §1.1
- [ ] **Multi-agent orchestration** — coordinator-subagent, task decomposition, parallel subagents, iterative refinement loops → §1.2, §1.6
- [ ] **Subagent context management** — explicit context passing, structured state persistence, crash recovery via manifests → §1.3, §5.4
- [ ] **Tool interface design** — writing descriptions, splitting vs consolidating, naming to reduce ambiguity → §2.1
- [ ] **MCP tool and resource design** — resources for content catalogs, tools for actions, description quality for adoption → §2.4
- [ ] **MCP server configuration** — project vs user scope, env-var expansion, multi-server simultaneous access → §2.4
- [ ] **Error handling and propagation** — structured errors, transient vs business vs permission, local recovery before escalation → §2.2, §5.3
- [ ] **Escalation decision-making** — explicit criteria, honoring customer preferences, policy-gap identification → §5.2
- [ ] **`CLAUDE.md` configuration** — hierarchy (user/project/directory), `@import`, `.claude/rules/` with globs → §3.1
- [ ] **Custom commands and skills** — project vs user scope, `context: fork`, `allowed-tools`, `argument-hint` → §3.2
- [ ] **Plan mode vs direct execution** — complexity assessment, architectural decisions, single-file changes → §3.4
- [ ] **Iterative refinement** — input/output examples, test-driven iteration, interview pattern, sequential vs parallel issue resolution → §3.5
- [ ] **Structured output via `tool_use`** — schema design, `tool_choice` config, nullable fields to prevent hallucination → §4.3
- [ ] **Few-shot prompting** — ambiguous-scenario targeting, format consistency, false-positive reduction → §4.2
- [ ] **Batch processing** — Message Batches API appropriateness, latency tolerance, failure handling by `custom_id` → §4.5
- [ ] **Context window optimization** — trimming verbose tool outputs, structured fact extraction, position-aware input ordering → §5.1
- [ ] **Human review workflows** — confidence calibration, stratified sampling, accuracy segmentation by document type and field → §5.5
- [ ] **Information provenance** — claim-source mappings, temporal data, conflict annotation, coverage-gap reporting → §5.6

### Technologies and concepts named in the appendix

| Area | What the guide names |
| --- | --- |
| **Claude Agent SDK** | agent definitions, agentic loops, `stop_reason` handling, hooks (`PostToolUse`, tool call interception), subagent spawning via the **`Task`** tool, `allowedTools` |
| **MCP** | servers, tools, **resources**, `isError` flag, tool descriptions, tool distribution, `.mcp.json`, environment variable expansion |
| **Claude Code** | `CLAUDE.md` hierarchy (user/project/directory), `.claude/rules/` with YAML `paths` frontmatter, `.claude/commands/`, `.claude/skills/` with `SKILL.md` frontmatter (`context: fork`, `allowed-tools`, `argument-hint`), plan mode, direct execution, `/memory`, `/compact`, `--resume`, `fork_session`, **`Explore` subagent** |
| **Claude Code CLI** | `-p` / `--print`, `--output-format json`, `--json-schema` |
| **Claude API** | `tool_use` with JSON schemas, `tool_choice` (`"auto"` / `"any"` / forced), `stop_reason` values (**`"tool_use"`, `"end_turn"`**), `max_tokens`, system prompts |
| **Message Batches API** | 50% cost savings, up to 24-hour window, `custom_id` correlation, polling, **no multi-turn tool calling** |
| **JSON Schema** | required vs optional, enums, nullable fields, `"other"` + detail-string pattern, strict mode |
| **Pydantic** | schema validation, semantic validation errors, validation-retry loops |
| **Built-in tools** | `Read`, `Write`, `Edit`, `Bash`, `Grep`, `Glob` — purposes and selection criteria |
| **Techniques** | few-shot prompting, prompt chaining, context-window management (token budgets, progressive summarization, lost-in-the-middle, context extraction, scratchpad files), session management (resumption, `fork_session`, named sessions, **session context isolation**), confidence scoring (field-level, calibration with labeled sets, stratified sampling) |

> [!IMPORTANT] Two corrections the blueprint settles
> **`Task`, not `Agent`.** The guide names the **`Task`** tool for spawning subagents and requires `allowedTools` to include `"Task"` — stated three times (task statement 1.3, the appendix, and Exercise 4). *Docs-sourced aside, not from the guide:* the SDK renamed the tool to `Agent` in Claude Code v2.1.63, keeping `Task` as a backward-compatible alias — see [[D1 - Agentic Architecture & Orchestration]] §1.3 for the citation. **`Task` is the exam answer.**
> **Only two `stop_reason` values are on the syllabus** — `"tool_use"` and `"end_turn"`. The seven-value table in `D1` §1.1 is docs-accurate and matters in production, but `pause_turn`, `refusal` and the rest are **not** blueprint material.

---

## 6 · Out of scope

> [!TIP] Read this list before every revision session
> These topics **will not appear**. Time spent here scores zero.

- Fine-tuning Claude models or training custom models
- Claude API authentication, billing, or account management
- Detailed implementation of specific programming languages or frameworks — beyond what tool and schema configuration needs
- Deploying or hosting MCP servers (infrastructure, networking, container orchestration)
- Claude's internal architecture, training process, or model weights
- Constitutional AI, RLHF, or safety training methodologies
- Embedding models or vector database implementation details
- Computer use (browser automation, desktop interaction)
- Vision / image analysis capabilities
- Streaming API implementation or server-sent events
- Rate limiting, quotas, or API pricing calculations
- OAuth, API key rotation, or authentication protocol details
- Specific cloud provider configurations (AWS, GCP, Azure)
- Performance benchmarking or model comparison metrics
- **Prompt caching implementation details** — beyond knowing it exists
- Token counting algorithms or tokenization specifics

> [!WARNING] What this means for [[00 - Claude Model Family & API Fundamentals]]
> Three of its sections are out of scope: **The Claude Model Family (2026)** (model comparison), **Cost & Rate Limits: Team Sizing Reference** (pricing/quotas), and **Prompt Caching** (implementation detail). Keep them as background for real work; don't revise them for the exam. The rest of that note is in scope.

---

## 7 · Official preparation exercises

The guide prescribes four hands-on exercises. Each maps to domains and is worth doing once, in code, rather than read.

| # | Exercise | Build | Domains |
| --- | --- | --- | --- |
| **1** | **Multi-tool agent with escalation logic** | 3–4 MCP tools with detailed descriptions — **include two with similar functionality** to test selection · agentic loop branching on `stop_reason` · structured errors (`errorCategory`, `isRetryable`, human-readable description) · a hook that intercepts tool calls to block an over-threshold operation and redirect to escalation · multi-concern messages that must decompose, resolve each, and synthesize | D1, D2, D5 |
| **2** | **Claude Code for a team workflow** | Project-level `CLAUDE.md` · `.claude/rules/` files with `paths:` globs (`src/api/**/*`, `**/*.test.*`) and verify conditional loading · a `.claude/skills/` skill with `context: fork` + `allowed-tools` · an MCP server in `.mcp.json` with env-var expansion **plus** a personal one in `~/.claude.json`, both live at once · plan mode vs direct execution on a single-file fix, a multi-file migration, and an open-ended feature | D3, D2 |
| **3** | **Structured data extraction pipeline** | Extraction tool with required + optional fields, an enum with `"other"` + detail string, nullable fields — verify the model returns `null` instead of fabricating · validation-retry loop feeding the document + failed extraction + specific error · few-shot examples across varied document structures · batch of 100 via Message Batches API, failures re-submitted by `custom_id` with chunking, total time vs SLA · field-level confidence routing + accuracy by document type and field | D4, D5 |
| **4** | **Multi-agent research pipeline** | Coordinator with `"Task"` in `allowedTools` delegating to ≥2 subagents, each receiving findings **in its prompt** · parallel subagents via **multiple `Task` calls in one response** — measure the latency win · subagent output separating claim / evidence excerpt / source / publication date, preserved through synthesis · simulate a timeout and verify structured error context reaches the coordinator, which proceeds on partial results and annotates coverage gaps · conflicting statistics from two credible sources preserved with attribution, not silently resolved | D1, D2, D5 |

---

## ✅ Practice Checklist

- [ ] I can state the exam code, item count, time limit, and passing score without looking
- [ ] I know the exam draws **4 scenarios from a bank of 6** and can name all six
- [ ] I can rank the five domains by weight and know D1 + D3 = 47%
- [ ] I have drilled the [12 official sample questions](../05%20-%20Practice/Exam%20Guide%20-%20Sample%20Questions/README.md) and can explain **why each distractor fails**
- [ ] I have covered scenarios **2** and **5** — the two with no third-party practice coverage
- [ ] I can answer "which `stop_reason` values drive the loop?" with the **exam** answer, not the docs answer
- [ ] I know `Task` is the exam answer for the subagent-spawn tool
- [ ] I have read the out-of-scope list and stopped revising those topics
- [ ] I have done at least exercises **2** and **4** hands-on

---

*Next: [[CCA-F Study Roadmap]] — the 6-week plan that executes against this blueprint.*
