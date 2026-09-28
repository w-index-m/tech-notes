# Claude Certified Architect - Foundations (CCA-F) Exam Study Guide

> Translated from the Japanese original: `cca-f-study-guide.md`

> This guide systematically covers the 5 domains of the CCA-F exam, based on Anthropic's official documentation (docs.claude.com / code.claude.com) and information from several third-party certification prep sites. It is written with the actual exam format (scenario-based, 4-choice questions) in mind, and each item also notes "How this is tested on the exam" where relevant.

---

# Table of Contents

1. Domain 1: Agentic Architecture & Orchestration (27%)
2. Domain 2: Tool Design & MCP Integration (18%)
3. Domain 3: Claude Code Configuration & Workflows (20%)
4. Domain 4: Prompt Engineering & Structured Output (20%)
5. Domain 5: Context Management & Reliability (15%)
6. Deep Dive: Implementation-Level Design Principles (16 items)
7. Appendix: Key Terminology & Common Wrong-Answer Patterns
8. Supplement: Cost Optimization, AI Fluency, Bedrock/Vertex, and 6 Scenarios in Detail

---

# Domain 1: Agentic Architecture & Orchestration (27% — the most heavily weighted domain)

## 1-1. How the Agentic Loop Works

At the core of Claude's agentic system is a loop that repeats "evaluate → execute tool → incorporate result." The mechanics are as follows.

1. **Receive the prompt**: The user's prompt, the system prompt, tool definitions, and conversation history are passed to Claude
2. **Evaluate and respond**: Claude assesses the situation and returns either a text response, one or more tool calls, or both
3. **Execute the tool**: The requested tool is executed and its result is collected. This result is fed back in as input for Claude's next decision
4. **Repeat**: Steps 2-3 repeat until there are no more tool calls. This single cycle is called a "turn"
5. **Return the result**: The final text response is returned, along with a result that includes token usage, cost, and session ID

**The key criterion**: whether the loop continues or ends is determined by the `stop_reason` field (`tool_use` or `end_turn`). Parsing the natural-language text content to decide whether the task is "done," or relying on a fixed iteration cap as the primary termination condition, are both anti-patterns.

> ⚠️ **How this is tested on the exam**: A typical question asks "which implementation correctly terminates the agentic loop?" and lists options such as ① checking whether the text contains the word "done" via regex, ② checking `stop_reason`, ③ force-terminating after a fixed 10 iterations. Option ② is the typical correct answer.

### Concrete example: the prompt "fix the authentication bug"

Since an abstract explanation alone can be hard to follow, let's trace turn by turn what actually happens.

| Turn | Claude's judgment ("evaluate") | Tool called ("execute tool") | Result returned ("feedback") |
|---|---|---|---|
| 1 | "First I need to understand the current state of the code" | Search the codebase for the string "login" with `Grep` | Text saying lines 23 and 47 of `auth.py` matched |
| 2 | "I want to look closely at the contents of that file" | Open `auth.py` with `Read` | The full file contents (e.g., password-validation code) |
| 3 | Judges "the comparison operator on line 47 being `is` instead of `==` is likely the cause" | Rewrite that line of `auth.py` with `Edit` | Result: "the edit is complete" |
| 4 | "I want to actually run this and verify the fix is correct" | Run `pytest tests/test_auth.py` with `Bash` | The test result (all passed, or a failure log) |
| 5 | Confirms all tests passed and judges no more tools need to be called | (no tool call) | `stop_reason: end_turn`, the loop ends, and the final response "the fix is complete" is sent to the user |

**Key points**:
- The "judgment (evaluation)" is always performed by Claude itself (the model). At every step it is thinking "should I read this code?" "should I run this command?"
- "Executing a tool" is Claude **calling a pre-built capability**; Claude is not magically rewriting files directly by itself. It uses tools with clearly defined roles: `Read` (read), `Write` (create new), `Edit` (rewrite), `Bash` (run commands), `Grep` (search)
- Each turn runs a small "judgment → tool call → result" cycle. This repeats four times, and only on the fifth pass does it reach `end_turn` (meaning "no additional tools are needed"), ending the whole loop
- "Creating something with a tool" (e.g., `Write`-ing a new file, `Edit`-ing code) is just one of many kinds of tools. Search, read, and execute are all tools of equal standing

## 1-2. Model-Driven vs. Predefined Decision Trees

- **Model-driven (recommended)**: Claude judges which tool to call next based on the conversational context at each step. Highly flexible, and able to handle unexpected situations
- **Predefined decision tree**: Follows a fixed, predetermined order of tool calls. Suited to simple tasks, but lacks flexibility and isn't really "agentic"

CCA-F tests your judgment of "why choose an agentic architecture, and when is a simple workflow sufficient?" When a task is deterministic with few branches, a workflow is appropriate; when it requires exploration or adaptive judgment, an agent is appropriate.

## 1-3. Subagents and Context Isolation

A subagent is an **independent agent instance** invoked by the main agent. Four benefits that come up frequently on the exam:

| Benefit | Description |
|---|---|
| **Context isolation** | A subagent starts as a new conversation; its intermediate tool calls and results stay within itself. Only its final message is returned to the parent |
| **Parallelization** | Multiple subagents can run simultaneously (e.g., running style-checker, security-scanner, and test-coverage in parallel during a code review) |
| **Specialized instructions** | Each subagent can be given a dedicated system prompt and specialized knowledge |
| **Tool restriction** | Subagents can be restricted to specific tools only (e.g., a review-only agent is allowed Read/Grep but not Edit) |

**What subagents do and do not inherit**:
- Inherited: their own system prompt, the project's CLAUDE.md, tool definitions
- Not inherited: the parent's conversation history, the parent's system prompt, Skills (unless explicitly specified)

> ⚠️ **Important constraint**: A subagent cannot itself spawn further subagents (no nesting).

**How to configure them**: two approaches — programmatic definition (specifying `description`, `prompt`, `tools`, `model`, etc. via the `agents` parameter) or file-based definition (placed as Markdown files under the `.claude/agents/` directory). Programmatic definitions take priority over file-based ones.

## 1-4. Multi-Agent Topology Patterns

- **Hub-and-spoke**: A central orchestrator distributes tasks to multiple subagents and integrates the results. The most common pattern
- **Pipeline**: Processing is chained serially, e.g., Agent A → B → C
- **Peer-to-peer**: Agents interact directly with each other (close to an "agent team" style feature)

**Handoffs and error propagation between agents**: You are tested on how an error occurring in one agent propagates to downstream agents, and on the design of who detects the failure and decides to retry or escalate.

## 1-5. Task Analysis and Decomposition

- Deciding between sequential and parallel execution: sequential is appropriate when tasks have dependencies on each other; parallel is appropriate when they are independent
- Dynamic replanning: the mechanism by which Claude revises its plan when circumstances change mid-execution
- Handling ambiguity: when a task is unclear, designs that insert a check with the user (the AskUserQuestion tool) or a pre-execution review via Plan Mode

---

# Domain 2: Tool Design & MCP Integration (18%)

## 2-1. MCP (Model Context Protocol) Basics

MCP is an open standard for connecting AI agents to external tools and data sources. It enables database queries and integration with APIs such as Slack and GitHub without custom implementation.

**Four connection methods (transports)**:

| Method | Use case | How to identify it |
|---|---|---|
| **stdio** | Local process (runs on the same machine) | When the documentation shows an execution command (e.g., `npx server-github`) |
| **HTTP** | Cloud-hosted remote API | When the documentation shows a URL |
| **SSE** | Streaming version of HTTP | Same as above (when streaming is required) |
| **SDK MCP server** | Custom tools defined directly within your app code | When you implement the tool yourself |

## 2-2. Tool Naming Conventions and Permission Grants

- MCP tools follow the naming convention `mcp__<server-name>__<tool-name>` (e.g., `mcp__github__list_issues`)
- Claude cannot call an MCP tool without explicit permission (`allowedTools`). A wildcard (`mcp__github__*`) can bulk-allow all tools from a specific server

> ⚠️ **How this is tested on the exam**: "Does setting `permissionMode: 'acceptEdits'` also auto-approve MCP tools?" → **NO**. `acceptEdits` only auto-approves file edits; MCP tools are not covered. `bypassPermissions` does auto-approve MCP tools, but it also disables every other safety check, making it too broad. **Specifying a wildcard in `allowedTools` is the most appropriate approach** — this is a frequently seen correct-answer pattern.

## 2-3. Authentication Methods

- **stdio servers**: API keys/tokens are passed via environment variables (the `env` field)
- **HTTP/SSE servers**: Credentials are passed via the `headers` field, e.g., `Authorization: Bearer <token>`
- **OAuth2**: The MCP spec supports OAuth 2.1. The SDK itself does not automatically handle the OAuth flow, so the application must complete the flow itself and then pass the access token via headers

## 2-4. MCP Tool Search

Configuring many MCP tools can consume a large fraction of the context window just for the tool definitions. Tool Search is a mechanism that holds tool definitions back from the context and loads, on each turn, only what Claude actually needs. It is enabled by default.

> ⚠️ **How this is tested on the exam**: A frequently seen pattern presents "how to address context pressure caused by connecting many MCP servers," with using Tool Search (dynamic tool loading) as the correct answer.

## 2-5. Tool Boundary Design, Resources, and Prompts

- **Tool boundary design**: Don't cram too much functionality into a single tool. Keep responsibilities clearly separated, at a granularity where Claude can easily judge "which tool should I use, and when?"
- **Resources**: Read-only context data (files, DB records, etc.) provided by an MCP server. Can be referenced without a tool call
- **Prompts**: Reusable prompt templates defined on the MCP server side. Can be invoked like slash commands

## 2-6. Error Handling and Troubleshooting

Causes of MCP server connection failures that are commonly tested:
- Required environment variables (tokens, etc.) are not set
- The server is not installed (for an `npx` command, the package doesn't exist or Node.js isn't installed)
- A malformed connection string (in the case of a DB connection)
- A network reachability problem (in the case of a remote HTTP/SSE server)

By checking the `status` of the `mcp_servers` field within the `init` subtype of the `system` message, you can detect connection failures before agent execution.

---

# Domain 3: Claude Code Configuration & Workflows (20%)

## 3-1. CLAUDE.md (Persistent, Project-Level Instructions)

CLAUDE.md is a file that communicates a project's conventions, rules, and caveats to Claude on an ongoing basis. Characteristics:

- **Priority by location**: it exists at multiple layers — the project root, subdirectories (can be placed per package in a monorepo), and the user level (`~/.claude/`) — and is loaded hierarchically
- **Resent with every request, but prompt-cached**: it is loaded at session start and re-injected into every subsequent request, which means instructions are unlikely to be lost even after compaction (summarization). This is the decisive difference between CLAUDE.md and "one-off prompt instructions"
- **Auto Memory**: there is also a mechanism by which Claude automatically records what it learns, which exists on a separate axis from manually managing CLAUDE.md

> ⚠️ **How this is tested on the exam**: A typical question is "where should you write instructions that must not be lost even after compaction?" → CLAUDE.md (not the initial prompt), because CLAUDE.md is always re-injected even when the conversation gets summarized.

## 3-2. Agent Skills (SKILL.md)

Skills are a mechanism for turning "frequently used procedures, checklists, and multi-step work" into invocable commands. The decisive difference from CLAUDE.md lies in **context cost**.

| Item | CLAUDE.md | Skill |
|---|---|---|
| When it's loaded | Always loaded at session start | Its body is loaded only when invoked (only the description is always loaded) |
| Suited for | Facts and rules that must always be honored | Procedures, checklists, workflows for specific tasks |
| Context cost | High (fully loaded every time) | Low (only when needed) |

**Components of SKILL.md**:
- YAML frontmatter (`description` is the most important — it's the material Claude uses to judge when to invoke it)
- `disable-model-invocation: true` — prohibits Claude from auto-invoking it, allowing only explicit invocation via `/skill-name` by the user (used for operations with side effects, e.g., deploys, commits)
- `user-invocable: false` — conversely, cannot be invoked by the user; only Claude invokes it based on contextual judgment (for background knowledge)
- `allowed-tools` — pre-approves the tools allowed while the skill runs
- `context: fork` — runs the skill as a subagent (an isolated context)
- Best practice is to bundle supporting files (`reference.md`, `scripts/`, etc.) and keep SKILL.md itself concise (recommended under 500 lines)

> ⚠️ **How this is tested on the exam**: "How should you implement a frequently used, multi-step procedure that doesn't need to sit in context at all times?" → the judgment being tested is that you should factor it out as a Skill rather than writing it into CLAUDE.md.

## 3-3. Plan Mode

Plan Mode is a mode in which Claude executes no tools at all and instead **presents only a change plan for a human to review**.

- Used as a preliminary stage before large-scale refactors or tasks involving destructive changes
- Only after review and approval does it move on to actual execution (edits, running commands)
- It forms the core of the "explore first → plan → implement" three-stage best practice

## 3-4. Hooks

Hooks are callbacks that fire at specific moments in the agentic loop, and they **run outside Claude's context window (within the user's own application process)**, so they consume no context.

**Representative hook events**:

| Hook | Fires at | Main use |
|---|---|---|
| `PreToolUse` | Before a tool executes | Input validation, blocking dangerous commands |
| `PostToolUse` | After a tool executes | Auditing output, triggering side effects |
| `UserPromptSubmit` | When a prompt is submitted | Injecting additional context |
| `Stop` | When the agent finishes | Verifying results, saving session state |
| `SubagentStart`/`SubagentStop` | When a subagent starts/finishes | Tracking and aggregating results of parallel tasks |
| `PreCompact` | Before context compression | Archiving the full transcript before compression |

**Important point**: if a `PreToolUse` hook rejects a tool call, that tool is not executed and Claude receives a rejection message (i.e., hooks can short-circuit control of the loop).

> ⚠️ **How this is tested on the exam**: "I want to validate a dangerous Bash command before execution and block it. Which hook should I use?" → `PreToolUse` is correct. "I want to leave an audit log of a tool's execution result" → `PostToolUse` is correct — this pairing is a frequently seen pattern.

## 3-5. Integrating Claude Code into CI/CD

- **GitHub Actions**: the official Action that can be triggered via an `@claude` mention to request a code review or implementation. Discussion points include CLAUDE.md configuration, security considerations (minimizing write permissions to the repository, etc.), and cost optimization
- **GitLab CI/CD**: similar integration is possible. Can be combined with cloud authentication methods such as OIDC/Workload Identity Federation
- **Code Review feature**: automatic review on pull requests. Review criteria can be customized via REVIEW.md

## 3-6. Permission Modes

| Mode | Behavior |
|---|---|
| `default` | Tools not covered by a permission rule require an approval callback |
| `acceptEdits` | File edits are auto-approved; everything else follows the default rules (MCP tools are not covered) |
| `plan` | No tool execution; only a plan is presented |
| `dontAsk` | Only pre-approved tools are executed; everything else is rejected (no prompt) |
| `bypassPermissions` | All approved tools are executed without confirmation (for isolated environments only) |

---

# Domain 4: Prompt Engineering & Structured Output (20%)

## 4-1. Why Structured Output Is Needed

Agents return free-form text by default, but handling output programmatically requires typed data. In the Claude Agent SDK, you **define the shape of the output with a JSON Schema** and pass it via the `outputFormat` (TypeScript) / `output_format` (Python) option to receive validated JSON in the `structured_output` field.

**Flow of operation**:
1. Define a JSON Schema (supports objects, arrays, enums, required fields, etc.)
2. The agent freely uses whatever tools it needs while carrying out the task (compatible with multi-turn tool use)
3. The final response is validated against the schema. On a mismatch, it is automatically re-prompted
4. If it succeeds within the retry limit, the result is `success`; if it fails, `error_max_structured_output_retries`

## 4-2. Type-Safe Schema Definitions (Zod / Pydantic)

- **TypeScript**: define the schema with Zod and convert it to JSON Schema with `z.toJSONSchema()`. Use `safeParse()` to validate and obtain a typed object at the same time
- **Python**: define the schema with Pydantic's `BaseModel` and convert it with `.model_json_schema()`. Use `model_validate()` to validate

This offers advantages over hand-written JSON Schema in terms of type inference, error messages, and reusability.

## 4-3. Error Handling Best Practices

- **Keep the schema simple**: deep nesting or too many required fields can make satisfying the schema difficult, depending on the nature of the task
- **Make fields optional to match the task**: when information isn't always available (e.g., some files may have no `git blame` info), the corresponding field should be optional
- **Avoid ambiguous prompts**: if the prompt is ambiguous, it becomes hard for the agent to judge what output it should produce

> ⚠️ **How this is tested on the exam**: A typical question asks "what is the most likely cause when structured output keeps failing?" listing options such as ① the schema is overly complex / has too many required fields, ② the prompt is ambiguous, ③ tools are being executed in the wrong order. ① and ② tend to be the correct answers.

## 4-4. Context Engineering

- **Prompt caching**: content that doesn't change across turns — system prompt, tool definitions, CLAUDE.md, etc. — is automatically cached, reducing cost and latency
- **Few-shot prompting**: showing a few concrete examples of the expected output format increases output consistency
- **Extraction pattern**: for tasks that "extract specific, typed information from a large volume of documents," a typical design combines structured output with tool use (search, grep, etc.)

---

# Domain 5: Context Management & Reliability (15%)

## 5-1. Components of the Context Window

The main elements that consume an agent's context:

| Element | When loaded | Impact |
|---|---|---|
| System prompt | Every request | A fixed, small cost |
| CLAUDE.md | At session start | Full content is sent every time, but cached after the first request |
| Tool definitions | Every request | A schema is added per tool; can be switched to dynamic loading via MCP Tool Search |
| Conversation history | Accumulates with each turn | Prompts, responses, and tool inputs/outputs accumulate |
| Skill descriptions | At session start | Only a short summary; the body is loaded only when invoked |

## 5-2. Automatic Compaction

As the context window approaches its limit, the SDK automatically summarizes older conversation history, keeping recent exchanges and important decisions while freeing up space.

- Compaction appears in the stream as a message of `type: "system"`, `subtype: "compact_boundary"`
- Because compaction replaces older messages with a summary, **specific instructions given early in the conversation may be lost**
- Mitigation: write rules that must persist to CLAUDE.md (which is re-injected every time). A `PreCompact` hook can also be used to archive the full transcript before compression

## 5-3. Handoffs and Error Propagation Between Multiple Agents

- A subagent starts as a new conversation and **inherits none of the parent's conversation history or the parent's tool results whatsoever**. The only channel from parent to subagent is the prompt string passed to the Agent tool, so any necessary file paths, error messages, or decisions must be explicitly included in the prompt
- The subagent's final message is returned to the parent as a tool result, but the parent may summarize it within its own response. If you want it preserved verbatim, you must explicitly instruct this, for example in the main query's system prompt

## 5-4. The Limits of Self-Review and Independent Review Instances

This is a point the exam clearly emphasizes.

- **Limits of self-review**: an agent that generated code within the same session retains the reasoning context from generation time, making it prone to trust its own judgment too readily (a state close to confirmation bias)
- **Independent review instances**: reviewing with a separate Claude instance that does not carry the generating side's reasoning context makes it easier to catch subtle problems
- **Multi-pass review**: splitting a large-scale review into ① a per-file local analysis pass and ② a cross-file integrated analysis pass prevents diluted attention and contradictory findings

> ⚠️ **How this is tested on the exam**: "What is the most effective design for quality verification after code generation?" → not self-review via extended thinking within the same instance, but **review by a second, independent Claude instance** — this is a frequently seen correct-answer pattern.

## 5-5. Other Design Patterns for Reliability

- **Setting turn/budget caps** (`max_turns`/`maxTurns`, `max_budget_usd`/`maxBudgetUsd`): with no limit, there's a risk of runaway behavior on open-ended tasks, so setting a budget cap is the default best practice for production agents
- **Escalation design**: when execution stops due to an error, decisions to escalate to a human or auto-retry are made by looking at `stop_reason` (`end_turn`/`max_tokens`/`refusal`) or the `ResultMessage`'s `subtype` (`error_max_turns`/`error_max_budget_usd`/`error_during_execution`, etc.)

---

# Deep Dive: Implementation-Level Design Principles

From here on, based on Anthropic's official prep PDF material (a slide-format implementation guide), we go further into implementation-level detail beyond the study guide so far. Since the exam's scenario questions test "can you make an implementation judgment call" rather than "do you know the facts," the content in this chapter tends to translate directly into exam points.

## Deep Dive 1: 5 Principles for Coordinator/Subagent Collaboration (Domain 1)

In addition to the subagent basics from section 1-3, these are the design principles for the coordinator side that ties together multiple subagents.

| Principle | Content |
|---|---|
| **① One Hub, Many Spokes** | Subagents never talk to each other directly; everything goes through the coordinator. Consolidating into a single hub makes logging, debugging, and failure recovery easier |
| **② Pick the Right Helpers** | Read the content of the query first, then judge which subagents are truly needed before calling them. Spinning up every agent every time wastes cost |
| **③ Split Without Gaps** | Divide the work without overlap and without gaps. Avoid the failure pattern where two agents are assigned similar scopes, and as a result no one ends up covering a key item |
| **④ Check, Refill, Repeat** | Check the results first returned by the subagents, and if you detect missing pieces, re-delegate them as additional tasks. Repeat this until it is complete, and only then integrate |

> ⚠️ **How this is tested on the exam**: "Should you immediately compile the final report as soon as you've collected the subagents' results once?" → No. Detecting gaps, re-delegating, and only then integrating is correct (Check, Refill, Repeat).

## Deep Dive 2: 7 Principles for Spawning Subagents (Domain 1)

These are principles at a level closer to implementation — **how** to spawn subagents.

| Principle | Content |
|---|---|
| Enable the Task tool | If `"Task"` isn't included in `allowedTools`, subagents cannot be spawned at all |
| Pack in the context | Since a subagent starts with zero memory, the topic, existing findings, and purpose must be explicitly included in the prompt |
| Clearly define the role | An `AgentDefinition` = description + system prompt + available tools, defined together as a set |
| **Fork to explore options** | Build a common baseline process (e.g., an analysis) only once, then branch multiple strategies from it for comparison. Don't redo the same analysis over and over |
| Always attach sources | Findings must always retain their source (URL, document name, page) as structured data so they can be verified later |
| Spawn in parallel | Issue multiple Task calls together within the same turn to run faster than sequential execution |
| Hand over goals, not steps | Rather than a fixed sequence of steps, handing over a "goal" and "quality criteria" lets the agent itself adapt when, for example, a search fails |

> ⚠️ **How this is tested on the exam**: "I want to compare different strategies across multiple subagents, but doing the same preprocessing twice is inefficient" → forking a shared baseline for reuse is the correct pattern.

## Deep Dive 3: Workflow Control and Handoff Patterns (Domain 1 & 3 combined)

| Pattern | Content |
|---|---|
| **Code Beats Prompts** | An instruction like "verify X before issuing a refund" may not be reliably followed if it's only written in the prompt. Critical rules must be enforced in code (via a Hook, etc.). A prompt can only "ask"; code can "refuse" |
| **Gate the Critical Step** | Design it so that the critical tool itself (e.g., issuing a refund) refuses to execute until a precondition (e.g., identity verification) is satisfied |
| **One Request, Many Concerns** | When a single request mixes multiple concerns (e.g., a car's oil change, brakes, and air conditioning), decompose it and process each in parallel, then resolve them together |
| **Hand Off the Whole Story** | When handing off to a human or another agent, pass the full context as summarized, structured data. Include not just "the customer's problem" but the symptoms, history, and what's already been tried, so the recipient doesn't have to ask again |

> ⚠️ **How this is tested on the exam**: "Can a critical action like a refund be reliably controlled by prompt instructions alone?" → No. Enforcement on the code side via a Hook (e.g., rejection in `PreToolUse`) is required — a frequently tested point common to Domains 1 and 3.

## Deep Dive 4: Intervention and Data Normalization via Hooks (Domain 1 & 3 combined)

| Pattern | Content |
|---|---|
| **Clean Before Reading (PostToolUse)** | Normalize a tool's output (e.g., date formats) to a standard form before handing it to the AI. Unifying inconsistent notations (01/05/2025, May 1, 1-5-25) stabilizes the AI's understanding |
| **Catch the Call Mid-Air (PreToolUse)** | Check a tool call before it executes, and stop rule violations (e.g., a refund amount exceeding the cap) preemptively |
| **Block Then Redirect** | Don't just reject the request outright — redirect it to an appropriate alternative workflow (e.g., escalating to a manager for approval) |
| **Hooks Hold the Line** | Rules that must never be broken — e.g., "get approval before deleting" — should be guaranteed by Hooks. Prompts are probabilistic; Hooks are deterministic |

> ⚠️ **How this is tested on the exam**: The division of labor is tested clearly — PostToolUse for normalizing/cleaning up output after execution, PreToolUse for validating/blocking before execution. The design of "not just blocking, but redirecting to the appropriate next step" (Block Then Redirect) is also frequently tested.

## Deep Dive 5: Task Decomposition — Chain vs. Adapt (Domain 1)

The strategy for task decomposition should be chosen to match the nature of the workflow.

| Type | Suited to | Example |
|---|---|---|
| **Chain** | Tasks whose procedure is known and predictable | Running security check → tests → doc check, in that order |
| **Adapt** | Unknown, exploratory tasks | Like apartment hunting, adjusting the plan each time new information arrives |

Two additional principles are also important here:

- **Per-File First, Cross-File Last**: when reviewing multiple files, first review each file individually, then perform a cross-file consistency check. Doing both at once scatters attention and increases the chance of missing things
- **Adapt as You Discover**: for exploratory tasks, don't form a complete plan up front — cycle through "explore → prioritize → adjust the plan"

> ⚠️ **How this is tested on the exam**: "Should the same fixed workflow be applied to both a task with a known procedure and an unknown, exploratory task?" → No. The correct approach is to use Chain for known tasks and Adapt (adaptive planning) for unknown tasks.

## Deep Dive 6: Managing Session State (Domain 1 & 5 combined)

| Pattern | Content |
|---|---|
| **Name It, Then Resume It** | Give important sessions a name so they can later be resumed with `--resume <name>` without losing context |
| **Fork to Compare Paths** | Use `fork_session` to create multiple independent branches from a shared analysis (baseline), comparing different strategies without re-analyzing |
| **Tell the Agent What Changed** | When resuming a session after a code change, specifically state what changed, e.g., "the auth file changed." Re-analyzing just the diff is more efficient than having the agent re-investigate everything |
| **Stale? Start Fresh Instead** | When old information no longer matches reality, don't force a resume — starting a fresh session and carrying over only a summary is more accurate |

> ⚠️ **How this is tested on the exam**: "What is the most efficient instruction when resuming a session after a code change?" → specifically naming the changed files and having only the diff re-analyzed (not a full re-investigation) is the correct pattern.

## Deep Dive 7: Designing Tool Descriptions (Domain 2)

| Principle | Content |
|---|---|
| The description determines the choice | Claude reads the `description` to choose a tool. An ambiguous description is a direct cause of wrong choices. Spell out the input format, examples, edge cases, and limitations fully in the description |
| Eliminate duplication | Two tools with similar descriptions cause miss-selection. Give each tool a clearly distinct purpose. Multiple purpose-specific tools select more accurately than a single general-purpose tool |
| Audit the system prompt | Even a good tool description can be overridden by strong wording like "always"/"never" elsewhere in the system prompt. If tool selection seems off, also suspect the system prompt |
| **4–5 tools is optimal** | An agent can select correctly among 4–5 tools, but giving it 18 turns selection into a guessing game. Remove tools unrelated to the role (Role-Scoped Tools) |

> ⚠️ **How this is tested on the exam**: "An agent is frequently choosing the wrong tool. What's the cause?" → the correct line of reasoning is to suspect ① ambiguous/duplicated descriptions, ② too many tools, and ③ interference from strong wording in the system prompt.

## Deep Dive 8: 6 Principles of Structured Error Responses (Domain 2)

| Principle | Content |
|---|---|
| Set the `isError` flag | Explicitly set `isError: true` on failure. Without it, the agent mistakenly assumes success |
| **Distinguish 4 kinds of errors** | transient (temporary → can retry) / validation (invalid input → fix and retry) / business (business rule violation → explain the reason) / permission (insufficient permission → escalate) |
| Structured metadata | Don't just say "an error occurred" — state clearly what happened and what to do next |
| Explain in plain language | When blocked by a business rule, clearly communicate why it was blocked |
| Local recovery plus escalation | Recover automatically from small errors, and escalate only when unresolvable |
| Distinguish empty results from errors | "Zero search results" is a normal response, and should be treated differently from "access failed" |

> ⚠️ **How this is tested on the exam**: "A search tool returned zero results. Should this be treated as an error?" → No. An empty result is a normal response. `isError: true` should be reserved for actual failures like access failures — this distinction comes up frequently.

## Deep Dive 9: When to Use Each `tool_choice` Setting (Domain 2)

| Mode | Behavior | When to use |
|---|---|---|
| `auto` (default) | Claude also decides whether to use a tool at all | Ordinary conversation, flexible responses |
| `any` | Forces one of the tools to be called | Situations where tool execution (not a text response) is mandatory (e.g., structured extraction) |
| Forcing a specific tool | Forces exactly one specified tool to be called | Forcing a step that must run first (e.g., identity verification), then reverting to `auto` afterward |

Supplementary principles:

- **Constrained Replacement**: replace a dangerous general-purpose tool (e.g., arbitrary SQL execution) with a safely restricted, dedicated version (only specific queries can be run)
- If you want to reliably get JSON output, using tool use is the only reliable method — more reliable than asking in the prompt to "answer in JSON"

> ⚠️ **How this is tested on the exam**: "I want to force an identity-verification tool to run first, every time" → forcing that specific tool via `tool_choice` only for the first step, then reverting to `auto` afterward, is the correct pattern.

## Deep Dive 10: MCP Config Files vs. Built-in Tools (Domain 2)

**When to use which MCP config file**:
- `.mcp.json` (at the project root): team-shared server configuration. Committed to git
- `~/.claude.json`: personal server configuration. Not shared
- Secrets such as tokens are expanded via environment variables and never written directly into `.mcp.json` (to prevent leaking them via git)

**When to use which built-in tool**:
- `Glob` = search by file name / `Grep` = search inside file contents
- `Read` = view, `Edit` = partial modification, `Write` = full replacement
- When the target of an `Edit` replacement is ambiguous, switch to `Read` + `Write`

**Principle for codebase exploration**: rather than reading every file, follow one clue at a time (a `Grep` hit → `Read` the matching file → trace back to the caller). Don't stop at the first function name you find — trace every path that leads to it.

> ⚠️ **How this is tested on the exam**: "Where should MCP server configuration that must be shared with the team be placed?" → `.mcp.json` (on the project side, managed by git). API tokens are correctly referenced via environment variables.

## Deep Dive 11: The CLAUDE.md Hierarchy, Path-Scoping Rules, and Skill Placement (Domain 3)

- CLAUDE.md has three layers: **user / project / directory**. Since the user-level file never rides along with git, team-wide rules must always be written at the project level (when "a colleague isn't following the rules," first suspect where the file is placed)
- `@import` can pull in shared standard files. Once a file exceeds one screen, split it by topic under `.claude/rules/` (focused splitting is preferable to one monolithic file)
- **Path-scoping rules (paths / glob)**: with a pattern like `**/*.test.tsx`, a rule can be loaded "only while editing the matching files" → this keeps context small. Copying the same CLAUDE.md into multiple folders is an example of an incorrect pattern
- The `/memory` command lets you check exactly which CLAUDE.md is actually being loaded at the moment (diagnose it instead of guessing)
- Custom commands are split between `.claude/commands/` (team-shared, managed by git) and `~/.claude/commands/` (personal). If you want to customize a team skill for your own personal use, copy it under a different name into `~/.claude/skills/` (don't edit the shared version directly)

> ⚠️ **How this is tested on the exam**: "There is a rule (e.g., for test files) that should only apply to a specific file type" → the correct answer is conditional loading via a path-scoping (glob) rule. Copying CLAUDE.md into every folder is a typical wrong-answer option.

## Deep Dive 12: Iterative Refinement and CI/CD Review Design (Domain 3)

**Iterative Refinement techniques**:
- When results aren't stable with prose instructions, switch to 2–3 concrete input/output examples
- Write tests first, and have Claude fix failures one at a time as you share them (TDD-style iteration)
- In an unfamiliar domain, have Claude interview you first, surfacing considerations you might have missed (cache-invalidation strategy, failure modes, etc.)
- Give feedback as a set of three: "input, actual output, expected output" (simply stating dissatisfaction won't fix it)
- Bundle changes that affect each other into one message; keep independent changes in separate, sequential messages (decide based on whether there's a dependency)

**CI/CD review design**:
- CI without `-p` will hang waiting for interactive input / put team rules in CLAUDE.md so they're automatically applied to Claude in CI as well
- Run the review in a separate session from generation / share past review findings to avoid repeating the same feedback / handing over existing test files lets you have only the new tests generated

## Deep Dive 13: JSON Schema Design and Self-Correcting Extraction (Domain 4)

| Principle | Content |
|---|---|
| The schema validates shape, not meaning | JSON Schema only guarantees structure. Business checks such as amount validity must be added yourself |
| **Required fields can incentivize fabrication** | Marking uncertain data as required tends to make Claude fabricate it just to fill it in. Uncertain fields should be nullable |
| Give enums an escape hatch | To avoid forcing a case that doesn't fit any option, always include something like `"other"` or `"unclear"` |
| Self-correction needs a set of three | On retry, hand over "the original document + the failed output + the specific validation error." A formatting error can be fixed by retrying, but retrying when the source document simply lacks the information is pointless (classify the error first, then decide whether to retry) |

**An extension of Few-shot** (a supplement to section 4-4):
- For ambiguous scenarios, prepare 2–4 examples that also show the reasoning behind the judgment
- Include examples that demonstrate the output format (location, issue, severity, remediation) to ensure consistency
- Show examples distinguishing "a genuine problem" from "an acceptable code pattern" to reduce false positives while still generalizing well
- When document structure is inconsistent (e.g., varying citation formats), show correct extraction examples drawn from a variety of structures

> ⚠️ **How this is tested on the exam**: "Missing fields in extraction results are being fabricated" → removing `required` and making the field nullable is correct. "Automatic retries on validation errors still don't fix it" → cases where the source document simply lacks the data should be classified as not eligible for retry.

## Deep Dive 14: Practical Judgment for the Batch API and Confidence-Based Routing (Domain 4)

**Criteria for deciding whether to use the Batch API**:
- It's 50% cheaper but has no SLA (up to 24 hours). Don't use it for anything someone is actively waiting on
- Batch is suited to single-shot requests. Multi-turn tool-use flows should run via the synchronous API
- Always attach a `custom_id` so you can identify and resend only the failed requests
- Validate your prompt on a small sample before sending a large production batch
- Decide your batch submission cadence by working backward from your own SLA (response deadline)

**Routing by confidence**:
- Have extraction/review results emit a self-reported confidence score
- High confidence → auto-process (auto-fix)
- Low confidence → route to human review
- This lets you concentrate human effort exactly where it's genuinely needed

> ⚠️ **How this is tested on the exam**: "Is the Batch API appropriate for classifying a large volume of documents overnight?" → YES (no one waiting, single-shot processing). "For a chatbot's responses?" → NO (a user is waiting). "For an agent's continuous tool execution?" → NO (multi-turn requires the synchronous API) — judging by this set of three is the key.

## Deep Dive 15: A Supplement to Multi-Agent Topology — Best of N

In addition to the three patterns introduced in section 1-4 (Hub-and-spoke / Pipeline / Peer-to-peer), there is one more important pattern.

- **Best of N**: have multiple agents execute the same task independently and in parallel, then select or synthesize the best result from among them. It costs more, but is effective in situations that demand accuracy and thoroughness, such as drafting an important decision.

## Deep Dive 16: Supplementary Exam-Taking Tips

- **Leaving a question unanswered counts as incorrect**. Even for a question you don't know, always select one answer by process of elimination (never submit a blank)
- The passing score (720/1000) is not a raw score but a **scaled score** (a value statistically adjusted for difficulty), so you cannot simply calculate "how many questions you need to get right." Scoring steadily across all domains, rather than being lopsided toward a specific one, is the shortest path to passing
- Of the three elements of MCP (**Tools / Resources / Prompts**), this guide devotes the most space to Tools, but the exam may also draw questions from Resources (read-only data) and Prompts (reusable templates), so it's recommended to review section 2-5 again

---

# Appendix: Key Terminology

| Term | Description |
|---|---|
| Agentic Loop | The execution cycle that repeats evaluate → execute tool → incorporate result |
| Turn | A single round-trip of the loop (Claude's response + tool execution + result feedback) |
| stop_reason | The reason the most recent response stopped (`end_turn`/`max_tokens`/`refusal`, etc.) |
| Subagent | An agent instance running in a context independent from the parent |
| Hub-and-spoke | A topology in which a central orchestrator ties together multiple agents |
| MCP | An open standard for connecting to external tools and data sources |
| Tool Search | A mechanism that dynamically loads only the needed tool definitions to save context |
| CLAUDE.md | The project's persistent instruction file (re-injected on every request) |
| Skill (SKILL.md) | A procedure/workflow definition whose body is loaded only when invoked |
| Plan Mode | A mode that presents only a plan, without executing any tools |
| Hook | A callback that fires outside the agent's context |
| Compaction | The automatic process of summarizing old history when context is tight |
| Structured Output | JSON output validated against a JSON Schema |
| Independent review instance | Review performed by a separate instance that doesn't inherit the generating side's reasoning |

## Frequent Wrong-Answer Patterns (Trap Options)

1. **"Parse the text content with a regex to determine loop termination"** → Wrong. Checking `stop_reason` is correct
2. **"`acceptEdits` mode also auto-approves MCP tools"** → Wrong. MCP tools are not covered; a wildcard in `allowedTools` is correct
3. **"Use a fixed iteration cap as the primary termination condition"** → Wrong (it can be a secondary safety-net cap, but it's inappropriate as the primary criterion)
4. **"Self-review within the same session is most effective"** → Wrong. Review by an independent instance is correct
5. **"Keep appending frequently used procedures to CLAUDE.md every time"** → From a context-cost perspective, factoring them out as a Skill is often more appropriate
6. **"A subagent can automatically reference the parent's conversation history"** → Wrong. It must be explicitly included in the prompt

---

---

# Supplement: Items Missing from the Original Summary

Cross-referencing the previous summary against Anthropic Academy's (anthropic.skilljar.com) official course catalog revealed the following gaps, which are added here.

## Supplement 1: Cost Optimization (relates to Domains 3 and 4; frequent in CI/CD scenarios)

The exam's "Claude Code for CI/CD" scenario tests knowledge of cost optimization.

### Cost management via model selection
The basic policy is to match the model to the complexity of the task. The judgment criterion tested is: Haiku-tier for simple classification/formatting work, Sonnet-tier for ordinary development work, and Opus-tier for advanced reasoning or refactoring.

### Message Batches API (asynchronous batch processing)
A mechanism that processes large volumes of requests that don't need an immediate response (data analysis, content moderation, large-scale evaluation, etc.) asynchronously, at **50% off the standard price for both input and output tokens**.

- Up to 10,000 requests can be submitted together per batch
- Processing typically completes within 24 hours (often within 1 hour)
- Results can be retrieved for 29 days after processing completes
- Can be combined with prompt caching (specifying the same `cache_control` across all requests in a batch can achieve roughly a 30–98% cache-hit rate)

> ⚠️ **How this is tested on the exam**: "I want to process a large volume of document-classification tasks that don't require an immediate response, at reduced cost" → using the Message Batches API is a typical correct answer.

### Combining with prompt caching to reduce cost
A prompt-cache read costs about 10% of the standard price; combined with the Batch API's 50% discount, the cost can theoretically be reduced to about 5% of the standard price (depending on the actual cache-hit rate). The effect is larger for workloads with a "long context that never changes each time," such as a system prompt.

## Supplement 2: Concrete Implementation Knowledge for the Claude Code CI/CD Scenario

More implementation-level knowledge tested in the "Claude Code for CI/CD" scenario.

### Non-interactive mode (the `-p` / `--print` flag)
When invoking Claude Code from a CI/CD pipeline or a script, use the `-p` (or `--print`) flag to run it once, non-interactively.

- `--output-format json`: returns the result in structured JSON form (also includes cost information as `total_cost_usd`)
- `--output-format stream-json`: for real-time streaming processing
- `--json-schema`: directly specifies the expected output type as a JSON Schema, forcing validated JSON
- `--allowedTools`: restricts which tools are allowed (e.g., `Read,Grep,Glob` limits it to a read-only analysis task)
- `--max-turns`: sets an upper limit on iteration count to prevent runaway behavior
- `--bare`: skips automatic loading of hooks, skills, plugins, MCP servers, and CLAUDE.md, giving highly reproducible behavior independent of the execution environment (a recommended setting for CI)

> ⚠️ **How this is tested on the exam**: "I want reproducibly identical results every time in a CI environment, without being affected by a developer's local settings (hooks, MCP, etc.)" → using the `--bare` flag is a typical correct answer.

### Session separation between generation and review instances
Domain 5's concept of an "independent review instance" is also tested in the CI/CD context. Running the review in a separate `claude -p` invocation (i.e., a new session) from the one that generated the code enables an objective review that doesn't inherit the reasoning context from generation time.

## Supplement 3: The AI Fluency Framework (the 4D Framework)

A foundational thinking framework for collaborating with AI, covered in Anthropic Academy's introductory course "AI Fluency: Framework & Foundations." It is positioned as prerequisite knowledge for CCA-F.

| Element | Content |
|---|---|
| **Delegation** | Deciding which tasks should be handed to AI, and when and how to stay involved |
| **Description** | Communicating your goal clearly to draw useful output from AI (the foundation of prompt engineering) |
| **Discernment** | Critically evaluating the usefulness and accuracy of AI's output and behavior |
| **Diligence** | Handling the final deliverable responsibly (attention to ethics, safety, and verification) |

These four correspond to three modes of collaboration — "Automation," "Augmentation," and "Agency (delegation/autonomy)" — and which D (element) matters most shifts depending on the mode. For example, in Automation mode, Delegation and Description carry the most weight, while in Agency mode (agentic tasks), Discernment and Diligence carry the most weight.

> ⚠️ **How this is tested on the exam**: rather than directly asking you to name the 4Ds, the exam is more likely to surface the ideas of Discernment and Diligence as the underlying philosophy behind Domain 5 (reliability) questions, phrased as things like "what should be considered first when delegating a task to AI" or "whose responsibility is it to verify AI output rather than accept it at face value."

## Supplement 4: Claude via Cloud Platforms (Bedrock / Vertex AI)

Anthropic Academy has dedicated courses — "Claude with Amazon Bedrock" and "Claude with Google Cloud's Vertex AI" — and these may fall within the exam's scope.

| Item | Amazon Bedrock | Google Cloud Vertex AI (now Agent Platform) |
|---|---|---|
| Authentication method | AWS IAM (no API key required) | Google Cloud authentication (Application Default Credentials, service accounts, etc.) |
| Model specification | Cross-region inference profile ID | Model ID in family + version format |
| Claude Code integration | `use_bedrock: true` (supports OIDC) | `use_vertex: true` / `CLAUDE_CODE_USE_VERTEX=1` (supports OIDC) |
| Main benefits | Billing and IAM governance within an existing AWS contract, audit logs via CloudTrail | Billing within an existing GCP contract, Workload Identity Federation |
| Billing model | Integrated into a cloud contract separate from the standard pay-as-you-go API | Same as above |

> ⚠️ **How this is tested on the exam**: "I want to use Claude while meeting governance and audit requirements integrated with AWS infrastructure" → using Claude via Amazon Bedrock is the anticipated correct answer. Consistency with an existing cloud contract and compliance requirements is the deciding axis.

## Supplement 5: The 6 Exam Scenarios (Detailed)

The exam randomly draws 4 out of these 6 scenarios. Here is a breakdown of the technical elements tested in each scenario.

| # | Scenario | Main points tested |
|---|---|---|
| 1 | **Customer support agent** | Agent SDK implementation, MCP tool integration, escalation-logic design, Hook-based compliance enforcement, structured error handling |
| 2 | **Code generation with Claude Code** | Selection of built-in tools (Read/Write/Bash/Grep/Glob), MCP server integration, codebase exploration strategy, distributing tools across multiple agents |
| 3 | **Multi-agent research system** (coordinator/subagent structure) | Multi-agent orchestration, context handoff, error propagation, result synthesis |
| 4 | **Developer productivity** (configuring a development team's workflow) | CLAUDE.md hierarchy configuration, choosing between Plan Mode and direct execution, custom slash commands, the TDD iteration pattern |
| 5 | **Claude Code for CI/CD** | Non-interactive execution with the `-p` flag, `--output-format json`, the Message Batches API, session separation between generation and review, multi-pass code review |
| 6 | **Structured data extraction** (building a pipeline from unstructured documents) | JSON Schema design, using `tool_use`, implementing a validate-and-retry loop, few-shot prompting, per-field confidence and human review |

## Supplement 6: Platform Developments as of August–September 2026 (reference information outside the exam scope)

The official Exam Guide remains at the July 2026 version (v1.0) and has not been updated, but the Claude and Claude Code platforms themselves have continued to be updated since then. The following is peripheral knowledge beyond the tested scope, and **understanding that "the underlying design principles don't change even when terminology gets renamed" should take priority over rote memorization**.

### Updates to the model lineup
In September 2026, Claude Fable 5.1 and Claude Mythos 5.1 were added to the top-tier models (of the Project Glasswing line). In August, Claude Opus 5 became the new default for complex agentic coding and enterprise use cases. Claude Sonnet 5, which this material primarily assumes, remains in active use as the general-purpose model.

### Renaming of subagent-related tools
The tool used to launch subagents was renamed from `Task` to `Agent` (August 2026). Custom subagents are now unified around being defined in the frontmatter of `.claude/agents/*.md` files.

> ⚠️ **How this is tested on the exam**: even if scenario text uses phrasing like "launch a subagent with the Agent tool," you should understand that the concepts you've already learned that correspond to `Task` (coordinator/subagent collaboration principles, the principle of context isolation, etc.) apply unchanged — there is no need to treat it as a separate new concept.

### Expansion of Hooks and Tool Search
The Hooks event catalog reached general availability (GA), expanding from 31 to 33 event types, and `PreModelSwitch`/`PostModelSwitch` were newly added. Tool Search reached GA in August 2026 and is on by default; the `defer_loading` approach, which addresses the performance degradation that starts to occur once tool count exceeds roughly 30–50, has become standard.

### Maturation of context-management features
Server-side context editing, automatic compaction, and the memory tool have matured to production-grade reliability, and Task budgets (beta) have also appeared. However, the design principles tested under Domain 5 ("Context Management and Reliability") — mitigating "Lost in the Middle," avoiding the Progressive Summarization Trap, preserving the claim-source mapping, and so on — remain unchanged.

---

*This material was compiled by cross-referencing Anthropic's official documentation (docs.claude.com / code.claude.com), Anthropic Academy's (anthropic.skilljar.com) course catalog, and information from several third-party certification prep sites. Actual exam content, weighting, and question trends are subject to change, so please check claude.com/partners or Anthropic Academy for the latest information before taking the exam.*

---

## Additional Practice Question Set (sourced from an overseas practice site, translated)

The following are additional practice questions collected from an unofficial overseas mock-exam site (claudecertificationguide.com, etc.), separate from Anthropic's official 12-question Exam Guide. The source is unofficial, but the content has been confirmed to be consistent with the sections of this guide.

### Additional Question 1

**Scenario**: CI/CD Pipeline Integration

> A DevOps team has integrated Claude Code into their CI/CD pipeline, handling PR review, test generation, security scanning, and deployment checks across a multi-language codebase that includes Terraform.


**Question**: In the CI/CD pipeline, one step has Claude Code generate tests, and the next step has it review that same code. The review step catches almost none of the problems in the generated tests, while human reviewers consistently find issues. Both CI steps correctly use the `-p` flag. What is the most likely cause and remedy?

- **A.** Generation and review should be run as independent sessions that share no context. That way the review side can approach the code without carrying over the original (generation-time) reasoning bias **(correct)**
- **B.** CLAUDE.md doesn't document test standards or review criteria, so the review lacks project-specific context
- **C.** The review step runs on the same model tier as the generation step and lacks the capacity to critique its own output. The review step should be upgraded to a more capable model
- **D.** The review step needs `--output-format json` to programmatically parse structured findings

**Explanation**: Running the review in the same session and context as generation causes the review side to also carry the generation-time reasoning ("this code should be correct"), making it hard to doubt its own judgment (confirmation bias). This is the same idea as "The Limits of Self-Review and Independent Review" from the deep-dive material — running the review in an independent session (a separate instance that shares no context) is the correct remedy. B (lack of standards in CLAUDE.md) would be a helpful supplementary improvement but not the root cause. C (upgrading the model) and D (output format) sound plausible but miss the point.

### Additional Question 2

**Scenario**: CI/CD Pipeline Integration

> A DevOps team has integrated Claude Code into their CI/CD pipeline, handling PR review, test generation, security scanning, and deployment checks across a multi-language codebase that includes Terraform.


**Question**: Across the codebase (50+ directories), test files are co-located with source files (e.g., `Button.test.tsx` next to `Button.tsx`). The team wants all tests, regardless of location, to follow the same conventions. What is the most maintainable approach?

- **A.** Create a skill under `.claude/skills/` with `paths` frontmatter, so it auto-launches every time a test file is edited
- **B.** Place a copy of a CLAUDE.md file in every one of the 50+ directories where test files and source co-exist
- **C.** Add all test conventions to the root CLAUDE.md file so they are automatically loaded at the start of every session
- **D.** Create a file under `.claude/rules/` with frontmatter `paths: ["**/*.test.tsx", "**/*.test.ts"]` and put the test conventions in it **(correct)**

**Explanation**: Conditional application via path-scoping rules (paths/glob patterns) is the role of `.claude/rules/`. Using a pattern like `**/*.test.tsx` to auto-apply per file type keeps the conventions consistent regardless of directory location. B is a classic wrong pattern (copying the same CLAUDE.md into 50 places is impractical and unmaintainable). C consolidates everything at the root, but that loads information unrelated to test conventions into every session, wasting context. A sounds plausible at first glance, but "automatic application based on file type" is the job of rules (path-matching), not skills (invocation-based) — this is the trap.

### Additional Question 3

**Scenario**: Developer Productivity Tools

> A platform engineering team is building internal developer tools on Claude. Automated code review, documentation generation, and codebase exploration are all built into the CI/CD pipeline.


**Question**: A developer-tool agent running on a version-controlled project has two guardrails: ① a PreToolUse hook that blocks writing files outside the project directory (100% enforced), and ② a system-prompt instruction that says "always create a backup before overwriting an existing file" (followed 88% of the time). A senior engineer argues that, for consistency, all guardrails should be converted to Hooks. What is the correct assessment?

- **A.** The senior engineer's argument is correct. All guardrails, including the recoverable backup rule, should be converted to deterministic Hooks for maximum reliability
- **B.** Both guardrails should be prompt instructions. Unifying on a single enforcement mechanism is easier to maintain than mixing Hooks and prompt rules
- **C.** Converting the directory restriction into a Hook is correct. The backup instruction, on the other hand, can stay as a prompt instruction, since a missed backup is recoverable (thanks to version control) **(correct)**
- **D.** The backup instruction should be converted to a Hook. A 12% failure rate on a documented guardrail is too high to leave to the prompt

**Explanation**: The key judgment axis is whether a failure's consequence is catastrophic or recoverable. Writing outside the directory would be catastrophic if it failed, so it should be reliably enforced with a Hook, but a failed backup is recoverable via git history since this project is under version control, so leaving it as a prompt instruction is acceptable. A is over-engineering ("convert everything to a Hook"), which runs against the "simplest solution" principle the exam favors. D fixates on the failure-rate number and overlooks the context of recoverability. B is wrong because it leaves a catastrophic risk (the directory restriction) to the prompt.

### Additional Question 4

**Scenario**: CI/CD Pipeline Integration

> A DevOps team has integrated Claude Code into their CI/CD pipeline, handling PR review, test generation, security scanning, and deployment checks across a multi-language codebase that includes Terraform.


**Question**: A Claude Code agent generates an API endpoint implementation. Detailed error-handling conventions have been specified, but the generated code is inconsistent with asynchronous error handling (sometimes try/catch, sometimes `.catch()`, sometimes no error handling at all). Adding more detailed instructions did not resolve it. What is the most effective next step?

- **A.** Add a linting rule that flags and rejects generated code whose error handling deviates from the target style, catching non-conforming output after generation
- **B.** Add a post-generation review step that rewrites incorrect error-handling patterns
- **C.** Add 2–4 few-shot examples, each with its reasoning, showing correct error-handling patterns across a variety of asynchronous scenarios **(correct)**
- **D.** Set temperature to 0 to eliminate the randomness that is disrupting consistency of error-handling style

**Explanation**: The fact that "adding more detailed instructions (explanation in the prompt) didn't resolve it" means the ambiguity has reached a level that words alone cannot dispel. Showing concrete input/output examples (few-shot), with reasons, is the most effective way to establish consistency. A and B are after-the-fact remedies (detect/fix after generation) that don't solve the root cause; D is off-target since the cause is "ambiguity in understanding the convention," not randomness.

### Additional Question 5

**Scenario**: CI/CD Pipeline Integration

> A DevOps team has integrated Claude Code into their CI/CD pipeline, handling PR review, test generation, security scanning, and deployment checks across a multi-language codebase that includes Terraform.


**Question**: There is a pipeline that extracts data from 2,000 contracts. For each contract, the model must call an internal `lookup_counterparty` tool and read its result to complete the extraction. The team wants to take advantage of the Batch API's 50% discount. What should they do?

- **A.** Submit to the Batch API and individually poll each request until its pending tool call resolves
- **B.** Submit the entire workflow to the Batch API, since batch requests support the same tool-call loop as synchronous requests
- **C.** Split each contract into two batches, feeding the output of the first batch as the tool result for the second batch
- **D.** Run the tool-calling extraction synchronously, since batch requests cannot continue processing from a tool result **(correct)**

**Explanation**: The Batch API is meant for single-shot requests and does not support a multi-turn tool-call loop (continuing a conversation after receiving a tool result). Any workflow that requires tool calls must run via the synchronous API. A, B, and C are all based on the mistaken premise that "the Batch API can also continue a tool-call loop."

### Additional Question 6

**Scenario**: Developer Productivity Tools

> A platform engineering team is building internal developer tools on Claude. Automated code review, documentation generation, and codebase exploration are all built into the CI/CD pipeline.


**Question**: The team has a `/deploy-check` skill that verifies deployment readiness. Requirements: (1) read configuration files across the entire repository, (2) check service health via bash commands, (3) produce a long, multi-page report, and (4) never modify source code. This skill needs to be usable team-wide. Which SKILL.md configuration satisfies all four requirements?

- **A.** Place it under `.claude/skills/` with `allowed-tools: ["Read", "Grep", "Glob", "Bash"]`
- **B.** Place it under `.claude/skills/` with `allowed-tools: ["Read", "Grep", "Glob", "Bash"]`, and `context: fork` **(correct)**
- **C.** Place it under `~/.claude/skills/` with `allowed-tools: ["Read", "Grep", "Glob", "Bash", "Write"]`, and `context: fork`
- **D.** Place it under `.claude/skills/` with `allowed-tools: ["Read", "Grep", "Glob"]`, and `context: fork`

**Explanation**: Placing it under `.claude/skills/` (project-shared, usable team-wide) with permission for config reading (Read), searching (Grep/Glob), and health checks (Bash), while excluding Write, satisfies requirement 4 ("never modify source"). Adding `context: fork` runs the long report generation independently, without cluttering the main conversation. A lacks `fork`, so the long report clutters the main conversation. C uses `~/.claude/skills/` (personal use, not team-wide) and includes Write, violating requirement 4. D lacks Bash, so health checks cannot be performed.

### Additional Question 7

**Scenario**: Developer Productivity Tools

> A platform engineering team is building internal developer tools on Claude. Automated code review, documentation generation, and codebase exploration are all built into the CI/CD pipeline.


**Question**: The team's agent must call `generate_report` to produce a weekly report on the first turn of every conversation. With `tool_choice: 'auto'`, the agent sometimes responds with a text summary instead. They want to guarantee the `generate_report` call on turn 1 while allowing normal tool selection afterward. What is the correct configuration?

- **A.** Exclude every tool except `generate_report` from the tool list on the first turn, then add the remaining tools back on subsequent turns
- **B.** Add a system-prompt instruction that says "always call `generate_report` on turn 1," keeping `tool_choice` as `auto`
- **C.** Set `tool_choice` to `'any'` for every turn of the conversation, requiring the agent to always call one of the available tools
- **D.** Force `generate_report` via `tool_choice` on turn 1, then switch to `'auto'` for subsequent turns **(correct)**

**Explanation**: Forcing a specific tool via `tool_choice` only on the first step, then reverting to `auto` afterward, is the correct pattern. B leaves it to the prompt, which is only probabilistically enforced (contrary to the already-covered Code Beats Prompts principle). C forces a tool call on every turn, which can cause an infinite loop in situations that should properly end with text. A is an overly complex implementation for a requirement the `tool_choice` feature already handles sufficiently.

### Additional Question 8

**Scenario**: Developer Productivity Tools

> A platform engineering team is building internal developer tools on Claude. Automated code review, documentation generation, and codebase exploration are all built into the CI/CD pipeline.


**Question**: A developer is iteratively refining a data-transformation function using Claude Code and has identified three problems: (1) date parsing mishandles timezone offsets, (2) currency formatting uses the wrong locale, (3) field names in the output JSON schema are incorrect. Problems 1 and 2 are independent of each other, but problem 3 changes the output shape that both must conform to. How should the developer give feedback?

- **A.** Send each of the three problems as a separate, sequential message, waiting for confirmation after fixing each one
- **B.** Bundle all three problems into a single message and have Claude address them comprehensively
- **C.** Fix problem 3 (the schema field names) first, then address problems 1 and 2 separately, since they are independent **(correct)**
- **D.** Use the interview pattern, having Claude ask clarifying questions about all three problems before making any changes

**Explanation**: Problem 3 is a precondition that changes the output shape (the schema), and problems 1 and 2 must conform to that new shape, so it's correct to nail down problem 3 first, then tackle 1 and 2. Since 1 and 2 are independent of each other, there's no need to bundle them into a single message — they can be addressed separately. A is inefficient, forcing even independent problems into sequential confirm-and-wait steps; B ignores the dependency and risks mixing everything into one pass; D is unnecessary since all three are already clearly defined problems that don't need clarifying questions.

### Additional Question 9

**Scenario**: Developer Productivity Tools

> A platform engineering team is building internal developer tools on Claude. Automated code review, documentation generation, and codebase exploration are all built into the CI/CD pipeline.


**Question**: A developer asked Claude Code to fix a function inside a large file. Since the target text appears in multiple places in the file, the Edit tool failed with a "non-unique match" error. What is the correct recovery approach?

- **A.** Use Read to load the entire file, then use Write to output the complete file with the change applied
- **B.** Switch to Bash and perform the replacement with `sed`, using line numbers rather than text matching
- **C.** Widen the Edit tool's search string by adding surrounding context until it becomes unique within the file **(correct)**
- **D.** Split the file into several smaller files so that each occurrence appears in only one file, then use Edit on each

**Explanation**: The correct recovery order when Edit fails is: ① widen the search string's surrounding context until it's unique → ② if that still doesn't work, use `replace_all` → ③ Read + Write as a last resort. Jumping straight to Read + Write (A) uses the last resort first. B is not the standard remedy presented here, and D is impractical and risks breaking the file structure.

### Additional Question 10

**Scenario**: Enterprise Data Platform with Federated Queries

> An enterprise analytics team is building a data platform that queries Snowflake, PostgreSQL, and third-party APIs via MCP tools. Result sets tend to be large, and handling caching, summarization, and cases where responses from multiple sources don't fit in the context window is a core challenge.


**Question**: The data platform team is conducting a human review of the agent's federated-query reports. Reviewers sampled 50 reports and obtained an overall accuracy of 97%. Leadership approved the system for production. Three weeks later, users report that currency-conversion calculations in cross-border revenue reports are wrong 40% of the time. Where did the review process fail?

- **A.** A sample size of 50 was too small to detect the currency-conversion problem, which has a 40% error rate
- **B.** The reviewers lacked currency-conversion expertise and couldn't identify the errors
- **C.** The reviewers used an aggregate accuracy metric, which masked category-specific failures like the 40% currency-conversion error rate **(correct)**
- **D.** Model accuracy degraded over time due to model drift; the 97% figure was correct at the time it was measured

**Explanation**: An aggregate metric like "97% overall" can mask low accuracy within a specific category (currency conversion, in this case). The lesson is that accuracy should have been validated per document type, category, or field before deciding whether to automate and go to production. A sounds plausible as a sample-size issue at first glance, but the root cause is the aggregation method — simply increasing the sample size while keeping the same aggregation approach could still miss the same problem. B and D are unrelated assumptions.

### Additional Question 11

**Scenario**: CI/CD Pipeline Integration

> A DevOps team has integrated Claude Code into their CI/CD pipeline, handling PR review, test generation, security scanning, and deployment checks across a multi-language codebase that includes Terraform.


**Question**: A Claude Code review prompt for a multi-language codebase classifies severity using prose descriptions such as "critical means dangerous code" and "minor means slightly inefficient code." Developers complain that similar code patterns receive different severity ratings from run to run. What is the most effective improvement?

- **A.** Lower the model's temperature to 0 to guarantee deterministic severity assessments
- **B.** Add a confidence threshold, reporting only findings with 90%+ confidence to exclude uncertain severity assessments
- **C.** Replace the prose severity descriptions with concrete TypeScript code examples for each severity level **(correct)**
- **D.** Add a second model pass that re-evaluates each finding's severity to detect inconsistencies

**Explanation**: Vague, prose-based criteria like "dangerous" or "slightly inefficient" are a source of inconsistent interpretation. Showing concrete code examples for each severity level increases consistency of judgment (the same idea as sharpening prompt accuracy with explicit criteria). A: lowering temperature does not resolve the ambiguity of the definitions themselves. B just excludes uncertain findings while the root cause (ambiguous definitions) remains. D only adds cost, and re-evaluating against the same ambiguous criteria doesn't improve anything.

---

## Additional Practice Question Set — Round 2 (sourced from an overseas practice site, translated)

The following are additional practice questions continuing from the previous 11. The source is an unofficial overseas mock-exam site, but the content has been confirmed to be consistent with the sections of this guide.

### Additional Question 1 (Round 2)

**Scenario**: CI/CD Pipeline Integration

> A DevOps team has integrated Claude Code into their CI/CD pipeline, handling PR review, test generation, security scanning, and deployment checks across a multi-language codebase that includes Terraform.


**Question**: In the CI/CD pipeline, one step has Claude Code generate tests, and the next step has it review that same code. The review step catches almost none of the problems in the generated tests, while human reviewers consistently find issues. Both CI steps correctly use the `-p` flag. What is the most likely cause and remedy?

- **A.** Generation and review should be run as independent sessions that share no context. That way the review side can approach the code without carrying over the original (generation-time) reasoning bias **(correct)**
- **B.** CLAUDE.md doesn't document test standards or review criteria, so the review lacks project-specific context
- **C.** The review step runs on the same model tier as the generation step and lacks the capacity to critique its own output. The review step should be upgraded to a more capable model
- **D.** The review step needs `--output-format json` to programmatically parse structured findings

**Explanation**: Running the review in the same session and context as generation causes the review side to also carry the generation-time reasoning ("this code should be correct"), making it hard to doubt its own judgment (confirmation bias). This is the same idea as "The Limits of Self-Review and Independent Review" from the deep-dive material — running the review in an independent session (a separate instance that shares no context) is the correct remedy. B (lack of standards in CLAUDE.md) would be a helpful supplementary improvement but not the root cause. C (upgrading the model) and D (output format) sound plausible but miss the point.

### Additional Question 2 (Round 2)

**Scenario**: CI/CD Pipeline Integration

> A DevOps team has integrated Claude Code into their CI/CD pipeline, handling PR review, test generation, security scanning, and deployment checks across a multi-language codebase that includes Terraform.


**Question**: Across the codebase (50+ directories), test files are co-located with source files (e.g., `Button.test.tsx` next to `Button.tsx`). The team wants all tests, regardless of location, to follow the same conventions. What is the most maintainable approach?

- **A.** Create a skill under `.claude/skills/` with `paths` frontmatter, so it auto-launches every time a test file is edited
- **B.** Place a copy of a CLAUDE.md file in every one of the 50+ directories where test files and source co-exist
- **C.** Add all test conventions to the root CLAUDE.md file so they are automatically loaded at the start of every session
- **D.** Create a file under `.claude/rules/` with frontmatter `paths: ["**/*.test.tsx", "**/*.test.ts"]` and put the test conventions in it **(correct)**

**Explanation**: Conditional application via path-scoping rules (paths/glob patterns) is the role of `.claude/rules/`. Using a pattern like `**/*.test.tsx` to auto-apply per file type keeps the conventions consistent regardless of directory location. B is a classic wrong pattern (copying the same CLAUDE.md into 50 places is impractical and unmaintainable). C consolidates everything at the root, but that loads information unrelated to test conventions into every session, wasting context. A sounds plausible at first glance, but "automatic application based on file type" is the job of rules (path-matching), not skills (invocation-based) — this is the trap.

### Additional Question 3 (Round 2)

**Scenario**: Developer Productivity Tools

> A platform engineering team is building internal developer tools on Claude. Automated code review, documentation generation, and codebase exploration are all built into the CI/CD pipeline.


**Question**: A developer-tool agent running on a version-controlled project has two guardrails: ① a PreToolUse hook that blocks writing files outside the project directory (100% enforced), and ② a system-prompt instruction that says "always create a backup before overwriting an existing file" (followed 88% of the time). A senior engineer argues that, for consistency, all guardrails should be converted to Hooks. What is the correct assessment?

- **A.** The senior engineer's argument is correct. All guardrails, including the recoverable backup rule, should be converted to deterministic Hooks for maximum reliability
- **B.** Both guardrails should be prompt instructions. Unifying on a single enforcement mechanism is easier to maintain than mixing Hooks and prompt rules
- **C.** Converting the directory restriction into a Hook is correct. The backup instruction, on the other hand, can stay as a prompt instruction, since a missed backup is recoverable (thanks to version control) **(correct)**
- **D.** The backup instruction should be converted to a Hook. A 12% failure rate on a documented guardrail is too high to leave to the prompt

**Explanation**: The key judgment axis is whether a failure's consequence is catastrophic or recoverable. Writing outside the directory would be catastrophic if it failed, so it should be reliably enforced with a Hook, but a failed backup is recoverable via git history since this project is under version control, so leaving it as a prompt instruction is acceptable. A is over-engineering ("convert everything to a Hook"), which runs against the "simplest solution" principle the exam favors. D fixates on the failure-rate number and overlooks the context of recoverability. B is wrong because it leaves a catastrophic risk (the directory restriction) to the prompt.

### Additional Question 4 (Round 2)

**Scenario**: CI/CD Pipeline Integration

> A DevOps team has integrated Claude Code into their CI/CD pipeline, handling PR review, test generation, security scanning, and deployment checks across a multi-language codebase that includes Terraform.


**Question**: A Claude Code agent generates an API endpoint implementation. Detailed error-handling conventions have been specified, but the generated code is inconsistent with asynchronous error handling (sometimes try/catch, sometimes `.catch()`, sometimes no error handling at all). Adding more detailed instructions did not resolve it. What is the most effective next step?

- **A.** Add a linting rule that flags and rejects generated code whose error handling deviates from the target style, catching non-conforming output after generation
- **B.** Add a post-generation review step that rewrites incorrect error-handling patterns
- **C.** Add 2–4 few-shot examples, each with its reasoning, showing correct error-handling patterns across a variety of asynchronous scenarios **(correct)**
- **D.** Set temperature to 0 to eliminate the randomness that is disrupting consistency of error-handling style

**Explanation**: The fact that "adding more detailed instructions (explanation in the prompt) didn't resolve it" means the ambiguity has reached a level that words alone cannot dispel. Showing concrete input/output examples (few-shot), with reasons, is the most effective way to establish consistency. A and B are after-the-fact remedies (detect/fix after generation) that don't solve the root cause; D is off-target since the cause is "ambiguity in understanding the convention," not randomness.

### Additional Question 5 (Round 2)

**Scenario**: CI/CD Pipeline Integration

> A DevOps team has integrated Claude Code into their CI/CD pipeline, handling PR review, test generation, security scanning, and deployment checks across a multi-language codebase that includes Terraform.


**Question**: There is a pipeline that extracts data from 2,000 contracts. For each contract, the model must call an internal `lookup_counterparty` tool and read its result to complete the extraction. The team wants to take advantage of the Batch API's 50% discount. What should they do?

- **A.** Submit to the Batch API and individually poll each request until its pending tool call resolves
- **B.** Submit the entire workflow to the Batch API, since batch requests support the same tool-call loop as synchronous requests
- **C.** Split each contract into two batches, feeding the output of the first batch as the tool result for the second batch
- **D.** Run the tool-calling extraction synchronously, since batch requests cannot continue processing from a tool result **(correct)**

**Explanation**: The Batch API is meant for single-shot requests and does not support a multi-turn tool-call loop (continuing a conversation after receiving a tool result). Any workflow that requires tool calls must run via the synchronous API. A, B, and C are all based on the mistaken premise that "the Batch API can also continue a tool-call loop."

### Additional Question 6 (Round 2)

**Scenario**: Developer Productivity Tools

> A platform engineering team is building internal developer tools on Claude. Automated code review, documentation generation, and codebase exploration are all built into the CI/CD pipeline.


**Question**: The team has a `/deploy-check` skill that verifies deployment readiness. Requirements: (1) read configuration files across the entire repository, (2) check service health via bash commands, (3) produce a long, multi-page report, and (4) never modify source code. This skill needs to be usable team-wide. Which SKILL.md configuration satisfies all four requirements?

- **A.** Place it under `.claude/skills/` with `allowed-tools: ["Read", "Grep", "Glob", "Bash"]`
- **B.** Place it under `.claude/skills/` with `allowed-tools: ["Read", "Grep", "Glob", "Bash"]`, and `context: fork` **(correct)**
- **C.** Place it under `~/.claude/skills/` with `allowed-tools: ["Read", "Grep", "Glob", "Bash", "Write"]`, and `context: fork`
- **D.** Place it under `.claude/skills/` with `allowed-tools: ["Read", "Grep", "Glob"]`, and `context: fork`

**Explanation**: Placing it under `.claude/skills/` (project-shared, usable team-wide) with permission for config reading (Read), searching (Grep/Glob), and health checks (Bash), while excluding Write, satisfies requirement 4 ("never modify source"). Adding `context: fork` runs the long report generation independently, without cluttering the main conversation. A lacks `fork`, so the long report clutters the main conversation. C uses `~/.claude/skills/` (personal use, not team-wide) and includes Write, violating requirement 4. D lacks Bash, so health checks cannot be performed.

### Additional Question 7 (Round 2)

**Scenario**: Developer Productivity Tools

> A platform engineering team is building internal developer tools on Claude. Automated code review, documentation generation, and codebase exploration are all built into the CI/CD pipeline.


**Question**: The team's agent must call `generate_report` to produce a weekly report on the first turn of every conversation. With `tool_choice: 'auto'`, the agent sometimes responds with a text summary instead. They want to guarantee the `generate_report` call on turn 1 while allowing normal tool selection afterward. What is the correct configuration?

- **A.** Exclude every tool except `generate_report` from the tool list on the first turn, then add the remaining tools back on subsequent turns
- **B.** Add a system-prompt instruction that says "always call `generate_report` on turn 1," keeping `tool_choice` as `auto`
- **C.** Set `tool_choice` to `'any'` for every turn of the conversation, requiring the agent to always call one of the available tools
- **D.** Force `generate_report` via `tool_choice` on turn 1, then switch to `'auto'` for subsequent turns **(correct)**

**Explanation**: Forcing a specific tool via `tool_choice` only on the first step, then reverting to `auto` afterward, is the correct pattern. B leaves it to the prompt, which is only probabilistically enforced (contrary to the already-covered Code Beats Prompts principle). C forces a tool call on every turn, which can cause an infinite loop in situations that should properly end with text. A is an overly complex implementation for a requirement the `tool_choice` feature already handles sufficiently.

### Additional Question 8 (Round 2)

**Scenario**: Developer Productivity Tools

> A platform engineering team is building internal developer tools on Claude. Automated code review, documentation generation, and codebase exploration are all built into the CI/CD pipeline.


**Question**: A developer is iteratively refining a data-transformation function using Claude Code and has identified three problems: (1) date parsing mishandles timezone offsets, (2) currency formatting uses the wrong locale, (3) field names in the output JSON schema are incorrect. Problems 1 and 2 are independent of each other, but problem 3 changes the output shape that both must conform to. How should the developer give feedback?

- **A.** Send each of the three problems as a separate, sequential message, waiting for confirmation after fixing each one
- **B.** Bundle all three problems into a single message and have Claude address them comprehensively
- **C.** Fix problem 3 (the schema field names) first, then address problems 1 and 2 separately, since they are independent **(correct)**
- **D.** Use the interview pattern, having Claude ask clarifying questions about all three problems before making any changes

**Explanation**: Problem 3 is a precondition that changes the output shape (the schema), and problems 1 and 2 must conform to that new shape, so it's correct to nail down problem 3 first, then tackle 1 and 2. Since 1 and 2 are independent of each other, there's no need to bundle them into a single message — they can be addressed separately. A is inefficient, forcing even independent problems into sequential confirm-and-wait steps; B ignores the dependency and risks mixing everything into one pass; D is unnecessary since all three are already clearly defined problems that don't need clarifying questions.

### Additional Question 9 (Round 2)

**Scenario**: Developer Productivity Tools

> A platform engineering team is building internal developer tools on Claude. Automated code review, documentation generation, and codebase exploration are all built into the CI/CD pipeline.


**Question**: A developer asked Claude Code to fix a function inside a large file. Since the target text appears in multiple places in the file, the Edit tool failed with a "non-unique match" error. What is the correct recovery approach?

- **A.** Use Read to load the entire file, then use Write to output the complete file with the change applied
- **B.** Switch to Bash and perform the replacement with `sed`, using line numbers rather than text matching
- **C.** Widen the Edit tool's search string by adding surrounding context until it becomes unique within the file **(correct)**
- **D.** Split the file into several smaller files so that each occurrence appears in only one file, then use Edit on each

**Explanation**: The correct recovery order when Edit fails is: ① widen the search string's surrounding context until it's unique → ② if that still doesn't work, use `replace_all` → ③ Read + Write as a last resort. Jumping straight to Read + Write (A) uses the last resort first. B is not the standard remedy presented here, and D is impractical and risks breaking the file structure.

### Additional Question 10 (Round 2)

**Scenario**: Enterprise Data Platform with Federated Queries

> An enterprise analytics team is building a data platform that queries Snowflake, PostgreSQL, and third-party APIs via MCP tools. Result sets tend to be large, and handling caching, summarization, and cases where responses from multiple sources don't fit in the context window is a core challenge.


**Question**: The data platform team is conducting a human review of the agent's federated-query reports. Reviewers sampled 50 reports and obtained an overall accuracy of 97%. Leadership approved the system for production. Three weeks later, users report that currency-conversion calculations in cross-border revenue reports are wrong 40% of the time. Where did the review process fail?

- **A.** A sample size of 50 was too small to detect the currency-conversion problem, which has a 40% error rate
- **B.** The reviewers lacked currency-conversion expertise and couldn't identify the errors
- **C.** The reviewers used an aggregate accuracy metric, which masked category-specific failures like the 40% currency-conversion error rate **(correct)**
- **D.** Model accuracy degraded over time due to model drift; the 97% figure was correct at the time it was measured

**Explanation**: An aggregate metric like "97% overall" can mask low accuracy within a specific category (currency conversion, in this case). The lesson is that accuracy should have been validated per document type, category, or field before deciding whether to automate and go to production. A sounds plausible as a sample-size issue at first glance, but the root cause is the aggregation method — simply increasing the sample size while keeping the same aggregation approach could still miss the same problem. B and D are unrelated assumptions.

### Additional Question 11 (Round 2)

**Scenario**: CI/CD Pipeline Integration

> A DevOps team has integrated Claude Code into their CI/CD pipeline, handling PR review, test generation, security scanning, and deployment checks across a multi-language codebase that includes Terraform.


**Question**: A Claude Code review prompt for a multi-language codebase classifies severity using prose descriptions such as "critical means dangerous code" and "minor means slightly inefficient code." Developers complain that similar code patterns receive different severity ratings from run to run. What is the most effective improvement?

- **A.** Lower the model's temperature to 0 to guarantee deterministic severity assessments
- **B.** Add a confidence threshold, reporting only findings with 90%+ confidence to exclude uncertain severity assessments
- **C.** Replace the prose severity descriptions with concrete TypeScript code examples for each severity level **(correct)**
- **D.** Add a second model pass that re-evaluates each finding's severity to detect inconsistencies

**Explanation**: Vague, prose-based criteria like "dangerous" or "slightly inefficient" are a source of inconsistent interpretation. Showing concrete code examples for each severity level increases consistency of judgment (the same idea as sharpening prompt accuracy with explicit criteria). A: lowering temperature does not resolve the ambiguity of the definitions themselves. B just excludes uncertain findings while the root cause (ambiguous definitions) remains. D only adds cost, and re-evaluating against the same ambiguous criteria doesn't improve anything.

### Additional Question 12 (Round 2)

**Scenario**: Multi-Agent Research System

> A consulting firm has deployed a multi-agent research system. Web-search, document-analysis, and synthesis agents collaborate under a coordinator to complete market-research reports from a large volume of sources.


**Question**: A developer resumed a Claude Code session after changing 3 files out of a 50-file codebase. The agent is reasoning from stale tool results and giving contradictory advice about the changed files. What is the correct remedy?

- **A.** Start a completely new session and have all 50 files re-analyzed from scratch, so stale information doesn't remain in the new context
- **B.** Use `fork_session` to branch the session, reflecting the 3 changed files into the new branch while the original session retains its previous state
- **C.** Start a new session injected with a summary of prior findings, explicitly naming the 3 changed files so a precise re-analysis can occur **(correct)**
- **D.** Resume the session and have the agent re-read only the 3 changed files, overwriting the existing stale results within the context window

**Explanation**: "Contradictory advice" is a sign that information has gone stale (Stale? Start Fresh Instead). In this case a new session, not a resume, is correct, but re-analyzing all files from scratch (A) is inefficient. Injecting a summary while explicitly naming the changed files (Tell the Agent What Changed) enables an efficient and accurate re-analysis. B's `fork_session` is a feature for comparing multiple strategies, and it would carry over the same stale context from the branch point, making it unsuitable here. D rests on the mistaken premise that "information within the context window can be overwritten" — conversation history is append-only, so old, contradictory information doesn't disappear and keeps accumulating.

### Additional Question 13 (Round 2)

**Scenario**: Multi-Agent Research System

> A consulting firm has deployed a multi-agent research system. Web-search, document-analysis, and synthesis agents collaborate under a coordinator to complete market-research reports from a large volume of sources.


**Question**: A multi-agent system is running a 45-minute deep-research workflow. At the 30-minute mark, the coordinator's synthesis quality degrades: instead of the specific statistics it was citing earlier, it starts using vague phrases like "key findings," and it confuses the financial subagent's claims with the news subagent's. Neither a token limit nor an error has been hit. What is the most likely diagnosis and correct remedy?

- **A.** A token limit has been silently hit, and the API is truncating the earliest messages. Upgrade to a model with a larger context window
- **B.** A subagent is returning contradictory data, confusing the coordinator. Add data validation to each subagent's output before it reaches the coordinator
- **C.** Context degradation: the earlier results are buried deep within a long context. Consolidate the key findings into a structured block placed near the end **(correct)**
- **D.** The model is suffering "fatigue" from a long-running session and needs a cooldown period before continuing

**Explanation**: The symptoms (specific statistics degrading into vague phrasing, confusing which subagent said what) are a textbook case of Lost in the Middle (information buried in the middle of a long context gets overlooked) and the Progressive Summarization Trap. A is ruled out since the question explicitly states no token limit or error has been hit. B assumes the underlying data itself is wrong, but the symptoms are about mix-ups and vagueness — a positional bias issue on the coordinator's side. D is an unscientific, anthropomorphized explanation; no such mechanism as "model fatigue" actually exists in LLMs, making it a classic trap option. The correct remedy is to restructure the key findings and relocate them near the beginning or end.

### Additional Question 14 (Round 2)

**Scenario**: Multi-Agent Research System

> A consulting firm has deployed a multi-agent research system. Web-search, document-analysis, and synthesis agents collaborate under a coordinator to complete market-research reports from a large volume of sources.


**Question**: The web-search subagent of a multi-agent research system timed out while investigating a complex topic. You need to design how this failure information reaches the coordinator. Which approach best enables intelligent recovery?

- **A.** Propagate the timeout exception to a top-level handler, immediately terminating the entire research workflow and discarding all progress so far
- **B.** Implement automatic retries with exponential backoff, and only after exhausting all retries return a generic "search unavailable" status
- **C.** Catch the timeout and return an empty result set marked as successful, so the rest of the workflow continues regardless of the failure
- **D.** Return structured error context including the failure type, the queries attempted, partial results, and possible alternative approaches **(correct)**

**Explanation**: (Another version of the same point as Official Sample Question 8) Structured error context gives the coordinator the information it needs to make an intelligent recovery decision. A is over-reactive, discarding even valuable partial progress. B ultimately returns only a generic status, hiding the information needed for a judgment call. C is the worst anti-pattern, disguising a failure as a success (conflating an empty result with an error).

### Additional Question 15 (Round 2)

**Scenario**: Developer Productivity Tools

> A platform engineering team is building internal developer tools on Claude. Automated code review, documentation generation, and codebase exploration are all built into the CI/CD pipeline.


**Question**: There is a developer-tool agent that writes code and runs shell commands. A system-prompt instruction — "never run destructive commands, never access files outside the project" — is being violated 3% of the time in testing. The team requires this rule to be followed absolutely. What should the architect implement?

- **A.** A PreToolUse hook that scans shell commands for destructive patterns and validates file paths before execution **(correct)**
- **B.** A PostToolUse hook that detects destructive commands after execution and rolls them back
- **C.** Strengthen the system prompt with more specific examples of forbidden commands and few-shot demonstrations
- **D.** Remove the shell-execution tool entirely, eliminating any possibility of a destructive command

**Explanation**: The key point is the requirement that the rule be followed "absolutely (100%)." Destructive commands (e.g., file deletion) may be irreversible once executed, so they must be stopped before execution (Catch the Call Mid-Air). B assumes post-execution rollback is possible, but deletion or external side effects aren't necessarily reversible, so it can't meet the "absolute" requirement. C is merely a prompt strengthening, and no matter how far you take it, it remains probabilistic (per the Code Beats Prompts principle, there's no guarantee the 3% becomes 0%). D is an overreaction that destroys the agent's core role (executing code).

### Additional Question 16 (Round 2)

**Scenario**: Multi-Agent Research System

> A consulting firm has deployed a multi-agent research system. Web-search, document-analysis, and synthesis agents collaborate under a coordinator to complete market-research reports from a large volume of sources.


**Question**: A synthesis agent is producing a report where several claims lack source citations. The web-search and document-analysis subagents are functioning correctly and returning properly cited results. What is the most likely root cause?

- **A.** The synthesis agent lacks direct web-search access, so it cannot re-fetch sources to back up the claims it's asked to write
- **B.** The synthesis agent's system prompt doesn't instruct it to include citations, so it drops sources even when given cited input
- **C.** The web-search subagent is returning plain prose summaries rather than a structured citation format the synthesis agent could convert into source attributions
- **D.** The coordinator is passing content to the synthesis agent stripped of structured metadata such as source URLs and document names **(correct)**

**Explanation**: Since it's explicitly stated that the subagents function correctly and return properly cited results, the sources themselves are correctly structured at the subagent stage. If the final report still lacks citations, the most coherent explanation is that the structured data was lost somewhere along the way — while passing through the coordinator. This is a case of the "preserving the claim-source mapping" principle (carrying claims, evidence, and sources through to the synthesis agent in structured form) being violated. A describes a normal division of labor for the synthesis agent and is not itself a problem. C contradicts the premise of the question (that the subagents return properly cited results).

### Additional Question 17 (Round 2)

**Scenario**: Developer Productivity Tools

> A platform engineering team is building internal developer tools on Claude. Automated code review, documentation generation, and codebase exploration are all built into the CI/CD pipeline.


**Question**: A developer is investigating a production bug. They know the error message "InvalidStateTransition" is logged somewhere in the codebase, but they don't know which file it's in or where the state machine is defined. What is the correct order of tools to pinpoint the bug?

- **A.** Search for "InvalidStateTransition" with Edit and replace it with a more descriptive error message
- **B.** Search with Glob for `'**/*state*'` to find state-machine-related files, then Read each one to look for the error message
- **C.** Grep the entire codebase for "InvalidStateTransition," then Read the files that match **(correct)**
- **D.** Use Read on common file locations like `src/index.ts` or `src/app.ts` and manually search for the error

**Explanation**: When a string (the error message) is already known, this is a problem of searching inside file contents, which is Grep's job. Glob is a file-name pattern search, and relying on the guess that "the word 'state' should be in the file name" risks missing files that don't follow that naming convention. A misuses an editing tool instead of a search tool, and rewriting before identifying the cause is dangerous. D is an inefficient method of guessing at common file paths. The correct exploration order is "Grep for a hit → Read the matching file."

### Additional Question 18 (Round 2)

**Scenario**: CI/CD Pipeline Integration

> A DevOps team has integrated Claude Code into their CI/CD pipeline, handling PR review, test generation, security scanning, and deployment checks across a multi-language codebase that includes Terraform.


**Question**: A CI/CD system prompt defines two review categories with the instructions "check for security vulnerabilities within each function" and "check for performance problems within each loop." The model frequently calls `performance_check` for security issues found inside loops, and `security_check` for performance problems found inside security-related functions. What is the root cause and best fix?

- **A.** Force `tool_choice` to `'auto'` and let the model judge the correct tool itself based on each finding's content
- **B.** Add more detailed tool descriptions that precisely explain when each tool should be called
- **C.** The keyword overlap between the instruction wording and the tool names is causing the confusion. Redefine the categories around the nature of the problem itself, not location (function/loop), and rewrite them using non-overlapping terminology **(correct)**
- **D.** The model is confused because a loop can have both security and performance problems. Add a rule that security always takes priority over performance

**Explanation**: The symptom — "a security issue inside a loop → `performance_check`" and "a performance issue inside a security-related function → `security_check`" — shows that the model is selecting tools based on positional keywords (function/loop) rather than the nature of the problem. Because the instructions define the categories using the structural phrasing "within each function"/"within each loop," the words "function"/"loop" themselves are triggering a mistaken association with the tool names. B is correct in general but doesn't address the specific root cause this question is asking about. D reframes it as a prioritization issue for when both problems co-occur, sidestepping the actual root cause — the category mix-up itself.

### Additional Question 19 (Round 2)

**Scenario**: Multi-Agent Research System

> A consulting firm has deployed a multi-agent research system. Web-search, document-analysis, and synthesis agents collaborate under a coordinator to complete market-research reports from a large volume of sources.


**Question**: A multi-agent research system is producing a synthesis report on market trends. Two reliable sources report different growth rates for the same sector: Source A reports 12% growth (2023 data), Source B reports 8% growth (2024 data). The synthesis agent currently picks the more recent value. What is the correct approach?

- **A.** Average the two values and report 10% growth, with a note recording the discrepancy between sources
- **B.** Flag this as a conflict and escalate it to a human researcher for resolution before including it in the report
- **C.** Annotate both values with their source and publication date so the reader can interpret them **(correct)**
- **D.** Always use whichever of the two sources is more recent, on the grounds that it reflects the latest data

**Explanation**: 12% in 2023 versus 8% in 2024 is more likely a natural time-series trend (growth slowing over time) than a genuine conflict between sources. The correct approach is to present both values with their source and publication date so the reader can interpret them in context (the principle of making time-series data explicit). A fabricates a nonexistent average value. B treats something that likely isn't even a real conflict as a dispute requiring human escalation. D is described in the question itself as the "current (needing improvement)" behavior, and simply picking the newer value discards information that hints at a trend.

### Additional Question 20 (Round 2)

**Scenario**: Code Generation with Claude Code

> A software engineering team uses Claude Code to write, review, and test code in a large TypeScript monorepo (200+ packages, tests co-located with source, multiple deployment targets).


**Question**: A large TypeScript monorepo has a root CLAUDE.md with company-wide coding conventions, plus a `.claude/rules/` directory with topic-specific rule files. A developer notices that `testing.md`'s conventions from `.claude/rules/` are being loaded even while editing an API handler file, wasting tokens unnecessarily. `testing.md` has no YAML frontmatter. What should the developer do to fix this?

- **A.** Rule files cannot be scoped by path, so add the test conventions to the root CLAUDE.md instead
- **B.** In the root CLAUDE.md, use a line like `@./.claude/rules/testing.md` so the file loads only when the import point is reached
- **C.** Add YAML frontmatter to `testing.md` with a `paths` field containing a glob pattern for test files, so it loads only for test files **(correct)**
- **D.** Move `testing.md` out of `.claude/rules/` into a directory-level CLAUDE.md inside a tests folder

**Explanation**: The direct cause of the problem is that `testing.md` has no YAML frontmatter. Without `paths` frontmatter, conditional loading doesn't function, so it's always loaded unconditionally. Only by specifying a glob pattern for test files in the `paths` field does the "only while editing test files" conditional loading actually take effect. A makes the symptom worse by moving it to the root, where it would be loaded unconditionally even more broadly. B's `@import` syntax unconditionally pulls in its contents whenever the importing file is loaded — it does not provide path-based conditional branching. D is a classic wrong pattern of scattering CLAUDE.md files by directory, and individually handling every test file scattered across 200+ packages is impractical.

### Additional Question 21 (Round 2)

**Scenario**: CI/CD Pipeline Integration

> A DevOps team has integrated Claude Code into their CI/CD pipeline, handling PR review, test generation, security scanning, and deployment checks across a multi-language codebase that includes Terraform.


**Question**: A CI/CD pipeline processes code reviews using the Message Batches API. After a batch completes, each review result needs to be matched back to its corresponding pull request. What is the correct mechanism for correlating batch results with the original requests?

- **A.** Rely on the Batch API returning results in the same order in which they were submitted
- **B.** Store a pull-request identifier in the `custom_id` field of each batch request, and match results using `custom_id` **(correct)**
- **C.** Submit each pull request as its own separate, single-item batch to preserve correlation
- **D.** Parse the review content and identify which pull request it belongs to based on the file names mentioned in the review

**Explanation**: With the Batch API, attaching a `custom_id` to each request and matching results back to their original request by that `custom_id` when results come in is the only correct method. A is wrong: the Batch API processes requests asynchronously and out of order, giving no ordering guarantee. C defeats the purpose of batching things together (the 50% discount, efficiency) by making every request its own single-item batch — an impractical approach. D relies on unreliable inference from the review content, carrying a high risk of mismatching.

### Additional Question 22 (Round 2)

**Scenario**: Multi-Agent Research System

> A consulting firm has deployed a multi-agent research system. Web-search, document-analysis, and synthesis agents collaborate under a coordinator to complete market-research reports from a large volume of sources.


**Question**: A multi-agent research system produced a report on "renewable energy technologies," but it covers only solar and wind, missing geothermal, tidal, biomass, and fusion. Each subagent thoroughly covered the topic it was assigned. Where does the root cause lie?

- **A.** The synthesis subagent failed to audit the input for coverage gaps, writing about solar and wind as if they represented the entire field
- **B.** The web-search subagent's query scope was too narrow, so geothermal, tidal, biomass, and fusion never appeared in the search results at all
- **C.** The coordinator's task decomposition assigned only solar and wind, overlooking the other categories of renewable energy **(correct)**
- **D.** The document-analysis subagent had no access to sources on geothermal, tidal, biomass, and fusion, so those categories never entered the report at all

**Explanation**: (Same point as Official Sample Question 7) It's explicitly stated that "each subagent thoroughly covered the topic it was assigned," meaning the subagents themselves functioned correctly. The problem is that the fields of geothermal, tidal, biomass, and fusion were never assigned to anyone in the first place — the root cause is that the coordinator's task decomposition was too narrow. A, B, and D each wrongly blame downstream agents that were functioning correctly within the scope they were assigned. In multi-agent systems, the quality of the coordinator's task decomposition determines the bulk of overall quality.

### Additional Question 23 (Round 2)

**Scenario**: Developer Productivity Tools

> A platform engineering team is building internal developer tools on Claude. Automated code review, documentation generation, and codebase exploration are all built into the CI/CD pipeline.


**Question**: A synthesis agent frequently hands control back to the coordinator just for simple fact-checks, causing 2-3 round trips per task and a 40% latency increase. Analysis shows 85% of these verifications are simple lookups. What is the most effective solution?

- **A.** Give the synthesis agent a scoped `verify_fact` tool for simple lookups, and escalate only complex verifications to the coordinator **(correct)**
- **B.** Increase the coordinator's parallelism so it can process the queued verification requests faster, absorbing the extra round trips
- **C.** Add a verification-result cache at the coordinator level so repeated lookups can be returned instantly, eliminating most of the round-trip latency
- **D.** Remove fact-checking from the synthesis workflow entirely, eliminating both the round trips and their associated latency

**Explanation**: (Same point as Official Sample Question 9) Apply the principle of least privilege: grant just enough authority to handle the 85% of simple cases directly, while keeping the existing coordination (via the coordinator) for the complex verifications (the remaining 15%). B doesn't change the round-trip structure itself even with higher parallelism, so it's not a real fix. C's caching only helps repeated lookups — it does nothing for first-time, unique lookups. D is an overreaction that removes the verification capability entirely, sacrificing accuracy along with it.

### Additional Question 24 (Round 2)

**Scenario**: CI/CD Pipeline Integration

> A DevOps team has integrated Claude Code into their CI/CD pipeline, handling PR review, test generation, security scanning, and deployment checks across a multi-language codebase that includes Terraform.


**Question**: A team runs `anthropics/claude-code-action@v1` so that Claude reviews every pull request against a checklist in CLAUDE.md. They want Claude to retain the default coding-assistant behavior while also applying 3 additional review rules not present in CLAUDE.md. Where should the additional rules be placed?

- **A.** Specify the 3 rules as `--append-system-prompt` inside `claude_args` **(correct)**
- **B.** Specify the 3 rules as `--system-prompt` inside `claude_args`
- **C.** Specify them in the action's `custom_instructions` input
- **D.** Specify them in the `prompt` input, which switches the action into automation mode and applies the rules to every pull-request event

**Explanation**: `--append-system-prompt` is a mechanism that appends to the default system prompt rather than replacing it, which exactly matches the requirement of "retaining the default behavior while applying additional rules." B's `--system-prompt` replaces the entire default system prompt outright, losing the default coding-assistant behavior. C's `custom_instructions` is likely a trap option that doesn't exist as a legitimate mechanism of this action. D's `prompt` input switches the action itself into a different mode, carrying side effects well beyond simply adding rules.

### Additional Question 25 (Round 2)

**Scenario**: Multi-Agent Research System

> A consulting firm has deployed a multi-agent research system. Web-search, document-analysis, and synthesis agents collaborate under a coordinator to complete market-research reports from a large volume of sources.


**Question**: A multi-agent research system must handle a client request involving three sequential stages: data collection, analysis, and report generation. Each stage depends on the output of the previous one. Which orchestration pattern is most appropriate?

- **A.** Pipeline orchestration — passing each stage's output as input to the next stage in a defined order **(correct)**
- **B.** A hub-and-spoke setup in which all three agents report independently to the coordinator
- **C.** Dynamic, adaptive decomposition — the coordinator decides the order at runtime based on the complexity of the query
- **D.** Parallel orchestration — running all three subagents simultaneously to minimize latency

**Explanation**: This is a clearly sequential process where each stage explicitly depends on the previous stage's output, which maps directly onto Pipeline (serial chaining) among the four topology patterns. B assumes each agent reports independently, which cannot properly handle the dependency relationship. C is suited to tasks whose steps are unknown or exploratory (Adapt as You Discover); here the steps are known, so Chain is appropriate. D is only valid when each stage is independent — since there is a dependency here, simultaneous execution is not possible.

---

## Additional Practice Question Set — Round 3 (sourced from GitHub: OlivierAlter/Claude-Certified-Architect-Foundations-Certification-Exam, translated)

The following are 8 new questions selected from an unofficial GitHub practice-question repository (containing 77 questions), chosen so as not to overlap with Rounds 1 and 2, and translated.

### Additional Question 1 (Round 3)

**Scenario**: Multi-Agent Research System (GitHub: OlivierAlter Q15)

> A multi-agent research system's agentic loop appends each tool result to the conversation history before sending the next request.


**Question**: A teammate proposes changing this so all tool results are stored in a separate database, and each iteration passes the model only a summary rather than the full result. Under which condition is this change most likely to degrade agent performance?

- **A.** When tool results include binary data such as images or file attachments
- **B.** When the model needs to reason about multiple tool results simultaneously to decide its next action. A summary may omit details necessary for that reasoning **(correct)**
- **C.** When the number of tool calls per session exceeds 10, since a large history slows down the API
- **D.** When tool responses return in under 200ms, since appending to history becomes redundant

**Explanation**: The agentic loop relies on tool results remaining in the conversation history so the model can reason about "what is already known" before deciding its next action. A summary may omit field values, error codes, and details needed for branching decisions, potentially leading the model to a wrong judgment. A is a valid concern but not the primary risk for a text-based research task. C and D are not recognized failure modes of this architecture.

### Additional Question 2 (Round 3)

**Scenario**: Multi-Agent Research System (GitHub: OlivierAlter Q17)

> A research system's coordinator delegated document analysis to a subagent.


**Question**: After the subagent finishes, the coordinator notices only 3 of the 5 specified sources were covered. The remaining 2 sources need to be analyzed. What is the correct approach to re-delegation?

- **A.** Give the synthesis agent the partial findings and have it infer the content of the missing sources from patterns in the 3 completed ones
- **B.** The coordinator should explicitly specify only the 2 missing sources, include the already-completed findings as context, and call the document-analysis subagent again **(correct)**
- **C.** Resend all 5 sources to a new instance of the document-analysis subagent, having it re-analyze all of them including the 3 already completed
- **D.** Skip re-delegating to the subagent, and have the coordinator itself directly analyze the remaining 2 sources

**Explanation**: The coordinator's role includes assessing output gaps and re-delegating precisely to fill them. Re-specifying only the incomplete sources, while providing the already-completed findings as context, is efficient and accurate. A has the synthesis agent fabricate content, undermining the report's accuracy. C wastefully reprocesses sources that are already done. D bypasses the coordinator/subagent architecture and ignores the subagent's specialization.

### Additional Question 3 (Round 3)

**Scenario**: Developer Productivity Tools (GitHub: OlivierAlter Q31)

> A customer-support agent has three tools with similar names and descriptions (`get_account_info`, `fetch_account_details`, `retrieve_customer_record`), each calling a different backend.


**Question**: The agent frequently picks the wrong tool. What is the most effective approach to resolving this misrouting?

- **A.** List all three tools and add a system-prompt instruction specifying which one to use for which type of request
- **B.** Rename the tools to reflect each one's distinct backend, and rewrite their descriptions to explain what data each returns, which backend it queries, and when to use it **(correct)**
- **C.** Consolidate the three tools into a single tool with a `source` parameter that specifies which backend to query
- **D.** Keep the current tools but randomize which one the agent calls, merging the responses in a post-processing step

**Explanation**: Ambiguous, similar tool names and descriptions are the cause of the misrouting. Renaming each tool to reflect its distinct backend and rewriting its description to explain what data it returns, what it queries, and when to use it is the correct fix. A might reduce misrouting somewhat but doesn't fix the root problem (the tool descriptions themselves being indistinguishable). C collapses three specialized tools down into a single general-purpose tool plus a parameter, simply pushing the burden of correct routing back onto the model. D is not a practical design.

### Additional Question 4 (Round 3)

**Scenario**: Code Generation with Claude Code (GitHub: OlivierAlter Q41)

> A senior engineer added detailed coding standards and security guidelines to their own `~/.claude/CLAUDE.md`.


**Question**: When a new team member clones the repository, Claude Code behaves differently and doesn't seem to follow the team's documented standards. What is the most likely cause?

- **A.** The new member's version of Claude Code is out of date and doesn't support shared configuration files
- **B.** `~/.claude/CLAUDE.md` is user-scoped and not version-controlled, so it does not travel to teammates when they clone the repository **(correct)**
- **C.** CLAUDE.md files must be placed inside a `.claude/` subdirectory; a root-level CLAUDE.md is ignored
- **D.** In the configuration hierarchy, project-level files must explicitly import from user-level files via `@import`

**Explanation**: User-level configuration in `~/.claude/CLAUDE.md` applies only to that individual developer and is never committed to version control. Teammates never see it, regardless of its contents. To share standards across the team, they must be placed in the project-level CLAUDE.md (the one that gets committed to the repository). The mechanisms described in A and D do not exist. C is wrong because a root-level CLAUDE.md is also a valid project-level location.

### Additional Question 5 (Round 3)

**Scenario**: Code Generation with Claude Code (GitHub: OlivierAlter Q54)

> A monorepo has 5 packages: a Python backend, a TypeScript frontend, a Go service, shared Terraform infrastructure, and a documentation site.


**Question**: The root CLAUDE.md has ballooned to 600 lines, and rules meant for one package are leaking into work on a different package. What is the most modular and maintainable solution using CLAUDE.md configuration?

- **A.** Create a CLAUDE.md in each package directory holding that package's rules, and have the root CLAUDE.md use `@import` to include conventions common to all packages **(correct)**
- **B.** Keep the 600-line root file but add explicit headings, and have developers tell Claude which section applies at the start of each session
- **C.** Delete the root CLAUDE.md and rely entirely on package-level files, accepting duplication of shared conventions across packages
- **D.** Move all rules into `.claude/rules/` files, using a `projects` key in the frontmatter to specify which subdirectory each rule applies to

**Explanation**: CLAUDE.md's `@import` syntax is designed exactly for this modular pattern: common conventions in the root file, package-specific rules in each package's own CLAUDE.md, with the root file importing the shared standards that apply everywhere. This keeps each file focused and maintainable while eliminating rule leakage between packages. B depends on developer discipline and doesn't scale. C removes the shared foundation, duplicating conventions across 5 files and risking maintenance drift. D is valid for path-scoping rules within a single project, but doesn't address the requirement for package-level CLAUDE.md separation.

### Additional Question 6 (Round 3)

**Scenario**: Structured Data Extraction (GitHub: OlivierAlter Q62)

> A structured-data-extraction pipeline processes invoices. There are two extraction tools — `extract_invoice_schema` and `extract_receipt_schema` — and it isn't known in advance which type each document is.


**Question**: After switching to `tool_choice: 'auto'`, 30% of the time the model calls neither tool and instead returns a text description of what it found. What is the correct fix?

- **A.** Set `tool_choice` to `'any'`, which guarantees one of the available tools is called without specifying which one **(correct)**
- **B.** Set `tool_choice` to `{"type": "tool", "name": "extract_invoice_schema"}`, always calling that specific tool
- **C.** Add a system-prompt instruction saying "always call one of the extraction tools; never return text"
- **D.** Consolidate both schemas into a single `extract_document_schema` tool with an optional `document_type` field

**Explanation**: `tool_choice: 'any'` guarantees the model calls a tool rather than returning conversational text, without needing to specify which tool. This is exactly the canonical use case: the document type isn't known in advance, there are multiple valid tools, and you want to ensure one of them is called. B forces a specific tool, which defeats the purpose when the document type isn't known ahead of time. C is a prompt-based approach that is only probabilistically followed, and has already been demonstrated to fail with a 30% text-response rate. D is a valid architectural change, but requires more effort than a single configuration change.

### Additional Question 7 (Round 3)

**Scenario**: CI/CD Pipeline Integration (GitHub: OlivierAlter Q64)

> A CI code-review pipeline is generating a high rate of false-positive findings. The team wants to improve the prompt and needs to understand specifically which code structures are being incorrectly flagged.


**Question**: The current findings output includes only `file`, `line`, and `description`. Which schema change would best enable a systematic analysis of the false positives?

- **A.** Add a confidence field (0-100) to each finding, so the team can filter by a confidence threshold
- **B.** Add a `detected_pattern` field to each finding, recording the specific code structure or pattern that triggered it **(correct)**
- **C.** Add a `category` field, so findings can be grouped by problem type for aggregate analysis
- **D.** Add an `is_false_positive` boolean field, instructing Claude to self-label its own false positives

**Explanation**: A `detected_pattern` field directly records the specific code structure that triggered each finding, letting the team identify which patterns generate the most false positives and update the prompt's criteria accordingly. A provides a confidence score but doesn't identify what triggered the finding. C enables grouping but not pattern-level debugging. D has Claude self-judge its own false positives, but without ground truth the model cannot reliably distinguish true false positives, making it unreliable.

### Additional Question 8 (Round 3)

**Scenario**: Multi-Agent Research System (GitHub: OlivierAlter Q20)

> A coordinator needs to research three independent subtopics in parallel: market trends, competitive analysis, and the regulatory landscape.


**Question**: To maximize throughput, how should the coordinator launch these subagents?

- **A.** Launch the subtopics sequentially: start the first subagent, wait for its result, then start the second, and so on, to avoid context conflicts
- **B.** Issue all three Task tool calls within a single response from the coordinator, allowing the subagents to run in parallel **(correct)**
- **C.** Route all three subtopics to a single subagent sequentially, sharing context to reduce memory usage
- **D.** Give a single subagent three separate prompts in sequence, passing each prior result as context into the next prompt

**Explanation**: The Claude Agent SDK supports parallel subagent execution by issuing multiple Task tool calls within a single response from the coordinator. This maximizes throughput for independent subtopics that don't depend on each other's results. A introduces unnecessary sequential latency. C assumes a dependency relationship that isn't indicated in the question. D collapses three specialized agents into a single sequential process, eliminating parallelism.
