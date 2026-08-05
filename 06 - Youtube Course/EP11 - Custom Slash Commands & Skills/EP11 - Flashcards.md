---
tags:
  - CCA-F
  - domain-3
  - skills
  - slash-commands
  - youtube-course
  - flashcards
date: 2026-08-04
status: done
domain: "3 of 5"
source: "Peace Of Code — Claude Certified Architect Ep 11"
---

# 🃏 EP11 — Flashcards

> [!NOTE] How to Use This Deck
> Self-contained review for **EP11 — Custom Slash Commands & Skills** (Domain 3, task statement 3.2). Overlap with the vault-wide deck is intentional — this deck stands alone as a complete review of the episode.
> Cards marked **(docs)** carry a correction or expansion verified against official Anthropic documentation. The lecture inverts what `allowed-tools` does and mis-describes `argument-hint` and `context: fork` — those cards give the verified behaviour.

**Related:** [[EP11 - Custom Slash Commands & Skills]] · [[D3 - Claude Code Configuration & Workflows]] · [[EP10 - Flashcards]] · [[EP03 - Subagent Context Passing & Session Management]]

---

## Domain 3 — Scoping and Distribution

**Q: A teammate clones the repo, types `/review`, and Claude doesn't recognise it — but it works on your machine. What is the cause?**
A: The command file is in user scope (`~/.claude/commands/review.md`), so `git clone` never delivered it. Move it to `.claude/commands/review.md` and commit.

**Q: Give the project-scope and user-scope paths for both commands and skills.**
A: Project — `.claude/commands/<name>.md` and `.claude/skills/<name>/SKILL.md`. User — `~/.claude/commands/<name>.md` and `~/.claude/skills/<name>/SKILL.md`.

**Q: What exam phrasing signals that something belongs in project scope?**
A: "Available to all contributors" — plus equivalents like "the whole team", "everyone who clones the repo". All mean `.claude/`, committed to version control.

**Q: Name a case where user scope is the better engineering choice, not just the lazy one.**
A: A cross-project helper. A command in `~/.claude/commands/` works in every project on your machine, so it can encode exactly which paths to consult across repos — far cheaper than starting Claude at your home directory and asking it to read everything.

**Q: You commit a `deploy` skill to `.claude/skills/`. A teammate has their own `deploy` in `~/.claude/skills/`. Whose runs? (docs)**
A: **Theirs.** Skill precedence is enterprise → personal → project, so **personal overrides project** — the reverse of the `CLAUDE.md` rule where the closer, more specific file wins.

**Q: A project skill and a `.claude/commands/` file share the same name. Which wins? (docs)**
A: The **skill** takes precedence.

**Q: Where do skills load from besides the startup directory? (docs)**
A: `.claude/skills/` in every parent directory up to the repo root loads at startup. Nested `.claude/skills/` **below** your starting directory load lazily — the first time Claude reads or edits a file in that subdirectory.

---

## Domain 3 — Commands vs CLAUDE.md

**Q: Why is "keyboard shortcut" an under-description of a slash command?**
A: A shortcut does text expansion. A slash command is a Markdown file holding a complete prompt that Claude reads and executes, so it can encode multi-step work rather than characters.

**Q: What single question decides whether content belongs in `CLAUDE.md` or in a command/skill?**
A: *Does this need to be true in every session, or only when I ask for it?* `CLAUDE.md` is ambient — loaded at every session start regardless of relevance. A command or skill costs nothing until invoked.

**Q: You have a 50-step procedural checklist in `CLAUDE.md`. What's wrong with that, and what's the fix?**
A: It's loaded into **every** session, so you pay for it whether or not you deploy that day — polluting context and diluting adherence. Move it to a skill, which loads only when used.

---

## Domain 3 — Skills vs Commands

**Q: What is the ceiling of a slash command that a skill removes?**
A: A command is a **static prompt** — same instructions every time, executed as written. A skill can be auto-loaded by Claude, run in its own context, carry supporting files, and be given tool constraints.

**Q: Officially, what is the relationship between `.claude/commands/deploy.md` and `.claude/skills/deploy/SKILL.md`? (docs)**
A: Custom commands have been **merged into skills** — both create `/deploy`, work the same way, and support the same frontmatter. Skills add a directory for supporting files, invocation control, and auto-loading.

**Q: Given commands and skills share the same frontmatter, is "a command can't fork" true? (docs)**
A: Not architecturally — a `.claude/commands/` file supports `context: fork` too. It's a description of typical use. **Exam answer: keep the lecture's static-prompt-vs-procedure distinction**, since the syllabus is built on it.

**Q: The lecture calls a command "a restaurant order form" and a skill "a specialist chef." What capability does that point at?**
A: Autonomy. A command executes exactly what's written; a skill can be handed an outcome, bring its own tools and files, ask clarifying questions, and work in its own context.

**Q: A skill is "like a subagent." When does it actually become one?**
A: Only when `context: fork` is set. Without it, the skill runs inline in your main session, sharing your context window.

---

## Domain 3 — SKILL.md Structure

**Q: What is the only required file in a skill, and where must it sit?**
A: `SKILL.md`, inside its own directory under `.claude/skills/` — e.g. `.claude/skills/deep-review/SKILL.md`. Everything else in the folder is optional.

**Q: Which frontmatter field determines whether Claude auto-loads a skill? (docs)**
A: **`description`** — what the skill does and when to use it. It's the field Claude reads to decide when to apply the skill.

**Q: Which `SKILL.md` frontmatter fields are required? (docs)**
A: **None** — all fields are optional. Only `description` is *recommended*; without it Claude falls back to the first paragraph of the body, making auto-invocation unreliable.

**Q: You rename a project skill's frontmatter `name` from `deep-review` to `audit`. What command now invokes it? (docs)**
A: Still `/deep-review`. For personal and project skills the command comes from the **directory name**; `name` is only a display label in listings. Only plugin skills use `name` to form the command.

**Q: Does `argument-hint: "[path-to-file]"` prompt a user who forgot the argument? (docs)**
A: **No.** It is **autocomplete display text** only — it shows the expected shape while typing. It doesn't prompt, validate, or block; an unfilled positional placeholder stays literal.

**Q: How does a value typed after a skill name actually reach the skill body? (docs)**
A: Via `$ARGUMENTS`, or `$0` / `$1` / `$ARGUMENTS[N]` for individual positions. If the body contains no `$ARGUMENTS`, Claude Code appends `ARGUMENTS: <input>` to the end of the content.

**Q: Which field stops Claude from triggering a skill on its own, and when is it essential? (docs)**
A: `disable-model-invocation: true`. Essential for anything with side effects — `/commit`, `/deploy`, `/send-slack-message` — where you must control timing rather than let Claude decide the code looks ready.

**Q: How do you limit a skill so Claude only auto-invokes it when working on certain files? (docs)**
A: The `paths` frontmatter field, with glob patterns — the same syntax as path-specific rules in `.claude/rules/`.

**Q: Once a skill is invoked, how long does its content stay in context? (docs)**
A: **For the rest of the session.** The rendered `SKILL.md` enters the conversation as a message and persists; Claude does not re-read the file on later turns.

**Q: If a skill's body loads only on use, why do the docs cap `SKILL.md` at 500 lines? (docs)**
A: Because after the first invocation every line is a **recurring** per-turn cost — the content persists all session. The saving is that it's free while *unused*; detail belongs in supporting files.

**Q: What happens if Claude re-invokes a skill whose content hasn't changed? (docs)**
A: Claude Code adds a short "already loaded" note rather than appending a second copy. If the rendered content differs — changed arguments or new dynamic-context output — the full content is appended again.

---

## Domain 3 — context: fork

**Q: What does `context: fork` do to a skill?**
A: Runs it as an isolated subagent whose prompt is the `SKILL.md` content, returning only the result to your main session — so verbose intermediate output never enters your context window.

**Q: Does a skill with `context: fork` have access to your conversation history? (docs)**
A: **No.** The `SKILL.md` content is its entire prompt. Anything it needs must be in the body or passed as arguments.

**Q: Why is calling `context: fork` "the same concept as fork sessions" misleading? (docs)**
A: Opposite treatment of history. Session forking (`--fork-session` / `/branch`) **copies the full conversation** into a new session. A forked *skill* starts with **no history at all** — and that isolation is exactly why it protects your context window.

**Q: A skill containing only "use these API conventions…" is given `context: fork`. What happens? (docs)**
A: It returns nothing useful — the subagent receives guidelines but no actionable task. `context: fork` suits skills with explicit instructions; reference-style content should run inline.

**Q: Do forked skills block the turn that invoked them? (docs)**
A: No — `background` defaults to **`true`**, so they run in the background and the result arrives when complete. Set `background: false` to wait within the invoking turn.

**Q: Name two costs of letting a forked skill run in the background. (docs)**
A: It runs with the **narrower tool set** that applies to background subagents, and its edits fall **outside session checkpoints** — so `/rewind` can't undo them, only git can.

**Q: Which field chooses *which* subagent executes a forked skill, and what's the default? (docs)**
A: `agent` — accepting `Explore`, `Plan`, `general-purpose`, or any custom subagent from `.claude/agents/`. Defaults to `general-purpose`.

**Q: A team's skill analyses hundreds of files and they fear intermediate results will flood the main conversation. What fixes it?**
A: `context: fork`. Distractors to reject: `allowed-tools` addresses permission prompts, not output volume; moving it to user scope changes who can run it, not where output lands.

---

## Domain 3 — Tool Permissions in Skills

**Q: A skill declares `allowed-tools: Read Grep`. Can it still call `Write`? (docs)**
A: **Yes.** `allowed-tools` only *pre-approves* the listed tools so they skip the permission prompt during the invoking turn. Every other tool remains callable under your normal permission settings.

**Q: Which frontmatter field actually removes a tool from a skill's reach? (docs)**
A: **`disallowed-tools`** — it takes tools out of Claude's available pool while the skill is active. `allowed-tools` grants; only `disallowed-tools` restricts.

**Q: You need a skill that reads code but must never modify a file. Which field, and why is the intuitive choice wrong? (docs)**
A: `disallowed-tools: Write Edit`. Listing only `Read`/`Grep` in `allowed-tools` looks like least privilege but merely pre-approves those two — `Write` stays callable.

**Q: How long does an `allowed-tools` grant last? (docs)**
A: Only the turn that invoked the skill — it clears when you send your next message, even though the skill's *content* stays in context. For a session-long grant, use allow rules in permission settings.

**Q: Why should you review a repository's `.claude/skills/` before trusting it? (docs)**
A: A committed skill can grant itself broad tool access via `allowed-tools`, which takes effect once you accept the workspace trust dialog — the same gate as permission rules in `.claude/settings.json`.

**Q: In one sentence, separate what `context: fork` controls from what the tool fields control.**
A: `fork` controls **where** the skill runs (isolated subagent vs main session); `allowed-tools` / `disallowed-tools` control **what it may touch**. Two independent axes.

---

## Domain 3 — Supporting Files

**Q: What do optional files in a skill directory buy you, given only `SKILL.md` is required?**
A: They load **only when `SKILL.md` points Claude at them**, so bulky templates, API references, and example collections cost nothing until needed. Scripts are executed rather than loaded, costing no context at all.

**Q: How should `SKILL.md` be written when a skill has supporting files?**
A: As a lean router that names each resource and when to use it — "for complete API details, see `reference.md`" — with the bulk deferred to those files.

**Q: What does `` !`git diff HEAD` `` do inside a skill body? (docs)**
A: Dynamic context injection — the shell command runs *before* Claude sees the content, and its **output** replaces the placeholder. So the skill arrives pre-loaded with real data rather than an instruction to go fetch it.

**Q: What does `${CLAUDE_SKILL_DIR}` resolve to, and why is it useful? (docs)**
A: The directory holding the skill's `SKILL.md`. It lets a skill reference its own bundled scripts regardless of the current working directory — and it substitutes inside `allowed-tools` too, so a bundled script can run without prompting.

---

## Domain 3 — Selection Signals

**Q: Map each phrasing to its field: "available to all contributors" · "prevent context pollution" · "least privilege" · "reduce permission prompts".**
A: Project scope (`.claude/`, committed) · `context: fork` · `disallowed-tools` · `allowed-tools`.

**Q: When should content be a `CLAUDE.md` entry, a skill, or a command?**
A: Always-true facts and standing rules → `CLAUDE.md`. Multi-step procedures, especially ones Claude should recognise and load itself → a skill. Static reusable prompts you trigger by name → a command.

**Q: A skill seems to stop influencing behaviour after its first response. What is usually happening? (docs)**
A: The content is still in context — the model is simply preferring other approaches. Strengthen the `description` and instructions, or use hooks to enforce behaviour deterministically. After heavy compaction, re-invoke the skill to restore its full content.

---

*Back to: [[EP11 - Custom Slash Commands & Skills]]*
