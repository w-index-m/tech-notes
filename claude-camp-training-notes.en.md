> Translated from the Japanese source: `claude-camp-training-notes.pptx`

# Claude Code Study Notes

*A study note covering Claude Academy's "Claude Code 101" course, the official Quickstart guide, summaries of five Claude Camp talks, and the Claude Frontier Academy enterprise training program.*

*Study notes based on Anthropic's official training materials.*

## Table of Contents

1. **Claude Academy: Claude Code 101 course** — the structure of the official self-study course
2. **Official Quickstart & CLAUDE.md / Auto Memory** — installation, basic commands, and how project memory works
3. **Claude Camp: Getting Started with Claude Code** — how the agent works and workflow practice
4. **Claude Camp: Moving to the Frontier** — AI progress trends and business strategy
5. **Claude Camp: Proving value and controlling cost** — cost management and real-world value examples
6. **Claude Camp: AI Fluency** — the 4D framework and organizational adoption
7. **Claude Camp: Your first real workflow in Cowork** — building a practical workflow in Cowork
8. **Claude Frontier Academy** — Anthropic's enterprise AI talent development program ($100M investment, Frontier Deployed Engineers)

---

# Part 1: Claude Academy — "Claude Code 101"

## Course Overview

*Anthropic's official course for learning the terminal-resident, agentic coding tool from the ground up.*

| | | | |
|---|---|---|---|
| **12** Lessons | **1.5h** Duration | **1** Quiz | **✓** Completion badge |

**What you'll learn (by the end of this course, you'll be able to):**

- Explain the difference between AI coding agents and chat-based tools
- Understand how the agent loop, context window, tools, and permissions work together
- Write effective prompts by choosing appropriately between manual mode, auto-approve, and Plan Mode
- Follow the Explore → Plan → Code → Commit workflow
- Customize Claude Code with CLAUDE.md, subagents, MCP, and hooks

*Source: Claude Academy, "Claude Code 101" course page (academy.claude.com/ja)*

## Course Structure (4 sections, 12 lessons)

| Section | Covers | Lessons |
|---|---|---|
| **What is Claude Code?** | The agent loop, tools, and permission control, understood from first principles | 2 lessons |
| **Your First Prompt** | Installing across multiple environments; choosing between approval modes, auto-approve, and Plan Mode | 2 lessons |
| **Daily Workflow** | Explore→Plan→Code→Commit, context management, code review | 3 lessons |
| **Customizing Claude Code** | CLAUDE.md, subagents, skills, MCP, hooks | 5 lessons |

*Source: Claude Academy, "Claude Code 101" course page (academy.claude.com/ja)*

## Lesson 1: What is Claude Code?

*Claude Code 101 · Lesson 1 of 12*

### How it differs from Claude.ai / what it can actually do

Unlike Claude.ai, Claude Code has direct access to your files, terminal, and entire codebase. Instead of copying and pasting back and forth, Claude Code itself carries out the work. The biggest difference is that it **operates as an AI agent**.

- Reads and understands the codebase (explaining how a feature works, tracking down a bug)
- Edits files across the whole project (refactoring a function and updating every call site that references it)
- Runs terminal commands (builds, tests, package installs — reading the output to decide the next step)
- Searches the web (for the latest documentation, API references)

### What is an agent?

Software that interacts with an environment and takes actions to achieve a defined goal. At its core, an LLM runs a real-time loop and may reach out to tools, external services, and even other AI agents.

**Three concepts for using it effectively:**

1. **Context window** — this is working memory. Since it can't hold everything, Claude has to search strategically.
2. **You control permissions** — you choose between asking for confirmation and running automatically.
3. **It makes mistakes too** — misreading intent, introducing bugs, over-engineering. Staying involved in the process lets you catch these early.

*Source: Claude Academy, "Claude Code 101," Lesson 1: What is Claude Code?*

## Lesson 2: How Claude Code Works

*Claude Code 101 · Lesson 2 of 12*

### The agentic loop (5 steps)

1. You enter a prompt.
2. Claude exchanges messages with the model, gathering context; the model returns either text or a tool call.
3. Claude takes action (editing files, running commands).
4. It verifies the result and judges whether the goal has been achieved.
5. If achieved, it stops and waits for your next input; if not, it loops back and tries again.

Throughout this loop, you can add context, interrupt, or steer at any point.

### Context / Tools

- When the context window nears its limit, Claude Code automatically decides what to drop or summarize, and compacts it.
- Tools are the backbone of agentic behavior. An ordinary AI just returns text, but tools let Claude decide "when code should actually be run" based on semantic understanding of the situation.

### Permission modes (4 types)

| Mode | Behavior |
|---|---|
| **Manual** | Asks for explicit permission before every edit or command execution |
| **Auto-approve** | Files are edited without confirmation; commands still require approval |
| **Plan Mode** | Uses read-only tools to put together a plan before starting any work |
| **Auto mode** | Proceeds with no permission prompts; a classifier running behind the scenes blocks irreversible, destructive, or out-of-environment operations |

Which mode a new session starts in depends on your plan and configuration — everything is configurable in settings files. Be careful when skipping permission checks: the more execution freedom you grant, the harder it becomes to notice a mistake.

*Source: Claude Academy, "Claude Code 101," Lesson 2: How Claude Code Works*

## The Essence of Claude Code, in One Line

*A simple three-part framework that also applies to understanding agentic AI in general.*

| LLM | Tool | Loop |
|---|---|---|
| **Judgment** | **Execution** | **Verify & repeat** |
| Decides what to do and which tool to call next, each time | Actually acts on the environment — editing files, running commands, web search, and so on | Checks the result itself and repeats steps 1–2 until the goal is achieved |

- It delivers **"verified work product," not just an "answer."** An ordinary chat simply answers your question with text, but Claude Code actually acts within the loop and checks the result itself before responding.
- **"Programming" is just one application of this mechanism.** The loop-plus-tools mechanism is general-purpose — Claude Code simply configures its tools around a developer-oriented set: reading and writing files, running the terminal, and searching the web.

## Lesson 3: Installing Claude Code

*Claude Code 101 · Lesson 3 of 12*

| Environment | How to install |
|---|---|
| **Terminal** | macOS/Linux/WSL: one-shot install via `curl` (Homebrew does not auto-update). Windows: PowerShell uses `Invoke-RestMethod`, CMD uses `curl`; `winget` is also available (no auto-update). Running `claude` from a directory grants access to that directory and its subfolders. |
| **VS Code** | Search "Claude Code" in the Extensions panel (the official Anthropic extension, marked with a blue verified checkmark). Use Ctrl/Cmd+Shift+P → "Claude Code: Open in New Tab." Can be switched to a terminal-style experience in settings. |
| **JetBrains** | Install the plugin from the JetBrains Marketplace and restart the IDE. Opens a terminal pane that runs alongside the editor. |
| **Desktop** | Toggle "Code" at the top of Claude Desktop. Supports working in a specific folder, changing permissions, and working in cloud environments. |
| **Web** | claude.ai/code, or the "Code" tab in the chat sidebar. Behaves like the desktop version, but is limited to GitHub repositories. |

**Which one should you use?** For staying on the cutting edge, use the terminal — new features land there first. IDE integration is best when you want a unified feel. Desktop is best for background execution alongside other work. Web is best when you want to work remotely.

*Source: Claude Academy, "Claude Code 101," Lesson 3: Installing Claude Code*

## Lesson 4: Your First Prompt

*Claude Code 101 · Lesson 4 of 12*

### Choosing a permission mode (toggle with Shift+Tab)

- **Manual mode** — asks for permission every time it edits a file or runs a command
- **Auto-approve mode** — file edits are auto-approved; commands still require permission
- **Auto mode** — proceeds without permission prompts, screened by background safety checks

There's no single right answer — pick whichever mode you're comfortable with.

### Plan Mode

Takes your prompt and analyzes/investigates the codebase using read-only tools. Asks clarifying questions along the way, and returns an actionable, detailed plan. Best for planning complex changes, or for a safe form of code review before anything is touched.

### Example: adding a dark-mode toggle

Run `claude` at the project root → press Shift+Tab a few times to enter Plan Mode → request: *"I want to implement dark mode across the app. Add a light/dark toggle in the header, and choose colors with good contrast based on the existing light theme."* → review and approve the plan → implementation proceeds. Depending on the permission mode, you may be asked to confirm partway through.

**Summary:** Make your prompts as specific as possible. Plan Mode lets you have Claude dig into the details of what you actually want before it runs any code.

*Source: Claude Academy, "Claude Code 101," Lesson 4: Your First Prompt*

## Lesson 5: Explore → Plan → Code → Commit

*Claude Code 101 · Lesson 5 of 12*

### Explore and Plan

In Plan Mode, Claude can only read files, not edit them. Ask it to "read the relevant files, search the web, and present an action plan" → review it, and if it doesn't meet your bar, ask for revisions. This stage — before any code has been touched — is the point where it's easiest to course-correct. If you don't plan to make changes and just want an overview, the `explore` subagent is another option.

### Coding

Once you approve the plan, Claude executes it. Depending on the permission mode, you can confirm each step, auto-approve, or run in full auto mode. Tips: state your success criteria explicitly, add supporting tools such as Claude in Chrome, and have a trustworthy test suite ready. If you run into the same issue repeatedly, ask Claude to save it to CLAUDE.md.

### Commit

Once you've tested it yourself and are satisfied, have a subagent code reviewer look it over before committing — a fresh set of eyes that doesn't carry the main agent's biases. Then have Claude generate a commit message in your own style, and repeat the cycle.

**Summary:** Explore = give it the context it needs. Plan = build an action plan you can measure success against. Coding = iterate until you're happy with the result. Commit = review, push, and move on to the next feature.

*Source: Claude Academy, "Claude Code 101," Lesson 5: The Explore→Plan→Code→Commit Workflow*

## Lesson 6: Context Management

*Claude Code 101 · Lesson 6 of 12*

### What is the context window, and what happens when it fills up?

The amount of memory Claude can hold at once. Prompts, files it reads, and every tool call and its result all add to it. As it nears the limit, Claude automatically compacts: important details get summarized and unneeded tool results get dropped to free up space — and this process can lose some detail.

### Commands

| Command | What it does |
|---|---|
| `/compact` | Manually compact. Frees up space while retaining what's been discussed so far |
| `/clear` | Start completely from zero. Carries none of the memory from the previous session |
| `/context` | View a graphical breakdown of context size |

**When to use which:** `/compact` when you want to keep working on the same feature and preserve the current context; `/clear` when starting a new feature and don't want to carry over bias from the previous conversation. Anything you want remembered across sessions belongs in CLAUDE.md.

### Three tips for saving context

1. **Be specific in your instructions.** Vague instructions cost more context in the long run, since Claude has to explore and reason more to fill the gaps.
2. **Manage your MCP servers.** By default, all tool definitions for unused MCP servers still load into context — turn them off if you're not using them, or consider a Skill instead.
3. **Use subagents.** They work in an isolated context and return only a summary to the main thread, so they never clutter the main context.

*Source: Claude Academy, "Claude Code 101," Lesson 6: Context Management*

## Lesson 7: Code Review (1/2)

*Claude Code 101 · Lesson 7 of 12*

The session that wrote (and explained) a change is often not the best judge of that change. Good practice: review it yourself before committing, then have Claude re-review it from a clean context that carries none of this session's history.

### Use `/diff` to see the actual changes

Shows uncommitted changes interactively (arrow keys to move between files, Enter to open). Three things to check every time:

| Check | What to look for |
|---|---|
| **Unrequested changes** | Config values, helper methods, etc. that were rewritten without being mentioned |
| **Weakened tests** | Tests that were skipped, deleted, or loosened just to make them pass |
| **New packages / hardcoded values** | A dependency added just for one function; URLs, keys, etc. hardcoded directly into code |

### Exercise example: sign-up form input validation (8 files changed)

Claude's summary reported "all tests passing." Looking at the actual diff shows the lesson: even when the summary itself isn't a lie, changes that got overlooked (like weakened tests) can slip in alongside it. Developing the habit of checking the actual diff — not just trusting the summary — matters.

*Source: Claude Academy, "Claude Code 101," Lesson 7: Code Review*

## Lesson 7: Code Review (2/2)

*Claude Code 101 · Lesson 7 of 12*

### Get a second opinion with `/code-review`

Reviews the change from a clean context that carries none of the session's history, and reports only the results (it won't make edits unless you ask). It runs in the background, can take anywhere from seconds to minutes, and counts against usage — so save it for changes that are actually worth a closer look. A plain-language request can trigger the same review too.

### Three buckets for sorting findings

| Bucket | When to use it |
|---|---|
| **Fix now** | A real, significant issue → fix it |
| **Ask why** | A finding you can't fully verify, or that seems off → quote the finding as-is and have Claude double-check it (the reviewer can be wrong too) |
| **Leave it** | A real but minor/unimportant issue → batch it into a future session |

### Exercise example: four findings

1. Skipped test / weak assertion [correctness] → **fix now**
2. `isValidEmail` missing a `trim()` [correctness] → actually already trimmed on line 4 — an example of a reviewer false positive (a candidate for "ask why")
3. Hardcoded `API_URL` [correctness] → **fix now** (a serious issue: production could end up sending requests to a dev address)
4. Hex color code hardcoded in an error message [style] → a candidate for "leave it"

**When a thorough review is warranted:** when the change is too large to hold in your head at once, when it involves sensitive or destructive operations, or before handing it off to a teammate — use both a human review and a Claude review in these cases.

If the same finding keeps coming up, write that rule into CLAUDE.md (the topic of the next lesson).

*Source: Claude Academy, "Claude Code 101," Lesson 7: Code Review*

## Lesson 8: The CLAUDE.md File

*Claude Code 101 · Lesson 8 of 12*

### The problem it solves

Without CLAUDE.md, every session starts from zero — re-exploring the codebase, re-figuring out dependencies, re-understanding existing features every single time, sometimes forced to guess. CLAUDE.md is "an onboarding script for the codebase" that's automatically read and appended to the prompt at the start of every session.

### Example

```
# Project
This is a Next.js 15 app using the App
Router, Tailwind, and Drizzle ORM.

# Commands
- Dev server: pnpm dev
- Run tests: pnpm test

# Code Style
- Use 2-space indentation
- Prefer named exports
```

### CLAUDE.md is for the team

It should be committed to version control. There's a hierarchy: **project level** (placed at the repo root, shared with the team) and **user level** (placed in your personal settings folder, applies across all your projects).

### Three tips

- If you find yourself repeating the same correction, explicitly ask Claude to "save this to memory."
- Reference docs you want Claude to read using `@`, e.g. `@README.md`.
- It's better to start *without* a CLAUDE.md, and generate one with `/init` once you see where you actually needed to course-correct — this keeps the content compact.

**Summary:** The difference between a frustrating session and a productive one often comes down to context. CLAUDE.md is how you supply it.

*Source: Claude Academy, "Claude Code 101," Lesson 8: The CLAUDE.md File*

## Lesson 9: Subagents

*Claude Code 101 · Lesson 9 of 12*

### How it works

What you discover while exploring isn't always relevant to the main feature you're building. Claude can spawn a task — like "explore this codebase" — and delegate it to a subagent, which runs in parallel with its own context window and returns only a summary to the main thread once it's done. This gets you the answer without cluttering the main context with the journey it took to find it.

### How to create them, and further customization

Defined as Markdown with YAML frontmatter. The easiest way is `/agents` → "Create new agent" and let Claude generate it (you choose scope, purpose, tools it can use, and a color).

- **Persistent memory:** subagents can retain memory across conversations, which is useful for continued work on the same project.
- Listing names under the `skills` key preloads skills for the subagent — but unlike the main conversation, here the *entire* skill gets loaded into context, not just its name and description.
- For deeper learning, there's a dedicated course: "Introduction to Subagents."

**Summary:** Keeping the context window clean is the key to staying productive. Use subagents to push heavy work into the background and return only the answer to the main thread.

*Source: Claude Academy, "Claude Code 101," Lesson 9: Subagents*

## Lesson 10: Skills

*Claude Code 101 · Lesson 10 of 12*

Coding conventions, PR review structure, commit message preferences — aren't you explaining the same things over and over? A **skill** is a Markdown file (a folder bundling instructions, scripts, and resources) that you only have to teach once, and it's applied automatically whenever it's relevant. SKILL.md's `description` is what tells Claude whether it should be used — your request is matched against every skill's description, and whichever one matches gets activated.

### Storage locations

| Scope | Location | Notes |
|---|---|---|
| **Personal** | `~/.claude/skills` | Follows you across all projects — preferences, commit style, etc. |
| **Project** | `.claude/skills` at the repo root | Automatically picked up by anyone who clones the repo — team standards like brand guidelines, etc. |

### Difference from CLAUDE.md and slash commands

CLAUDE.md is loaded into every conversation (e.g. "always use TypeScript strict mode"). Skills load on demand, only when they match a request — and only their name and description load at first, so they don't weigh down context (you don't need a PR-review checklist while you're debugging). Slash commands require you to type them explicitly; skills recognize the situation and apply automatically.

If you find yourself explaining the same thing to Claude over and over, that's exactly "a skill waiting to be written." There's also a dedicated course, "Introduction to Agent Skills."

*Source: Claude Academy, "Claude Code 101," Lesson 10: Skills*

## Lesson 11: MCP

*Claude Code 101 · Lesson 11 of 12*

The **Model Context Protocol (MCP)** is an open standard that lets Claude Code connect to external tools and data sources. Much of your context lives outside the codebase — databases, productivity apps, public repositories — and MCP bridges that gap. Example: pulling issue details from the Linear MCP, or fetching up-to-date dependency documentation via Context7.

### How to add it, and scope

Add a server with `claude mcp add`. Two types: **HTTP** (for remote services) and **Stdio** (for local processes on your own machine). Use `/mcp` to check connection status and disable servers you don't need.

**Scope:**

- **Local** — just you, current project only
- **User** — all your projects
- **Project** — `.mcp.json` checked into version control, so the whole team automatically gets the same configuration

### The cost of context

Even when unused, tool definitions for a configured MCP server live permanently in the context window — the more servers you configure, the heavier it gets. Check with `/mcp` and disable anything you're not actively using. If a CLI equivalent already exists (`gh`, `aws`, etc.), the CLI is more context-efficient. Sometimes a Skill is more useful, since only its name and description stay resident. If MCP tools exceed 10% of context, Claude Code automatically switches into a tool-search mode — but that isn't guaranteed to work reliably.

*Source: Claude Academy, "Claude Code 101," Lesson 11: MCP*

## Lesson 12: Hooks

*Claude Code 101 · Lesson 12 of 12 (final lesson)*

### Why use hooks

Unlike the other mechanisms in this course, hooks are **deterministic** — they always run. Asking in CLAUDE.md to "run Prettier on every edit" works most of the time, but occasionally it gets skipped. A hook runs every single time, without exception.

### Main events (configured in `settings.json`)

| Event | Fires |
|---|---|
| `PreToolUse` | Before a tool call |
| `PostToolUse` | After a tool call completes |
| `UserPromptSubmit` | When a prompt is submitted (before processing) |
| `Stop` | When Claude finishes its response |
| `Notification` | When Claude sends a notification |

### Blocking with `PreToolUse` (exit codes)

Receives JSON (tool name, input) over stdin.

- Exit code **0** = continue normally
- Exit code **2** = block the action; the stderr message becomes feedback to Claude
- Any other code = show an error without blocking

This lets you stop operations that need a "guarantee" — writing to production config, commands containing `rm -rf`, direct commits to `main`, and so on.

**Representative example:** set an `"Edit|MultiEdit|Write"` matcher on `PostToolUse` to run an extension-appropriate formatter (Prettier, gofmt, etc.) on every edit.

Checking a hook into `.claude/settings.json` distributes it to the whole team (use the `CLAUDE_PROJECT_DIR` environment variable to reference the script's absolute path). Anything you need to run reliably every time belongs in a hook, not a prompt.

*Source: Claude Academy, "Claude Code 101," Lesson 12: Hooks*

---

# Part 2: Official Quickstart

## Install Through Login

### ① Install

```bash
# macOS / Linux / WSL
curl -fsSL https://claude.ai/install.sh | bash
```

```powershell
# Windows PowerShell
irm https://claude.ai/install.ps1 | iex
```

**Verify:** run `claude --version` — if a version number appears, you're set. Native Install updates itself automatically in the background.

### ② Log in

Typing `claude` prompts browser-based authentication the first time. Use `/login` to re-login or switch accounts.

**Supported accounts:** Claude Pro/Max/Team/Enterprise (recommended), Claude Console (a dedicated workspace is auto-created on first login), via cloud providers such as Bedrock/Vertex/Foundry, or via an organization's self-hosted gateway (SSO).

### ③ First session

`cd /path/to/your/project && claude` to start. Opening with a question like "what does this project do?" lets Claude Code automatically read the files it needs and answer — you don't have to hand it context manually.

*Source: Claude Code official documentation, "Quickstart"*

## Basic Command List

### Shell commands (start/resume from the terminal)

| Command | What it does |
|---|---|
| `claude` | Start interactive mode |
| `claude "task"` | Start interactive mode with an initial prompt |
| `claude -p "query"` | Run a one-off query and exit |
| `claude -c` | Resume the most recent conversation in the current directory |
| `claude -r` | Pick a past conversation to resume |

### Session commands (used while Claude Code is running)

| Command | What it does |
|---|---|
| `/clear` | Clear conversation history |
| `/help` | Show available commands |
| `/exit` (or Ctrl+D twice) | Exit Claude Code |

*Source: Claude Code official documentation, "Quickstart" (see the CLI reference / command reference for the complete list)*

## The Typical 8 Steps

1. **Understand the codebase** — start with a question like "what does this project do?"
2. **Make your first code change** — e.g. "add a hello world function"; say yes when asked for approval
3. **Interact with Git** — work conversationally, e.g. "commit my changes with a descriptive message"
4. **Fix bugs / add features** — just describe it in natural language; Claude locates the relevant code, implements the change, and runs the tests, all automatically
5. **Other workflows** — refactoring, writing tests, updating docs, and code review can all be requested the same way

**Tips for beginners:** be specific in your requests · break instructions into steps · let Claude explore first · use keyboard shortcuts to save time

*Source: Claude Code official documentation, "Quickstart"*

---

# Part 3: Deep Dive — CLAUDE.md, AGENTS.md, and Auto Memory

## Deep Dive ①: CLAUDE.md vs. Auto Memory

Every session starts from a blank context. Two mechanisms carry knowledge forward:

| | CLAUDE.md file | Auto Memory |
|---|---|---|
| **Written by** | You | Claude itself |
| **Content** | Instructions, rules | Things learned, patterns |
| **Scope** | Project / personal / organization | Per-repository (shared across worktrees) |
| **Loaded** | In full, every session | Every session (top 200 lines, or up to 25KB, of `MEMORY.md`) |
| **Use for** | Coding conventions, workflow, architecture | Your preferences, correction instructions, project context that can't be read from the code |

Both are treated as **context, not enforced settings** — they're passed as a user message after the system prompt, not as part of the system prompt itself. If you need to reliably block a specific action, use a `PreToolUse` hook instead.

*Source: Claude Code official documentation, "How Claude remembers your project"*

## Deep Dive ②: Where CLAUDE.md Files Live (In Detail)

**Load order:** broad scope → narrow scope (loaded later = instructions closer to the working directory are presented later in the sequence).

| Level | Location | Notes |
|---|---|---|
| **Managed policy** | macOS: `/Library/Application Support/ClaudeCode/CLAUDE.md`<br>Linux/WSL: `/etc/claude-code/CLAUDE.md`<br>Windows: `C:\Program Files\ClaudeCode\CLAUDE.md` | Organization-wide (managed by IT/DevOps). Cannot be excluded by individual settings. |
| **User instructions** | `~/.claude/CLAUDE.md` | Personal settings shared across all your projects |
| **Project instructions** | `./CLAUDE.md` or `./.claude/CLAUDE.md` | Shared with the team, distributed via source control |
| **Local instructions** | `./CLAUDE.local.md` | Personal only, excluded via `.gitignore` (sandbox URLs, etc.) |

### Load-rule details

Every `CLAUDE.md`/`CLAUDE.local.md` in the working directory and all of its parent directories is concatenated and loaded at startup (all of them are loaded — none overwrite the others). Files in subdirectories are lazy-loaded only when Claude reads a file in that directory. HTML comments (`<!-- -->`) are automatically stripped and don't consume context.

*Source: Claude Code official documentation, "How Claude remembers your project"*

## Deep Dive ③: Writing Effective Instructions

- **Size** — aim for under 200 lines per file. The longer it is, the more it weighs down context and the lower the compliance rate. Once it grows large, split it into path-scoped rules.
- **Structure** — group content with headings and bullet lists. Claude reads structure the same way a human reader does.
- **Specificity** — instead of "format the code nicely," say "use 2-space indentation"; instead of "test your changes," say "run `npm test` before committing." Make instructions verifiable.
- **Consistency** — contradictory instructions force Claude to pick one arbitrarily. In a monorepo, `claudeMdExcludes` can exclude another team's unrelated CLAUDE.md.

### Automatic auditing with `/doctor prompt-audit` (v2.1.283+)

Audits CLAUDE.md, CLAUDE.local.md, AGENTS.md, rules, skills, commands, subagents, and output styles all together. Detects instructions aimed at outdated models, references to nonexistent files, and contradictions, and produces a report. No actual changes are applied unless you request them.

*Source: Claude Code official documentation, "How Claude remembers your project"*

## Deep Dive ④: `@path` Import Syntax

`@path/to/file` lets you pull a file's contents into CLAUDE.md. Relative paths resolve relative to the **referencing file**, not the working directory — be careful. Recursive imports go up to 4 levels deep.

Wrapping a reference in backticks makes it literal text: `` `@README` `` (in backticks) is **not** imported, but `@README` (without backticks) **is** imported.

If you want to share personal settings across worktrees, `@import` files under `~/.claude/` rather than relying on `CLAUDE.local.md`.

**External imports** (paths pointing outside the working directory) trigger an approval dialog the first time. If you decline, the dialog won't appear again and the file won't be loaded — a safeguard against unintentionally loading a file someone else committed.

Files you wrote yourself in user scope — such as `~/.claude/CLAUDE.md` or `~/.claude/rules/` — are normally trusted without a dialog. However, in **desktop Cowork sessions**, imports and symbolic links pointing outside the working directory are skipped entirely.

*Source: Claude Code official documentation, "How Claude remembers your project"*

## Deep Dive ⑤: `.claude/rules/` in Detail

### Basics and path targeting

- One file = one topic (e.g. `testing.md`). Subdirectories are also detected recursively.
- Use the `paths` field in YAML frontmatter to scope a rule to specific files. With no `paths` specified, the rule always loads.
- `paths` matches when a file is **opened** — not on every tool call.
- Brace expansion (e.g. `{ts,tsx}`) shares a budget of at most 1,000 patterns / 4MiB per rule.

### Glob pattern examples

| Pattern | Matches |
|---|---|
| `**/*.ts` | Any TypeScript file, at any depth |
| `src/**/*` | All files under `src/` |
| `*.md` | Markdown files at the project root |
| `src/components/*.tsx` | React components in a specific directory |

An invalid pattern (e.g. `photos [2024/**`) disables only that pattern (no matches); other patterns keep working normally.

### Sharing and user-level rules

Symbolic links can share rules across multiple projects (circular links are detected safely). A symlink pointing outside the working directory requires the same approval as an external import — if you want to use one without approval, place it in `~/.claude/rules/` (shared across all projects). User-level rules are read before project rules, but carry **equal priority**: when they conflict, either one may be followed arbitrarily.

*Source: Claude Code official documentation, "How Claude remembers your project"*

## Deep Dive ⑥: Managing at Large-Team Scale

### Distributing a Managed CLAUDE.md org-wide

Place the file at the OS-specific designated path and distribute it via MDM, Group Policy, Ansible, etc. (it cannot be excluded by individual settings). The `claudeMd` key in `managed-settings.json` also lets you write the body directly in JSON instead of distributing a separate file. **Scope:** every session and every repository on that machine. **Priority:** loaded before user/project CLAUDE.md.

### Settings vs. CLAUDE.md — when to use which

| Need | Use |
|---|---|
| Blocking specific tools/commands/paths | Managed settings: `permissions.deny` |
| Enforcing sandbox isolation | Managed settings: `sandbox.enabled` |
| Code style / data-handling caution | Managed CLAUDE.md |
| Behavioral instructions to Claude | Managed CLAUDE.md |

Settings are enforced **client-side regardless of Claude's judgment**, whereas CLAUDE.md only steers behavior — it is not an enforcement layer.

`claudeMdExcludes`: in a monorepo, you can exclude another team's CLAUDE.md/rules by glob pattern (settable at any settings layer — user/project/local/managed — and arrays merge across layers). Only the Managed-policy CLAUDE.md cannot be excluded.

*Source: Claude Code official documentation, "How Claude remembers your project"*

## Deep Dive ⑦: Relationship with AGENTS.md

### Default load priority

| Situation | Result |
|---|---|
| Only AGENTS.md exists, no CLAUDE.md/.local.md | AGENTS.md is read |
| Both AGENTS.md and CLAUDE.md/.local.md exist | Only CLAUDE.md is read (AGENTS.md is ignored) |
| CLAUDE.md already `@`-imports AGENTS.md | AGENTS.md's content is read via CLAUDE.md |

### `/config`'s "Project instructions" setting values

| Value | Behavior |
|---|---|
| `claude-md-or-agents-md` (default) | Reads CLAUDE.md if present, otherwise AGENTS.md |
| `claude-md-and-agents-md` | Reads both (CLAUDE.md then AGENTS.md, in that order, per directory) |
| `claude-md` | CLAUDE.md only |
| `managed-only` | Only the organization's Managed CLAUDE.md and auto memory (rules, AGENTS.md, etc. are all excluded) |

Note: unlike CLAUDE.md, AGENTS.md does **not** fire the `InstructionsLoaded` hook, and it is **not** loaded additionally via `--add-dir` — differences worth being aware of. Requires v2.1.277 or later.

*Source: Claude Code official documentation, "How Claude remembers your project"*

## Deep Dive ⑧: Auto Memory Internals & Troubleshooting

### Auto Memory's 4 types, and the criteria for saving

There are 4 types: `user`, `feedback`, `project`, `reference`. Information that can be read straight from the code, or that's already in CLAUDE.md, is not saved.

- `MEMORY.md` (the index) only has its **top 200 lines, or up to 25KB**, loaded at startup. Anything beyond that isn't loaded next time, so Claude is prompted to rewrite it when it overflows.
- Topic-specific files are **not** loaded at startup — Claude reads them only when needed.
- Not inherited by subagents by default (exception: a `fork` inherits the parent's entire conversation).

### Troubleshooting

| Symptom | What to check |
|---|---|
| Instructions aren't being followed | Check the "Memory files" section via `/context`, make instructions more specific, look for contradictions. Commit/PR rules can conflict with Claude Code's built-in default instructions (adjust via `includeGitInstructions`, etc.) |
| AGENTS.md isn't being read | Check whether there's a CLAUDE.md/.local.md above the working directory → check whether `claude --version` is v2.1.277 or later → check via `/config` whether it's set to `claude-md` or `managed-only` |
| CLAUDE.md is too large | Warns above 200 lines. Files over 4MiB won't be loaded. Split into path-scoped rules. |
| Instructions disappeared after `/compact` | The project-root CLAUDE.md is reloaded from disk, but nested CLAUDE.md files and path-scoped rules aren't reloaded until the relevant file is opened again |

For anything that must run reliably at a specific point (e.g. before a commit), implement it as a **Hook**, not in CLAUDE.md — hooks run as shell commands at fixed lifecycle events, applied regardless of Claude's judgment.

*Source: Claude Code official documentation, "How Claude remembers your project"*

---

# Part 4: Claude Camp Session Summaries

## Getting Started with Claude Code (1/3)

*Final session of Claude Camp · Anthony & Maya · 89 minutes*

### What is an agent?

2022–23 was the era of text-in/text-out chatbots. 2024 onward is the era of "agents" — the model calls tools in a loop to achieve a goal. Claude Code itself originated as a project built by Anthropic's "Boris" at a hackathon in late 2024.

### 4 principles for effective use

1. Keep a human in the judgment loop (don't trust it fully)
2. Define "success" up front (avoid vague instructions)
3. Treat the first output as a "draft"
4. Give it enough instruction (Claude can't read your mind)

### Choosing a model

| Model | Speed/Cost | Best for |
|---|---|---|
| **Haiku** | Fastest, cheapest | Simple tasks |
| **Sonnet** | Balanced | Standard work |
| **Opus** | Flagship model | Complex development |
| **Fable** | Highest performance, high cost | New concepts, complex strategy |

**Positioning:** this accelerates the traditional development process — which used to take days for code comprehension and manual test writing — through faster understanding via dialogue, automated code generation and test loops, and mutual review.

*Source: Anthropic "Claude Camp," summary of the "Getting Started with Claude Code" session*

## Getting Started with Claude Code (2/3)

### CLAUDE.md hierarchy

```
~/.claude/claude.md              (personal settings)
  → /project/claude.md           (whole project)
    → /project/subdir/claude.md  (per-feature settings)
```

Auto-generated by `/init`, so manual creation usually isn't necessary — it automatically records the project's language, module relationships, and conventions.

### Permission modes (from least to most freedom)

| Mode | Behavior | Best for |
|---|---|---|
| **Plan Mode** | Plan, then approve before executing | Complex or high-risk features |
| **Approve** | Explicit approval for every change | The learning phase, or important changes |
| **Auto** | Runs automatically (default) | Routine work at scale |
| **YOLO** | Fully automatic, no approval | Disposable environments only |

**A notable point (quote):** "A mode where everything is auto-approved can actually be safer than one where the user approves each step one by one" — a study found that users who approve step by step tend to fall into "rubber-stamping," and are more likely to miss dangerous operations.

### Prompt specificity determines efficiency (example: fixing tests)

- ❌ **Vague:** "fix the failing tests" → leaves too much room for interpretation, and the tests themselves can end up being rewritten
- ✓ **Specific:** "The tests in `/tests/session.test.js` are failing. Run them, fix the implementation code (not the tests), and report back once all tests pass."

*Source: Anthropic "Claude Camp," summary of the "Getting Started with Claude Code" session*

## Getting Started with Claude Code (3/3)

### Context management / checkpoints

- **Stacking order:** system prompt → tool definitions → CLAUDE.md, etc. → conversation history
- **Handling it:** auto-summarization / `/compact` (creates a summary) / `/clear` (full reset) / Escape (interrupt)
- **Caching:** activates when you use the same model continuously within a short window; it expires on a model/effort/mode change, or after 5 minutes to 1 hour of inactivity
- **Checkpoints:** file state and conversation are automatically saved before and after each turn. Restore with Escape×2 or `/rewind` (⚠️ manual edits are not recorded)

### Skill / MCP

- **Skill** = a folder of `skill.md` + reference files + assets. Can be auto-generated just by asking in natural language, e.g. "create a skill for XYZ."
- **MCP** = a standard protocol for securely connecting to external systems (files, SharePoint, Jira, GitHub, Slack, databases, etc.)
- **Built-in review skills:** `/code-review` (code quality), `/security-review` (vulnerabilities), `/simplify` (deduplication and simplification)

### Best-practice checklist (key points)

- **At the start:** generate CLAUDE.md with `/init`, spell out conventions, explicitly choose a model (Opus recommended as default)
- **During execution:** switch to Plan Mode for anything complex, pin target files with `@file`
- **For efficiency:** set output style to Concise, take advantage of caching within 5 minutes, run `/compact` at natural breakpoints
- **To avoid risk:** check history with `/rewind` before approving, consider Approve Mode for production
- **For reuse:** turn frequently-used tasks into Skills and share them with the team

*Source: Anthropic "Claude Camp," summary of the "Getting Started with Claude Code" session*

## Moving to the Frontier

*Max Kirby (Anthropic Head of Strategy) · 45 minutes*

### 3 indicators of AI progress trends

1. **Exponential growth** — intelligence keeps improving; currently judged to be in a vertical-ascent phase
2. **Agent persistence** — "Claude can now keep running for months at a time." The doubling period for capability has shortened from 7 months to 4 months.
3. **Falling token costs** — dropping more than 10x per year. The technology is still early-stage, and further declines are expected.

### 2 business-strategy mindsets

- **Innovation Mindset** — use the newest, highest-performing model to solve problems that were previously impossible
- **Diffusion Mindset** — prioritize cost efficiency with cheaper models, aiming for broad adoption

It's strongly recommended not to mix the two.

### 3-stage implementation framework

1. **Adoption first** — adopt within your own organization
2. **Choose a business strategy**
3. **Choose a lane** — Frontier Building / Frontier-Ready / Adoption-First

**Closing words (quote):** "When everyone can do almost anything, what matters most is knowing what you should do." *How* is unstable, so focusing on *What* and *Why* becomes the competitive advantage.

*Source: Anthropic "Claude Camp," summary of the "Moving to the Frontier" session*

## Proving Value and Controlling Cost (1/2)

*Shannon (Customer Success) & Noel (Finance) · 90 minutes · first APAC session*

### 4 levers of cost management

1. **Model selection** — Haiku/Sonnet/Opus/Fable. Opus 5.5, just announced, is "cheaper and higher quality." Setting a default model is recommended.
2. **Effort level** — low/medium/high. Higher levels consume more tokens. Medium is generally sufficient in most cases.
3. **Group-based access control (RBAC)** — e.g. a developer group (Code + Cowork, access to higher-tier models) vs. base users (Cowork only, limited to Sonnet)
4. **Spending caps** — three tiers: organization / group / user. Currently monthly only (there's strong demand for weekly/biweekly caps).

**User notifications:** alerts at 75% and 90% of usage, a notification when the cap is reached, with an option to approve additional spend via Slack.

*Source: Anthropic "Claude Camp," summary of the "Proving value and controlling cost" session*

## Proving Value and Controlling Cost (2/2)

### 6-step methodology for realizing value

1. Select a workflow (must meet 3 conditions: trigger, reproducibility, output)
2. Agree on a baseline (confirm current cost/time with Finance)
3. Implement as a Skill
4. Classify the value (cost avoidance vs. value creation)
5. Quantify it
6. Roll it into the CFO sheet

### Example: cost avoidance (Legal)

Contract review time: **11 hours/case → 6 hours/case (45% reduction)**. Quarterly volume of **610 cases** × 6-hour reduction per case = **3,660 hours saved**. "Equivalent to **$300–500K** in cost avoidance."

### Example: value creation (Sales)

Deal-prep time: **45 minutes → 10 minutes (3x faster)**. **190 additional deal opportunities.** 25% close rate × average **$20–34K** deal size → additional revenue of **$900K–1.6M**.

### Value framework for Claude Code (hierarchy of measurement units)

**Volume** (shipped units) → **Code Assist rate** (share of work done with Claude, e.g. 18/22 = 82%) → **Business impact** (quantified time savings, e.g. 4 days → 1.5 days = 62.5% reduction).

**Phoenix Corp case study:** an 80-microservice migration saved roughly **$1.8M**. One ASX-listed company compressed migration costs from **seven figures** down to **$20–30K in token spend**.

### Step Change Process (contract review example)

From stage 1 (11h → 6h), stage 2 shifted to exception-based processing — reviewing only **180 exceptions out of 610 total cases (a 70% reduction)** — and a new baseline was re-agreed with Finance.

*Source: Anthropic "Claude Camp," summary of the "Proving value and controlling cost" session*

## AI Fluency

*Kristen Swanson (Anthropic Head of Education) · 43 minutes*

### The 4D framework (inner/outer loop)

- **Inner loop:** Description (explain clearly) ⇄ Discernment (critically evaluate the output)
- **Outer loop:** Delegation (choose what to hand off) ⇄ Diligence (keep AI use transparent)

**The "Discernment Tax" problem:** because models are verbose, users end up stuck doing heavy verification work.

**Example internal norm:** labeling titles with ❤️ (fully human-made) / 💪 (human + Claude collaboration) / 🤖 (fully Claude-made)

### The 3 elements of the AI-fluency ecosystem

- **Mindset** — the single most important attitude is "adapting to the pace of AI's evolution." Recommends holding "evaling parties" to try hard tasks whenever a new model comes out.
- **Skill** — emphasize "task success," not specific conversational techniques. Build it up through iteration and sharing knowledge across teams.
- **Access** — which models, interfaces (desktop/mobile/terminal), and features are available.

### 81,000-person survey

A wish to "build deeper expertise" and a fear that "skills will become obsolete" coexist. Recognizing both is key to leadership.

**Key quotes:** "Task success is the key." · "Mindset creates value faster than features." · "A culture of continuous learning and experimentation."

*Source: Anthropic "Claude Camp," summary of the "AI Fluency" session*

## Your First Real Workflow in Cowork (1/2)

*Maddie (moderator) · Anthony & Shannon (Q&A) · APAC Day 3 · 90 minutes*

### Chat vs. Cowork — when to use which

**Rule of thumb (quote):** "If it's a question, ask in Chat. If it's work, start it in Cowork." Chat is where you ask a question and get an answer; Cowork is where you specify the outcome you want and Claude gathers context → executes → delivers a finished file.

### Cowork's 4-stage execution loop

| Stage | What happens |
|---|---|
| **① Understand** | Asks questions to draw out expertise (e.g. "who is this for?") |
| **② Plan** | Makes the steps explicit — the cheapest stage at which to make corrections |
| **③ Execute** | Operates tools, files, and the web (can take several minutes) |
| **④ Verify & Deliver** | Checks consistency against existing files and delivers the finished product |

**Required environment:** the Claude desktop app (the browser version has limited features) · a laptop with local file access · sleep prevention (needed for scheduled recurring tasks)

**Setting up a connector (example: Google Calendar):** Settings → Customize → Connectors → "Connect" on the relevant service → log in and grant permission in the browser. For Team/Enterprise users, an Admin enables it org-wide first, then each user signs in individually — the "hotel key card" analogy: the Admin activates the floor, the user taps their card.

*Source: Anthropic "Claude Camp," summary of the "Your first real workflow in Cowork" session*

## Your First Real Workflow in Cowork (2/2)

### 5 hands-on demos (building a weekly status brief)

| Demo | Time | Description |
|---|---|---|
| **Demo 1: Create practice files** | 10 min | Generate dummy data such as meeting notes and task lists |
| **Demo 2: Gather information** | 15 min | Read every file in a folder plus the calendar, tag each line with its source; also demonstrates detecting contradictions and prompting the user to confirm |
| **Demo 3: Create a Skill** | 15 min | The "don't write it first, reflect afterward" philosophy — get a good result first, then ask Claude to "package what you just did into a Skill" |
| **Demo 4: Build a Project** | 20 min | A workspace combining files + instructions + memory; 3-tier access settings (view / comment / edit) |
| **Demo 5: A recurring scheduled task** | 10 min | Runs automatically every Monday at 8am; since it runs on the local PC, it fails if the PC is asleep or the app isn't running |

### The "U-curve" of building the habit, and approval settings

For the first 2–3 weeks, confirming everything step-by-step is actually slower than doing it manually — but past the break-even point, it accelerates sharply. Recommended order: **Manually Approve** (learning phase) to monitor it → once trusted patterns are established, switch to **Automatically Approve** → then share with the organization.

**Safety design (quote):** "It can only touch the folders it's been granted access to" — access is limited to the specified folder(s), and the feature can also be turned on/off at the organizational level.

*Source: Anthropic "Claude Camp," summary of the "Your first real workflow in Cowork" session*

---

# Part 5: Claude Frontier Academy

*Anthropic's enterprise AI talent development program, announced in 2025.*

**$100M investment · Goal: train 10,000 Frontier Deployed Engineers (FDEs) by the end of 2027 · 12-week residency · Running in San Francisco, New York, and London**

### Program structure (a medical-residency training model)

1. **Multi-day in-person training** directly with Anthropic engineers
2. **Simulated enterprise deployment exercises** — a mock walkthrough from use-case selection through security review
3. **Graded practical assessments**
4. **A 12-week residency back at their own organization**, leading a real Claude project they brought with them
5. **A final assessment**, leading to the "Claude Frontier Deployed Engineer (FDE)" badge

### Who it's for, and participating organizations

Organizations nominate hands-on software engineers with a track record of building with LLMs and helping others adopt AI within their business — each arrives with a named Claude project to lead upon return. Initial cohorts include engineers from Accenture, Bain, Capgemini, Commonwealth Bank of Australia, Deloitte, McKinsey, Morgan Stanley, and Novo Nordisk. The program builds on the existing **Claude Partner Network**, under which over 175,000 professionals have already earned Claude certifications.

*Source: Anthropic official news, "Claude Frontier Academy" (anthropic.com/news/claude-frontier-academy)*

---

# Part 6: Glossary & Wrap-Up

| Term | Meaning |
|---|---|
| **Agentic loop** | The mechanism by which a model repeatedly calls tools to achieve a goal |
| **CLAUDE.md** | A persistent memory file recording a project's conventions and context (auto-generated by `/init`) |
| **Skill / MCP / Plugin** | Skill = a reusable procedure; MCP = a standard protocol for connecting to external systems; Plugin = a bundle for distributing these |
| **4D framework** | The dual loop of Description, Discernment, Delegation, and Diligence |
| **Cost avoidance / Value creation** | Two axes for classifying value: reducing the cost of existing work, vs. generating new revenue or outcomes |
| **Cowork's 4-stage loop** | Understand → Plan → Execute → Verify & Deliver |

## Where This Note Fits

This is a study note specifically for Anthropic's **official** training materials (Academy and Camp) — distinct from Claude CCA-F exam prep or GitHub strategy guides — and is expected to be revised as further sessions and materials become available.
