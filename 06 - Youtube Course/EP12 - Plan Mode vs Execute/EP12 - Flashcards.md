---
tags:
  - CCA-F
  - domain-3
  - plan-mode
  - iterative-refinement
  - youtube-course
  - flashcards
date: 2026-08-04
status: done
domain: "3 of 5"
source: "Peace Of Code — Claude Certified Architect Ep 12"
---

# 🃏 EP12 — Flashcards

> [!NOTE] How to Use This Deck
> Self-contained review for **EP12 — Plan Mode vs Execute** (Domain 3, task statements 3.4 and 3.5). Overlap with the vault-wide deck is intentional — this deck stands alone as a complete review of the episode.
> Cards marked **(docs)** carry a correction or expansion verified against official Anthropic documentation. The lecture's demo runs in **Cline**, not Claude Code, so none of the demo's mechanics are exam answers; it also overstates that Claude never acts without consent, misreads the default effort level, and undersells `Explore` as "just a prompt." Those cards give the verified behaviour.

**Related:** [[EP12 - Plan Mode vs Execute]] · [[D3 - Claude Code Configuration & Workflows]] · [[D5 - Context Management & Reliability]] · [[EP02 - Multi-Agent Systems & Coordinator Patterns]] · [[EP11 - Flashcards]]

---

## Domain 3 — Choosing the Mode

**Q: A developer asks Claude to refactor a 40,000-line coupled monolith into microservices. Ten minutes later imports are broken and no test passes. What is the root cause?**
A: Mode selection, not model error. In direct execution Claude can't hold the whole codebase in context, so it works file by file — committing edits from a partial view and hopping dependency to dependency. Early decisions get encoded before a better global approach becomes visible.

**Q: What single condition makes a task safe for direct execution?**
A: **The destination is known** — scope is clear, output is predictable, and a mistake is cheap to locate and fix. The work is delegation, not design.

**Q: Name the four plan-mode triggers, and state how many must fire.**
A: Touches more than one file non-trivially · multiple valid approaches · expensive to reverse · alters architectural shape. **Any one is sufficient.**

**Q: A mechanical variable rename spans 40 files. Which mode, and why does the file count mislead?**
A: **Direct execution.** The trigger is more than one file *non-trivially* — 40 files is one unambiguous decision applied 40 times. The exam's real signal is an ambiguity word (*coupled*, *interdependent*, *significant*) next to the number.

**Q: Why is a very long, example-rich direct-execution prompt not a substitute for plan mode?**
A: The limit isn't prompt quality, it's the context window. Claude still discovers file by file and edits as it goes. Plan mode changes *when* discovery happens relative to *when* edits happen.

**Q: You're starting a brand-new app with no existing code. Is plan mode pointless?**
A: No — on a greenfield project the **requirements** are what get investigated. Claude can then propose better foundations (more generic structure, fewer duplications, cleaner service boundaries), which are cheap now and expensive to retrofit.

**Q: Why is "choose plan mode or direct execution" the wrong framing?**
A: They're sequential, not exclusive — **plan is the bridge**. You never stop at a plan, so the shape is plan → approve → execute. Where no trigger fires, execute directly.

**Q: An exam option reads "run an autonomous migration script without user input." On what basis do you eliminate it?**
A: It removes the **approval gate**. The plan→approve→execute shape requires you to accept an approach before edits begin; an option that skips human approval can't be the plan-mode answer.

**Q: Why is "build extensive test suites for all services before any migration" the strongest distractor on the monolith question, and why is it still wrong?**
A: Because testing before migration is genuinely good practice. It's wrong because it puts test work *ahead of* planning work on a codebase whose dependency structure is still unknown — the better move is to plan the test suite **inside** plan mode. It's one step of the correct answer promoted to the whole answer.

---

## Domain 3 — Plan Mode Mechanics (Claude Code)

**Q: Give three ways to enter plan mode in Claude Code. (docs)**
A: `Shift+Tab` to cycle `default` → `acceptEdits` → `plan`; prefix a single prompt with `/plan`; or launch with `claude --permission-mode plan`. `"permissions": {"defaultMode": "plan"}` in `.claude/settings.json` sets it as a project default.

**Q: Is plan mode read-only? (docs)**
A: **No.** Claude reads files *and runs shell commands to explore*, then writes a plan. Only **edits to your source** are blocked, and only until you approve the plan.

**Q: What happens mechanically when you approve a plan in Claude Code? (docs)**
A: Approving **exits plan mode** and switches the session into an editing mode. The plan→execute handoff is a built-in step, not a separate decision.

**Q: How do you leave plan mode without approving the plan? (docs)**
A: Press `Shift+Tab` again.

**Q: Name Claude Code's six permission modes. (docs)**
A: `default` (labelled Manual) · `acceptEdits` · `plan` · `auto` · `dontAsk` · `bypassPermissions`.

**Q: "Claude never does anything without your consent." Where does this claim break down? (docs)**
A: Consent is a property of the **permission mode**, not of Claude. `acceptEdits` auto-approves file edits and common filesystem commands, `auto` lets a classifier approve actions instead of you, and `bypassPermissions` skips checks entirely. Plan mode is a guardrail because it blocks edits — not a restatement of a universal rule.

**Q: A demo shows a Plan/Act mode toggle and a thinking-budget slider set to 2,800 tokens. Do these exist in Claude Code? (docs)**
A: **No** — those belong to **Cline**, a third-party VS Code extension with separately-billed credits. Claude Code uses permission modes and sets reasoning depth with `/effort`.

**Q: A developer on a Claude Pro subscription hits "credits balance is $0.00" inside a VS Code extension. What's the diagnosis? (docs)**
A: The extension bills separately from the Claude subscription — they're two different accounts. Switching to the Claude Code CLI (or the Claude desktop app) uses the subscription instead.

---

## Domain 3 / Domain 5 — Context Exhaustion and Exploration

**Q: Describe context window exhaustion during codebase discovery.**
A: Claude reads files, follows import chains, chases usages, and reads tests — filling the context with file contents before it has started reasoning about what to do. It has no room left to think.

**Q: A question mentions context window exhaustion or large-codebase discovery. What is the answer?**
A: The **`Explore` subagent** — it reads broadly in its own context window and returns a compact summary, keeping discovery output out of the main session.

**Q: Why isn't "use a 1M-token context window" or "start a fresh session" the fix for context exhaustion?**
A: Both only postpone the problem — and the bigger window costs more. Neither removes the discovery cost from the main session; only delegating discovery does.

**Q: Is `Explore` just a phrase you put in a prompt? (docs)**
A: No — it's a **built-in Claude Code subagent** (alongside `Plan` and `general-purpose`). Claude delegates to it on its own when it needs to search or understand a codebase without making changes, and specifies a thoroughness level: `quick`, `medium`, or `very thorough`. No configuration is required, which is the half the lecture gets right.

**Q: Your `CLAUDE.md` says to ignore `vendor/`. Will a delegated `Explore` subagent honour that? (docs)**
A: **No.** `Explore` and `Plan` are the only subagents that skip `CLAUDE.md` and git status, to stay fast and cheap. Restate the rule in the prompt you give Claude when delegating.

**Q: You want to continue working with a subagent after it reports back. Why can't you resume `Explore`? (docs)**
A: `Explore` and `Plan` are **one-shot** and return no agent ID. Use `general-purpose` or a custom subagent when the work needs to continue.

**Q: Which prior pattern is the `Explore` subagent an instance of?**
A: The coordinator/subagent pattern — a specialist works in its own isolated context and returns only a summary to the coordinating session.

---

## Domain 3 — Model and Effort

**Q: Claude keeps making the same failed attempt at a bug, repeatedly. What does that signal, and what do you change?**
A: The **model is under-powered for that specific step** — not that the prompt is wrong. Escalate the model (Sonnet → Opus) and raise the effort level rather than re-prompting harder.

**Q: List Claude Code's effort levels and the default. (docs)**
A: `low`, `medium`, `high`, `xhigh`, `max` — the default is **`high`** on every model that supports effort (Opus 4.7 defaults to `xhigh`).

**Q: Name three ways to change the effort level in Claude Code. (docs)**
A: Any of: `/effort` (slider or a level name) · left/right arrow keys inside `/model` · the `--effort` launch flag · the `CLAUDE_CODE_EFFORT_LEVEL` environment variable · `effortLevel` in a settings file · `effort` in skill or subagent frontmatter.

**Q: You set `xhigh` on a model that doesn't support it. What happens? (docs)**
A: Claude Code falls back to the **highest supported level at or below** the one you set — so `xhigh` runs as `high` on Opus 4.6 or Sonnet 4.6.

**Q: You need deeper reasoning on one turn only, without changing the session's effort. What do you do? (docs)**
A: Include **`ultrathink`** anywhere in the prompt. It's the only keyword Claude Code recognises — `think`, `think hard`, and `think more` are passed through as ordinary prose.

**Q: Why scaffold a new app on a cheaper model rather than the most capable one?**
A: Scaffolding is largely mechanical and the framework CLI does much of the work, so capability isn't the constraint — spending the expensive model's tokens on a blueprint buys nothing. Escalate only when the task actually resists.

---

## Domain 3 — Iterative Refinement

**Q: Why is "make it better" useless feedback, and what does the corrected form look like?**
A: It gives Claude nothing to act on. Specific feedback names a location and a desired behaviour — e.g. *"the error handling in the try-catch block on line 47 needs to log the full error object."*

**Q: In test-driven iteration, what exactly do you feed back to Claude, and why is that more precise than describing the problem?**
A: **The test failures themselves.** The feedback is machine-generated, so there's no ambiguity about what success means — Claude knows precisely which assertions must pass, instead of interpreting your description of the target.

**Q: Prose descriptions of a transformation keep getting interpreted inconsistently. Which refinement technique applies?**
A: **Concrete examples** — supply a before/after pair or the exact target format. One concrete snippet beats a page of prose.

**Q: What is the interview pattern?**
A: Brief Claude on the high-level goal, then ask it to raise any clarifying questions before it starts — inverting the usual flow so Claude surfaces what it needs instead of you guessing.

**Q: Why does the interview pattern beat spending longer writing the perfect prompt?**
A: Claude often knows better than you which missing information would most change the output. You delegate *identifying the gap*, not just the work — ten minutes of elaborate prompting can still miss the one decisive detail.

**Q: A colleague says the interview pattern is redundant when you're already in plan mode. Correct them.**
A: They're independent axes: plan mode is **where Claude runs** (a permission mode); the interview pattern is **how you prompt**. Use both — get the questions answered first so the plan is right on the first draft.

**Q: A question describes an open-ended implementation with unclear scope and a goal of reducing rework. Which technique?**
A: **Interview pattern** — "reduce rework", "improve first-pass quality", "unclear scope", and "multiple possible interpretations" are its exam tells.

**Q: You have several issues to report on one output. When do you send them together, and when one at a time?**
A: Issues that **interact** go in a single message, because separate fixes may interfere with each other. **Independent** issues go sequentially, one fix at a time.

---

## Domain 3 — Signal-to-Answer Mapping

**Q: Exam phrasing: "large-scale refactor", "hard to reverse", "migrate", "re-architect". Answer?**
A: **Plan mode.**

**Q: Exam phrasing: "clear stack trace", "obvious single-file fix". Answer?**
A: **Direct execution.**

**Q: Exam phrasing: "context window filling up", "large codebase discovery". Answer?**
A: **`Explore` subagent.**

**Q: Exam phrasing: "ambiguous requirements", "unclear scope", "reduce rework". Answer?**
A: **Interview pattern.**

**Q: Exam phrasing: "transformation task", "prose keeps being interpreted differently". Answer?**
A: **Concrete examples.**

**Q: A team wants to extract DB queries from a 200-file monolith into an ORM. Which mode, and which triggers fire?**
A: **Plan mode** — three triggers fire at once: many files, hard to reverse, architectural impact. Only one was needed; multiple triggers reinforce rather than being required.

**Q: An approved plan is executed faithfully but the app's back button doesn't work. Where is the defect?**
A: In the **plan**, not the execution. Execution is faithful to the plan, so a requirement the plan never specified simply won't be built — which is why plan review is the load-bearing step.

---

*Back to: [[EP12 - Plan Mode vs Execute]]*
