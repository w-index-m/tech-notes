# CCA-F Exam Prep Supplement (Derived from the Slide Deck)

> Translated from the Japanese original: `cca-f-study-guide-supplement.md`

> A supplement meant to be read alongside `cca-f-study-guide.md` (the main guide). After reviewing Anthropic's official PDF-format prep materials (a 137-slide deck), I found several explanations and angles that weren't in the main guide's Markdown, so I've compiled them here separately to avoid duplication.

---

## Table of Contents

1. Absolute Basics (Terminology 101)
2. Exam System & Meta-Strategy
3. The 7 Values of stop_reason
4. Agent Fundamentals & How Claude Code Works
5. MCP vs. Skill: When to Use Which (Supplement)
6. Context Injection, Plan Mode, and Effort Level
7. Prompt Engineering (Evaluation Workflow and 4 Techniques)
8. Criteria for Escalation Design
9. Practical Context Management Patterns (Explore Subagent, Scratchpad, /compact)
10. Lost in the Middle / The Trap of Progressive Summarization
11. RAG (Retrieval-Augmented Generation) Basics
12. Detailed Rules of Prompt Caching
13. Recommended Learning Resources (English)

---

## 1. Absolute Basics (Terminology 101)

Foundational background for CCA-F, broken down so even non-engineers can follow.

- **The difference between chat and the API**: An everyday chat screen is just "a human operates it and gets a reply back," but the API is "a window where programs exchange data directly with each other." To use a delivery analogy: chat is handing something over in person, while the API is delivery with an attached slip. What corresponds to the information on that "slip" are status/flags like `stop_reason` and `isError`.
- **What a status/flag is**: A short "signal" meant for programs, separate from the human-readable "text." Like the colors of a traffic light, or "delivered/not home" for a courier, it's a mechanism for making mechanical judgments without ambiguity in wording. That's why you check `stop_reason` rather than judging "whether the text contains the word 'done.'"
- **What JSON is**: A note-taking format for programs that organizes information as pairs of "item name: value." Structured outputs, JSON Schema, and required/nullable are all rules applied to these individual items within the JSON format.

---

## 2. Exam System & Meta-Strategy

### System details (as of July 2026)
- On June 30, 2026, the exam moved from ProctorFree to Pearson VUE. The official mock exam was discontinued, making the 12 sample questions inside the Exam Guide PDF the only officially confirmed practice material.
- The certification family has expanded beyond CCA-F to four certifications total: Associate-Foundations ($99), Developer-Foundations ($125), and Architect-Professional ($175).
- Sources disagree on the question format (single-select 4-choice only, vs. a mix of single- and multi-select), so verify with the official guide right before your exam.
- Explicitly listed as out of scope: model fine-tuning, detailed API billing calculations, and detailed OAuth implementation.

### What passer data reveals about the trap
- One test-taker scored a perfect score three times in a row on the official mock exam (25 questions, memorization-style) but failed the real exam with 590/1000. Meanwhile, another test-taker with no prior study passed with 890/1000.
- Reason: the mock exam is definition-recall style ("what is X?"), while the real exam is diagnostic/applied style ("the system is broken like this — what's the cause and the fix?"). Rote memorization of terms doesn't prepare you for that.
- The judgment axis running through the entire exam: "Does this rule need to be enforced without exception, or does it just need to generally be followed?" → The former is enforced in code (Hooks, schemas, permissions); the latter is handled via prompting.

### 5 pitfalls (traps) pointed out by overseas sources
1. **Subagent isolation**: Subagents don't automatically share context with the parent. "It can automatically see the parent's information" is a trap.
2. **The CLAUDE.md hierarchy**: There are three levels — project-root / user-global / enterprise — merged in a specific order. Many questions probe conflicts in priority ordering.
3. **Leaking information in tool descriptions**: It's a mistake to write implementation details (e.g., "uses a Postgres backend") in a tool's description. The correct approach is to describe "what the tool does from the user's perspective."
4. **MCP error design**: Returning a raw exception as-is is wrong. The correct approach is a structured error object that includes a recoverable flag and a hint.
5. **Rolling-window context**: As a conversation grows longer, older turns get summarized or dropped. This is easy to overlook but comes up frequently.

### Meta-strategy summary
- Trap answer choices are designed to catch "people who know the concept but lack practical judgment."
- After reading a question, first identify "which Task Statement is this about?"
- Domain 1 concepts (stop_reason, Hooks, subagent isolation) frequently reappear as traps in other domains too.
- When multiple choices seem plausible, choose the simplest solution over over-engineering (two-stage LLM calls, training a separate model, complex classifiers).
- Judgment axis for resuming a session: "a clean new session with an accurate summary" is often higher quality than "resuming while dragging along stale context." `--resume` is valid only when the premises haven't changed.
- Exam-day tips: no translation tools allowed (questions and choices are entirely in English). Pace yourself at about 3 minutes to answer plus 1 minute to review per question. An unanswered question counts as incorrect, so always select something. The passing score is a scaled score, so you can't calculate it as a simple raw percentage.

---

## 3. The 7 Values of stop_reason

The main guide focused on `end_turn` and `tool_use`, but there are actually 7 values, including the following.

| Value | Meaning |
|---|---|
| `end_turn` | The response completed naturally |
| `tool_use` | A tool call is requested (loop continues) |
| `max_tokens` | The token limit was reached (cut off mid-generation) |
| `stop_sequence` | A specified stop string was reached |
| `refusal` | The model refused for safety reasons (from Opus 4.7 onward, this comes with `stop_details.category`, which can be used for routing by safety category — cyber, bio, etc.) |
| `pause_turn` | Paused for a long-running tool, etc. (meant to be continued) |
| (others) | Additional values may be added depending on SDK/API version |

> ⚠️ **How this is tested on the exam**: Resuming generation after `max_tokens` is an important implementation-level topic. Also make sure you understand category-based routing for `refusal`.

### The third, easily-overlooked trap in agent-loop termination logic

In addition to the two error patterns already covered (① judging completion via text parsing, ② cutting off after a fixed number of iterations), there's a third:

- **The mistake**: Judging that the task is "done" by checking `response.content[0].type == "text"`.
- **Why it's wrong**: Claude's response can contain a text block alongside a tool_use block at the same time (explanatory text like "First, let me check this file," co-occurring with a tool call). Just because the first block is text doesn't mean there's no tool call (i.e., it doesn't mean the task is complete).
- **The 3 principles of correct judgment**: ① don't judge completion via natural-language parsing ② don't rely on a fixed iteration count as the primary judgment (it's fine as a safety ceiling) ③ don't rely on the type of the first block → The only thing you can trust is `stop_reason`.

---

## 4. Agent Fundamentals & How Claude Code Works

### The 3 components that make up an agent
- **The brain**: Claude itself (decides what needs to be done)
- **The limbs**: Tools (Read/Write/Bash/Grep, etc. — perform the actual operations)
- **The loop**: The mechanism that repeats "judge → execute a tool → judge again based on the result" until the goal is achieved (the condition for stopping is `stop_reason`)

Claude Code is positioned as a product that specializes this agentic way of operating for development work (coding).

### The 3-step process of a coding assistant
The same mindset as a human developer: ① understand ② change ③ verify — a 3-step process. Steps ① and ③ require "interacting with the outside world," which a language model alone cannot do, so the "tool use" mechanism is used (rules such as "if you want to read a file, respond with 'ReadFile: filename'" are automatically appended to the model's instructions).

**Important**: The model isn't actually reading files — it's only generating correctly formatted text. The actual file I/O, etc., is handled by the host side (Claude Code).

**Why Claude Code can be used safely even on large codebases**: Because it's designed not to require pre-indexing (sending the entire codebase externally).

---

## 5. MCP vs. Skill: When to Use Which (Supplement)

In a nutshell: **MCP = limbs that connect to the outside world, Skill = a procedure manual for a fixed task**. The two aren't mutually exclusive — using them together is the default assumption (it's also common for a Skill's procedure manual to instruct, "use this MCP tool to query the DB").

| | MCP | Skill |
|---|---|---|
| What it provides | The ability to connect to external systems | Reproducibility of a procedure/rule set |
| Benefits | Autonomous and repeatedly callable / can chain multiple tools / standardized across clients / can expose a narrow, safe surface of functionality | Ensures reproducibility / low context cost (body text is only loaded when invoked) / can be combined with MCP |
| Example use cases | "I want to post to our internal Slack," "I want to query the production DB" | "Commit messages must always follow this format," "PR review must always check these 5 items" |

The judgment axis is structurally the same as Domain 1's "model-driven vs. decision tree": it's easy to reason about as — a one-off deterministic process is a script, while something Claude uses continuously with ongoing autonomous judgment is MCP.

---

## 6. Context Injection, Plan Mode, and Effort Level

### @-mentions and AGENTS.md
- Writing `@file-path` in a conversation automatically includes that file's contents in the request. The same `@` syntax also works inside CLAUDE.md (e.g., you can make a frequently referenced DB schema file always get loaded).
- If an `AGENTS.md` for other tools already exists, writing `@AGENTS.md` as the first line of CLAUDE.md lets you load its contents first (no need to duplicate the content).

> ⚠️ **How this is tested on the exam**: "We want to pull in the contents of an existing AGENTS.md into CLAUDE.md without duplicating it" → Write `@AGENTS.md` as the first line of CLAUDE.md.

### The /init command
A command run on a new project that analyzes the entire codebase and auto-generates a CLAUDE.md.

### Planning Mode (/plan)
- How to launch it: type `/plan`, or press Shift+Tab twice (once if auto-edit-approval is already active).
- Claude reads more files and produces a detailed implementation plan to present.
- Before approving, you can press Ctrl+G to open the plan in a text editor and fine-tune it before submitting.
- Well suited to tasks that need a broad understanding of the codebase or changes spanning multiple files.

### Effort Level (/effort)
- Use `/effort` to check and adjust the current level (low = fast and cheap, up to max = spends the most time reasoning on hard problems).
- If you only want deeper thinking for a single prompt, include the keyword `ultrathink` in the prompt (this doesn't change the effort level for the whole session).
- Well suited to complex logic problems, difficult debugging, and algorithmic challenges.

> ⚠️ **How this is tested on the exam**: Planning Mode and Effort Level are controls on different axes. Planning Mode controls "breadth" — which files get read and planned around — while Effort Level controls "depth" — how deeply a single problem gets reasoned through. Both consume additional tokens.

---

## 7. Prompt Engineering (Evaluation Workflow and 4 Techniques)

### The 5 steps of prompt evaluation (eval)
1. Write a first draft of the prompt
2. Build an evaluation dataset
3. Run each data point through Claude
4. Have a grader score each one from 1 to 10
5. Refine the prompt and repeat ① through ④

> The key with a model grader is to have it output not just a score, but also "what's good, what could be improved, and why." Without that reasoning, scores tend to get rounded to a safe ~6 out of 10.

### Raising prompt accuracy with explicit criteria (Official Exam Guide Task 4.1)
- Giving concrete, clearly delineated criteria — rather than vague instructions — reduces false positives.
- When one category has a high false-positive rate, it can erode trust even in the categories that are accurate.
- Countermeasure: temporarily disable the category with many false positives while individually improving the prompt for that category. Providing concrete code examples for severity classification also improves the consistency of judgments.

> ⚠️ **How this is tested on the exam**: "AI review has too many false positives. Should we filter by confidence?" → NO. "Only report items with high confidence" is too vague a criterion. Rewriting the judgment criteria to be concrete is the correct answer.

### The 4 prompt engineering techniques
1. **Clarity and directness**: Use imperative sentences that start with a verb. "What should I eat?" → "Please generate a one-day meal plan."
2. **Specificity (guidelines/steps)**: Explicitly specify output length, structure, and elements to include. For tasks requiring complex judgment, also specify the procedure (process steps).
3. **Structuring with XML tags**: Explicitly separate mixed content — instructions vs. data, code vs. documentation — using custom tags (e.g., `<athlete_information>`).
4. **Few-shot (providing examples)**: Show input/output examples using `<sample_input>`/`<ideal_output>` tags. This improves accuracy on corner cases like sarcasm and irony.

> These are used within an iterative improvement cycle (set a goal → draft → evaluate → apply a technique → re-evaluate). Exam questions tend to probe the whole cycle of evaluation combined with technique, rather than a technique in isolation.

---

## 8. Criteria for Escalation Design (Official Exam Guide Task 5.2)

Appropriate escalation conditions:
- The customer has explicitly requested a human
- There's an exception or gap in policy (mere complexity alone is not enough)
- No meaningful progress can be made

Other principles:
- **Honor the customer's request immediately**: If they say "I want to talk to a human," escalate immediately. If the issue is within a resolvable range, acknowledge their frustration while proposing a resolution, and only escalate if they ask again.
- **Sentiment analysis is not reliable**: Auto-escalation based on sentiment analysis, or the AI's self-reported confidence score, are both uncertain proxy signals that don't correlate with the actual complexity of a case.
- **When multiple matches exist, confirm rather than guess**: If a customer's information matches multiple records, don't pick one via a heuristic — ask for additional identifying information.

> ⚠️ **How this is tested on the exam**: "First-contact resolution rate is below target, and even simple cases are getting escalated" → The correct answer is to add escalation criteria plus few-shot examples to the system prompt. Adding sentiment analysis or a separate classifier model is over-engineering.

---

## 9. Practical Context Management Patterns

### Navigating large codebases (Official Exam Guide Task 5.4)
- **Signs of context degradation**: In a long-running session, if the model starts making vague references like "in the typical pattern" instead of citing specific class names, that's a sign of degradation.
- **Explore subagent**: Isolate the codebase exploration phase (which tends to produce verbose output) into a subagent, and return only a summarized result to the main conversation, preventing the context window from being exhausted.
- **Scratchpad files**: Record important findings to an external file so they can be referenced for subsequent questions.
- **The /compact command**: A command to explicitly compress context usage when verbose exploration results start filling up the context.
- **Designing for crash recovery**: Each agent exports its state to a known location, and the coordinator reads a manifest on resume and injects it into the prompt.

### Human review workflows and confidence calibration (Official Exam Guide Task 5.5)
Even when overall accuracy is high, accuracy can be low for specific document types or fields.

> ⚠️ **How this is tested on the exam**: "Overall accuracy is 97%, so is it fine to automate?" → NO. You should verify accuracy per document type and per field before deciding whether to automate. Aggregate accuracy can hide low accuracy in specific segments.

### Managing source provenance and integrating multiple sources (Official Exam Guide Task 5.6)
- **Preserving the claim-source mapping**: Have subagents output claims, the supporting excerpts, and the source URL/document name in a structured form, and have the integrating agent preserve that mapping when it merges everything together.
- **Handling conflicting statistics**: When numbers disagree across multiple credible sources, don't arbitrarily pick one — present both values with their sources and annotate the discrepancy.
- **Recording timestamps explicitly**: Including the publication date/collection date in structured data prevents a mere difference in timing from being misread as "conflicting information."
- **Rendering appropriate to content type**: Render the integrated output in a format appropriate to the type of content — e.g., financial data as tables, news as prose, technical findings as structured lists.

---

## 10. Lost in the Middle / The Trap of Progressive Summarization

### Lost in the Middle
- A phenomenon (Liu et al., 2023) where LLMs tend to overlook important information placed in the "middle" of a long context. Simply changing the position of information can cause accuracy to drop by 15-30 points.
- Performance traces a U-shaped curve: accuracy is high at the beginning (primacy) and end (recency) of the context, and lowest in the middle.
- Even models touted as supporting "ultra-long context" retain this positional bias. Making the context bigger doesn't solve it.
- The cause is the positional bias of the Transformer's attention mechanism (attention skews toward tokens at the beginning and end).
- **Countermeasures**: Place the most important information at the beginning or end of the context. Limit retrieved chunks to 3-8. Compress long documents via a sliding window or hierarchical summarization before passing them in.

> ⚠️ **How this is tested on the exam**: "We retrieved a large number of chunks with RAG, but accuracy is poor" → The correct answer is to reduce the number of retrieved chunks and reposition the most important chunks at the beginning/end. Widening the context or adding more chunks can backfire.

### The Trap of Progressive Summarization
The topic most favored by the exam in Domain 5. When a long conversation is summarized to save tokens, specific values can get lost.

- What's frightening is that it fails "silently": the summarization process itself doesn't raise an error. The conversation appears to continue without issue.
- Instead of the lost specific value, the model can confidently generate a plausible-sounding value (fabrication).
- **Countermeasure**: Exclude values that may be referenced later — amounts, IDs, dates, etc. — from summarization, and keep them separately as structured data in an external memo (e.g., a scratchpad).

> ⚠️ **How this is tested on the exam**: "After a long conversation, Claude misreports the order details" → The cause is that progressive summarization compressed away / lost specific values. The countermeasure is to preserve important values in a form that summarization won't sweep up (CLAUDE.md, an external file, etc.).

---

## 11. RAG (Retrieval-Augmented Generation) Basics

RAG = a mechanism that retrieves relevant information from external documents and includes it in the context so Claude can answer using it.

> ⚠️ **How this is tested on the exam**: "Search accuracy for part numbers and proper nouns is low" → The correct answer is to combine semantic search (embeddings) with BM25 keyword search (hybrid search). Semantic search is robust to paraphrasing but weak at identifying exact-match identifiers.

---

## 12. Detailed Rules of Prompt Caching

A mechanism that, if you're sending the same content every time, lets you cache that portion to substantially cut cost and processing.

- The order in which things are cached is `tools → system → messages`. Changing something partway through this hierarchy invalidates the cache for everything after that point.
- A cache read costs about 10% of the price of a standard input token. A write (the first time) costs about 25% more, but you make that back on subsequent hits.
- The default TTL (time to live) is 5 minutes. The timer resets on every hit, so as long as you keep using it, the cache stays alive. A 1-hour TTL is also selectable (at additional cost).
- There's a minimum token-count threshold (1,024-4,096 tokens depending on the model). Below that, content won't be cached even with `cache_control` attached.
- `cache_control` can be used in at most 4 places per request.

> ⚠️ **How this is tested on the exam**: "The cache isn't taking effect and we're being billed in full every time" → Typical causes are: ① a variable element like a timestamp got mixed into the system prompt, ② the conversation history is reconstructed every time, shifting the prefix, ③ the cache gets invalidated by adding an MCP tool or switching models. The iron rule is to place variable parts after the portion meant to be cached.

---

## 13. Recommended Learning Resources (English)

- **Anthropic Academy (official, free)**: All 13 courses are directly relevant to the exam. The "Tool Use" and "Agents" tracks offer the best return on time invested. `anthropic.skilljar.com`
- **Official Exam Guide PDF + sample questions**: Required reading. Note, however, that the official practice questions are easier than the real exam, so "a perfect score" doesn't mean "fully prepared." Available from the CCA-F official exam page (Partner Network access required).
- **Community-made study guides (GitHub)**: Paul Larionov's repository (available in 4 languages, `github.com/paullarionov/claude-certified-architect`) and Daron Yondem's exam guide both have good reputations.
- **Reddit r/ClaudeAI**: Searching "CCA-F" turns up many accounts from both passers and failers. The threads from people who failed are especially rich with concrete examples of "what tripped them up."
- **Claude Certification Guide**: 30 lessons and 250+ free practice questions (mock exam / glossary / quick reference). `claudecertificationguide.com`

Recommended study pattern: "First, actually build something yourself (e.g., a Claude Code project using 2-3 subagents) → read through the Academy courses in full → for each domain, write out 10 of your own 'the system is broken like this' practice Q&As."

> * Links to GitHub, Reddit, etc. are unofficial community-produced information. Always verify the accuracy of their content against Anthropic's official information.

---

*This material was compiled from a PDF/slide deck that cross-references Anthropic's official documentation (docs.claude.com / code.claude.com), the Anthropic Academy (anthropic.skilljar.com) course catalog, and information from several third-party certification prep sites, extracting and organizing content not found in `cca-f-study-guide.md`. Actual exam content, scoring, and question trends are subject to change, so be sure to verify against official information before taking the exam.*
