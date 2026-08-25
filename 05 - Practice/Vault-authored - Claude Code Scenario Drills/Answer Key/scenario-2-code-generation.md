---
tags:
  - CCA-F
  - practice-exam
  - answer-key
  - domain-3
  - claude-code
  - code-generation
date: 2026-08-25
status: done
---

# Scenario 2 — Code Generation with Claude Code · Answer Key

**Q1–Q10.** Questions: [../Questions.md](../Questions.md) · index: [../README.md](../README.md) · domain note: [[D3 - Claude Code Configuration & Workflows]]

Primary domains per the official guide: **D3 (Claude Code Configuration & Workflows)** and **D5 (Context Management & Reliability)**.

> [!NOTE] Vault-authored, not from a bank
> Every item is derived from the exam guide's own task statements and verified against `D3`. See [../README.md](../README.md) § Provenance.

---

## Q1 — New engineer doesn't get the team's conventions → **B**

**B.** The conventions live in the tech lead's `~/.claude/CLAUDE.md` — user-level, not shared via version control.

**Why B wins.** The guide's hierarchy is **user-level** (`~/.claude/CLAUDE.md`) → **project-level** (`CLAUDE.md` at root or `.claude/CLAUDE.md`) → **directory-level** (subdirectory `CLAUDE.md`). Of these, only the project and directory files are committed and travel with the repository. *(Docs add two scopes the guide omits: a **managed-policy** scope above user level, and `CLAUDE.local.md`, which is project-scoped but gitignored — so "in the repo" and "project-scoped" aren't quite synonyms.)* The stem's tell is *"worked ever since"* for the author and never for anyone else — that asymmetry is the signature of user-scoped configuration.

| Distractor | Why it fails |
|---|---|
| **A** run `/init` | `/init` generates a starter `CLAUDE.md`; it doesn't activate configuration that exists elsewhere. Nothing in the stem says the project file is missing |
| **C** size truncation | The mechanism doesn't exist as described — docs say Claude Code loads a `CLAUDE.md` up to 4 MiB **in full and skips a larger one**; there is no silent truncation. And either way, a file-level problem can't affect one engineer and spare the rest — the symptom is per-user, not per-file |
| **D** directory-level needs a first visit | Loosely **describes real behavior** — files above the working directory load at launch, while subdirectory files load **on demand** when Claude reads files there. But lazy loading works identically for every teammate, so it cannot explain why one engineer alone is affected |

**Takeaway.** *"Works for me, not for the team"* → check user scope before anything else. Note D is true-but-irrelevant rather than false: the discriminator is the **asymmetry**, not the loading model. Exam guide: task statement 3.1, *"diagnosing configuration hierarchy issues"*. See [[D3 - Claude Code Configuration & Workflows]] §3.1 · [[EP10 - CLAUDE.md Hierarchy & Config Rules]].

---

## Q2 — Diagnosing inconsistent behavior across projects → **C**

**C.** Run `/memory`.

**Why C wins on the exam.** The guide names `/memory` for this under 3.1: *"using the `/memory` command to verify which memory files are loaded and diagnose inconsistent behavior across sessions."* That is the wording the exam is written against. *(In current docs `/memory` actually lists memory-file **locations**, including files that don't exist yet — see the callout below.)*

| Distractor | Why it fails |
|---|---|
| **A** `/context` | Real, and in **current docs it does list loaded memory files** — but the exam guide names `/memory` for this task under 3.1 and never mentions `/context` at all. See the docs-drift callout below |
| **B** `/compact` | A context-reduction tool. Destroys evidence rather than gathering it, and the stem says *before changing any configuration* |
| **D** add `@import` | A change, not a diagnosis — and you don't yet know what to import |

> [!WARNING] Guide-current vs docs-current — `/context` genuinely does this now
> The exam guide (2026-07) names **`/memory`** for verifying loaded memory files, and `/context` appears nowhere in it. Current Claude Code docs prescribe **`/context`** for exactly this check — the memory page says to run it and read the **Memory files** list, including under its *"Claude isn't following my CLAUDE.md"* troubleshooting entry. It also bounds `/memory` the other way: `/memory` lists memory file *locations* including files that don't exist yet, while `/context` shows what actually **loaded into this session**.
> **Exam answer: `/memory`.** Real debugging: reach for `/context`.
> Source: https://code.claude.com/docs/en/memory · checked 2026-08-25

**Takeaway.** For the exam: `/memory` = the named diagnostic. `/compact` = shrink what is loaded. In production, `/context` is the one that tells you what actually loaded. See [[D3 - Claude Code Configuration & Workflows]] §3.1.

---

## Q3 — Monorepo, eight packages, standards files already exist → **B**

**B.** `@import` in each package's `CLAUDE.md`, pulling in only the relevant standards files.

**Why B wins.** `@import` exists to keep `CLAUDE.md` **modular** — referencing external files instead of inlining them. The stem supplies both preconditions the exam guide names: the standards files already exist, and *"each maintainer knows which standards apply"* — maintainer domain knowledge driving selective inclusion.

| Distractor | Why it fails |
|---|---|
| **A** reorganize under headers | Leaves every package loading every other package's standards. Headers organize prose; they don't scope loading |
| **C** move to `.claude/skills/` | Skills are **on-demand, task-specific**. Coding standards are **always-loaded, universal-within-scope** — that's the `CLAUDE.md` side of the skills-vs-`CLAUDE.md` split |
| **D** duplicate inline | Eight copies of drifting standards. Directly against what modularity is for |

**Takeaway.** Content exists in files + you need selective inclusion → `@import`. See [[D3 - Claude Code Configuration & Workflows]] §3.1.

---

## Q4 — One `CLAUDE.md` covering three unrelated topics → **A**

**A.** Split into topic-specific files in `.claude/rules/`.

**Why A wins.** `.claude/rules/` is the exam guide's named alternative to a monolithic `CLAUDE.md` — its own example is *"`testing.md`, `api-conventions.md`, `deployment.md`"*, which is the stem's three topics exactly.

| Distractor | Why it fails |
|---|---|
| **B** `.claude/commands/` | That directory holds **slash commands** — user-invoked actions, not standing conventions |
| **C** per-subdirectory `CLAUDE.md` | Works only when a topic maps cleanly to a directory. Testing conventions and deployment procedures don't |
| **D** last-loaded wins | Fabricated. The hierarchy **layers**; it doesn't overwrite |

> [!TIP] Distinguish Q4 from Q6 in the official sample set
> Both answer `.claude/rules/`, for different reasons. **Here** it's *organization* — one file has too many topics. **There** it's *conditional activation* — the `paths:` glob loads a rule only when you touch a matching file. Rules do both; the stem tells you which is being tested.

**Takeaway.** See [[D3 - Claude Code Configuration & Workflows]] §3.1 and §3.3.

---

## Q5 — Skill floods the main conversation → **B**

**B.** `context: fork`.

**Why B wins.** `context: fork` runs the skill in an **isolated sub-agent context**, so its output doesn't pollute the main conversation. The exam guide names verbose codebase analysis as the canonical case.

| Distractor | Why it fails |
|---|---|
| **A** `allowed-tools` | Governs **tool permissions**, not where output lands. Even on the guide's reading, narrowing tools doesn't stop a permitted `Grep` returning thousands of lines |
| **C** `argument-hint` | Concerns invocation arguments, not output volume. *(It shows an autocomplete hint — it does not actually prompt, despite the guide's wording. See the Q7 callout.)* |
| **D** `model: haiku` | Changes who does the work, not where the transcript accumulates. Verbose output is verbose regardless of model |

**Takeaway.** Three exam-named frontmatter options, three distinct jobs: `context: fork` = isolation · `allowed-tools` = tool permissions · `argument-hint` = invocation UX. The guide lists these as examples, **not** as the complete field set — see the Q6 drift callout. See [[D3 - Claude Code Configuration & Workflows]] §3.2 · [[EP11 - Custom Slash Commands & Skills]].

---

## Q6 — Restricting a skill's tool access → **D**

**D.** `allowed-tools` in the skill frontmatter.

**Why D wins on the exam.** The exam guide's Domain 3 skill bullet names it directly: *"configuring `allowed-tools` in skill frontmatter to restrict tool access during skill execution (e.g., limiting to file write operations to prevent destructive actions)"* — the stem restated. Configuration beats an instruction in the body.

| Distractor | Why it fails |
|---|---|
| **A** `PreToolUse` hook | In the guide's vocabulary, hooks belong to **Domain 1** (`PostToolUse`, "tool call interception") and `PreToolUse` never appears at all — so it is not what Domain 3 is testing here. **But see the drift callout: in shipping Claude Code this is a genuinely valid, and arguably better, answer.** |
| **B** a `[!WARNING]` in the body | Prompt-layer. Shifts probability; guarantees nothing — the one rebuttal here that is solid in both the guide and the docs |
| **C** `context: fork` | As written in the stem — bare `context: fork`, no `agent:` — it isolates **context** while leaving `Bash` callable, so the migration can still drop a table. *(Not a blanket rule: pairing `context: fork` with an `agent:` does scope the execution environment's model, tools and permissions — see the callout.)* |

> [!WARNING] Guide-current vs docs-current — this item's premise has drifted badly
> Three things the guide's framing gets wrong about shipping Claude Code, all verified against current docs — and all easy to absorb as fact if you only read the guide:
> 1. **`hooks` *is* a `SKILL.md` frontmatter field**, and all hook events are supported — the docs' worked example is a `PreToolUse` matcher on `Bash` that *runs a security validation script* (blocking then depends on the script: `PreToolUse` blocks on exit code 2). Option A is not a fabricated mechanism. **Caveat:** frontmatter hooks are registered *when the skill is invoked*, so they cannot gate that invocation — only the tool calls that follow.
> 2. **`allowed-tools` grants, it does not restrict.** Docs: it *"does not restrict which tools are available: every tool remains callable."* The field that removes tools is **`disallowed-tools`**, which the exam guide never mentions.
> 3. **The guide says frontmatter *"including"* `context: fork`, `allowed-tools`, `argument-hint`** — a non-exhaustive list, not a closed set of three. Real fields also include `model`, `disallowed-tools`, `hooks`, `paths`, `agent` and more.
>
> **Exam answer: `allowed-tools` (D)** — it is what the guide names. **Production answers**, in rough order of directness: **`disallowed-tools`** (removes tools from the pool), a **`PreToolUse` hook** (intercepts and can block), or **`context: fork` with an `agent:`** (scopes the forked environment's tools and permissions). Note too that `allowed-tools` grants are **turn-scoped** and a matching `ask`/`deny` permission rule still overrides them.
> Source: https://code.claude.com/docs/en/skills · https://code.claude.com/docs/en/hooks · checked 2026-08-25

**Takeaway.** The guide frames tool scoping as `allowed-tools`; answer that. But do not carry "a skill without `Bash` cannot call `Bash`" into real work — it is false. See [[D3 - Claude Code Configuration & Workflows]] §3.2.

---

## Q7 — Skill invoked without required parameters → **A**

**A.** `argument-hint` frontmatter.

**Why A wins on the exam.** The guide's skill bullet is the stem restated: *"using `argument-hint` frontmatter to prompt developers for required parameters when they invoke the skill without arguments."*

| Distractor | Why it fails |
|---|---|
| **B** required-parameters section in the body | The body is read **after** invocation, so it cannot surface anything at invocation time |
| **C** `context: fork` | Isolation, not parameter collection |
| **D** `PreToolUse` hook | Hooks *are* declarable in skill frontmatter (see Q6), and a `PreToolUse` hook matching `Skill` can even fire when Claude auto-invokes a skill. The decisive objection is **registration order**: a skill's own frontmatter hooks are registered *at* invocation, so they cannot gate the invocation that registers them. Blocking is also disproportionate to a missing argument |

> [!WARNING] Guide-current vs docs-current — `argument-hint` displays, it does not prompt
> Current docs define it as a *"hint shown during autocomplete to indicate expected arguments"* (e.g. `[issue-number]`). It does not prompt, block, or make an argument required — **no frontmatter field does**. A skill *body* can still ask (validate and fail loudly, or use `AskUserQuestion`); what doesn't exist is a declarative way to require an argument.
> **Exam answer: `argument-hint` (A).** In real skills, validate arguments in the body and fail loudly.
> Source: https://code.claude.com/docs/en/skills · checked 2026-08-25

**Takeaway.** See [[D3 - Claude Code Configuration & Workflows]] §3.2.

---

## Q8 — Personal variant of a shared skill → **B**

**B.** A variant under `~/.claude/skills/` **with a different name**.

**Why B wins.** User-scoped skills aren't version-controlled, so teammates are unaffected. The **different name** is part of the guide's own phrasing, and the reason is precedence rather than ambiguity: personal skills **override** project skills, so a same-named variant would silently shadow the team's version *for you* — you'd lose access to the shared skill without noticing.

| Distractor | Why it fails |
|---|---|
| **A** username branch in the shared file | Changes a version-controlled file everyone loads — the opposite of "without affecting teammates" |
| **C** stricter checklist in `~/.claude/CLAUDE.md` | User-scoped, so the isolation is right, but `CLAUDE.md` is **always-loaded** — you'd apply review rules to every task, not just reviews |
| **D** fork the repository | Wildly disproportionate, and creates a maintenance burden for a personal preference |

**Takeaway.** Personal customization → `~/.claude/`, new name. This is the same user-vs-project axis as Q1, read from the other end: what makes user scope a *bug* in Q1 makes it the *feature* here. See [[D3 - Claude Code Configuration & Workflows]] §3.2.

---

## Q9 — One-line null-date fix with a clear stack trace → **C**

**C.** Direct execution.

**Why C wins.** Direct execution is for **simple, well-scoped changes**. The exam guide's own examples are *"a single-file bug fix with a clear stack trace"* and *"adding a date validation conditional"* — the stem is both.

| Distractor | Why it fails |
|---|---|
| **A** plan mode | Plan mode is for large-scale changes, multiple valid approaches, architectural decisions, multi-file modifications. None apply |
| **B** plan then execute | A real and recommended combination — for a **library migration**, not a one-line fix. Disproportionate here |
| **D** `Explore` subagent | For **verbose discovery phases** that would exhaust context. The stack trace has already done the discovery |

> [!WARNING] The over-process trap
> Every distractor here is a *good practice applied at the wrong scale*. The exam tests proportionality in both directions: knowing when to reach for plan mode (official sample Q5) and knowing when not to. Cost of over-planning a one-liner is real.

**Takeaway.** See [[D3 - Claude Code Configuration & Workflows]] §3.4 · [[EP12 - Plan Mode vs Execute]].

---

## Q10 — Prose keeps being interpreted inconsistently → **C**

**C.** Provide 2–3 concrete input/output example pairs.

**Why C wins.** The exam guide states it directly: concrete input/output examples are *"the most effective way to communicate expected transformations when prose descriptions are interpreted inconsistently."* The stem confirms the requirement is stable and the description is the failing part.

| Distractor | Why it fails |
|---|---|
| **A** restate more precisely + "be careful" | More prose is the thing that already failed three times. General exhortations don't improve precision — the same principle that sinks *"be conservative"* in Q16 |
| **B** move to `CLAUDE.md` | Changes **how often** the description loads, not **how well** it communicates. An ambiguous instruction loaded always is still ambiguous |
| **D** spec-then-implement | Adds a step that inherits the same ambiguity — the spec is written from the prose that isn't landing |

**Takeaway.** Prose interpreted inconsistently → show, don't tell. Task statement 3.5 pairs this with test-driven iteration and the interview pattern — three named refinement techniques worth knowing by name. See [[D3 - Claude Code Configuration & Workflows]] §3.5.

---

## Pattern summary — Q1–Q10

| Principle | Questions |
|---|---|
| User scope vs project scope is a **version-control** question | 1, 8 |
| Diagnose before configuring; know which command answers which question | 2 |
| `@import` = modular inclusion · `.claude/rules/` = topic split + path scoping | 3, 4 |
| The three `SKILL.md` frontmatter fields do three different jobs | 5, 6, 7 |
| Guarantees come from configuration, never from prose | 6 |
| Proportionality — over-process is as wrong as under-process | 9 |
| Examples beat more prose | 10 |

*Next: [scenario-5-continuous-integration.md](scenario-5-continuous-integration.md)*
