> Translated from the Japanese source: `ccao-f-study-guide.md`

# Claude Certified Associate - Foundations (CCAO-F) Study Guide

> This guide treats the **official Anthropic Exam Guide (version 1.0, effective July 2026; both the English version and the official Japanese translation were checked)** as its primary source, and supplements and cross-checks it against several articles the user collected (the official blog, exam-experience reports, the official Pearson VUE page, and actual sample practice questions). The "Detailed Objectives by Domain" and the "3 Official Sample Questions" sections are reproduced from the official translation as-is.
>
> ⚠️ **Important note**: According to the official Pearson VUE page, **the content of this exam is scheduled to be updated starting December 7, 2026** (the updated exam guide is expected to be published on November 17, 2026). This guide is based on version 1.0 (effective July 2026). If you are taking the exam on or after December 7, be sure to check the latest version of the exam guide.
>
> Note: CCAO-F (Claude Certified **Associate** - Foundations) is a **separate credential** from `cca-f-study-guide.md` (Claude Certified **Architect** - Foundations, exam code CCAR-F), which already exists in this repository. The Associate version (this guide) is aimed at **business roles** and assumes no programming or API experience, while the Architect version is aimed at **technical practitioners** working with agent design, MCP, and Claude Code.
>
> **Background**: In March 2026, Anthropic launched the "Claude Partner Network (CPN)" with a $100 million investment to help partner companies adopt Claude. Initially, only a technical certification for architects (Claude Certified Architect, Foundations) was offered, but as promised — "additional certifications for sellers, architects, and developers will be introduced later this year" — the program expanded to a four-certification structure, including this credential (CCAO-F), in July 2026.

---

## 1. About This Certification (from the Official Exam Guide)

The Claude Certified Associate – Foundations certification validates the ability to **use Claude to complete business and productivity tasks with minimal support**. This includes streamlining workflows using the platform's built-in features, capabilities, and tools; identifying opportunities to improve processes with Claude; choosing approaches that balance quality, efficiency, and cost; and recognizing limitations and escalating more complex or technical work to Claude architects or developers.

This certification is intended for **practitioners who use Claude as a productivity tool** in roles such as operations, marketing, project management, education, and communications.

### Who This Certification Is Not For

This certification is not intended for software developers who build with the API or design agentic systems, nor for specialists in machine learning, software engineering, or advanced AI system design. That scope falls under the Claude Architect and Claude Developer certifications.

### Minimally Qualified Candidate (MQC) Profile

The exam is designed around a **Minimally Qualified Candidate (MQC)** profile — the minimum bar a candidate must clear to pass. The MQC is a practitioner who uses Claude as a core productivity tool, applies it effectively within real workflows to improve efficiency, quality, and outcomes, and has foundational, practical knowledge of capabilities such as structuring prompts, orchestrating tasks, and working with projects, artifacts, and workflow-based interactions.

**Recommended experience (not required)**:
- Regular, hands-on use of Claude in a work setting
- A foundational understanding of structured problem-solving, workflow design, and the use of digital tools
- Experience in roles such as business analyst, project manager, operations lead, marketing/communications/HR/education practitioner, consultant, or knowledge worker
- A practical understanding of AI limitations, including hallucination, context constraints, and data sensitivity

**Prerequisites: there are no required prerequisites or mandatory courses.** No software development or API experience is required. The certification is awarded based solely on exam performance.

---

## 2. Exam Overview (from the Official Exam Guide)

| Item | Details |
|---|---|
| Certification | Claude Certified Associate – Foundations |
| Exam code | CCAO-F |
| Number of questions | 60 |
| Question format | Multiple-choice and multiple-select questions. Each question specifies how many answers to select |
| Time limit | 120 minutes |
| Delivery | Proctored: online proctoring, a testing center, or both, depending on program policy |
| Passing score | Scaled score of **720** on a 100–1,000 scale |
| Exam fee | $99 USD |
| Validity period | **12 months** from the date the certification is awarded |
| Results reporting | Pass/fail and a scaled score (100–1,000). Performance by domain is also reported |

### Scoring Methodology

This exam uses a **criterion-referenced assessment**. Each candidate is measured against a fixed performance standard, not against other candidates. The passing score was established through a formal standard-setting study, in which trained subject matter experts determined the level of performance expected of an MQC. Section-level performance is provided for self-awareness purposes only and **is not used to determine pass/fail** (pass/fail is determined solely by the overall scaled score).

---

## 3. Exam Domains and Weighting (from the Official Exam Guide — 7 domains total)

| Domain | Content Area | Weight |
|---|---|---|
| 1 | Prompting and Task Execution | 14% |
| 2 | Evaluating and Validating Outputs | **21% (highest weight)** |
| 3 | Product and Model Selection | 12% |
| 4 | Workflow Integration and Solution Design | 16% |
| 5 | Configuration and Knowledge Management | 12% |
| 6 | Governance, Risk, and Responsible Use | 15% |
| 7 | Troubleshooting and Optimization | 10% |

**The highest-weighted domain is not "Prompting" but "Evaluating and Validating Outputs" (21%)**. This indicates that what the exam values most is not "the ability to get the AI to write something," but "the ability to judge whether the AI's output is actually fit for use at work."

---

# Domain 1: Prompting and Task Execution (Weight: 14%)

## Official Objectives (from the Exam Guide)
- Craft effective prompts for business and technical tasks
- Apply task decomposition techniques to structure complex requests
- Iteratively refine prompts to improve output quality
- Adapt prompting strategies to the type of task (analysis, research, drafting, brainstorming)

## Commentary

### 1-1. Basic Elements of a Good Prompt
- **A specific goal**: be clear about what you want to achieve
- **Context**: convey background information, constraints, and the intended audience
- **Specify the output format**: specify bullet points, tables, length, tone, etc.
- **Examples (few-shot)**: giving one or two examples close to the desired output improves accuracy

### 1-2. Task Decomposition
For complex requests, breaking the task down into stages tends to produce more accurate results than throwing everything at the model at once. A real example from the question bank (from a practice question supplied by the user): for a request to "create a customer journey map," the correct decomposition order is ① define the persona and mapping scope → ② identify the key stages → ③ identify the touchpoints at each stage → ④ extract pain points/opportunities → ⑤ integrate into the final deliverable — an order that moves **from the big picture to the details, and from analysis to synthesis**.

### 1-3. Iterative Refinement
When output gradually drifts away from the requested template, the systematic approach (confirmed from a practice question supplied by the user) is **not to keep making large changes hoping to stumble onto a match (a bad move), but to roll back to the version that came closest to the request and adjust again from that baseline**.

### 1-4. Adapting Strategy to Task Type
- Analysis: centers on organizing and evaluating data/information
- Research: centers on synthesizing information from multiple sources
- Drafting (writing): centers on adjusting tone and phrasing for the audience
- Brainstorming: centers on generating a divergent list of options

### 1-5. Deciding Whether to Give Constraints Up Front During Brainstorming (a key exam theme)
A real example from the question bank (from a Udemy practice exam): when a task is producing nothing but safe, generic suggestions, giving constraints such as budget or time window **up front** and asking Claude to generate "only ideas that fit the conditions from the start" backfires — Claude leans even further toward playing it safe. The correct approach is to **first ask broadly for a wide range of diverse ideas, and only afterward apply the constraints to narrow them down** (diverge, then converge).

That said, **the situation is different when a constraint is a mandatory compliance requirement** (such as approved wording or prohibited language) — those should be stated up front. In the example where "a submission containing a previously rejected claim came up," the correct answer is to **state the approved claims and prohibited wording as constraints first, generate multiple options, and then review them**. In short: preference-based narrowing constraints (budget, time window) are applied later, while mandatory compliance constraints are stated up front — knowing which is which is the point being tested.

---

# Domain 2: Evaluating and Validating Outputs (Weight: 21% — highest weight)

## Official Objectives (from the Exam Guide)
- Evaluate the accuracy and completeness of Claude-generated output
- Identify hallucinations, inconsistencies, and bias in responses
- Apply fact-checking and verification techniques
- Determine when human review or additional verification is required
- Edit, adapt, refine, and compare outputs for the intended audience
- Organize and curate information, and select the appropriate output format (artifacts, inline, structured data)

## Commentary

### 2-1. Detecting Hallucination and Fact-Checking
As in the official sample question (below), Claude can **plausibly fabricate specific-looking citation numbers or figures**. The more important a verifiable claim is (numbers, clause/section numbers, proper nouns, dates), the more it needs to be checked against a primary source. **Having Claude self-report its own "confidence level" is not a reliable indicator of accuracy** (per the official explanation).

### 2-2. Recognizing When Human Review Is Needed
- The higher the risk of a situation (contracts, regulatory matters, healthcare, legal, external publication), the more essential human review becomes
- A real example from the question bank: "an unedited draft for a paying customer that includes an SLA (service-level) commitment" is treated as the high-impact situation that most needs escalation/review (from a practice question supplied by the user)

### 2-3. Editing, Adapting for Audience, and Comparing Outputs
Even with the same content, adjust the phrasing and level of detail depending on the audience (e.g., executives vs. frontline staff). Compare multiple outputs and judge which one more closely matches what was requested.

### 2-4. Choosing the Output Format
- **Artifacts**: when you want to save and keep iterating on a self-contained deliverable
- **Inline**: for short answers that are complete within the flow of the conversation
- **Structured data**: when downstream processing (ingestion into another system, etc.) is required

A real example from the question bank (from a Udemy practice exam): in a situation where you need to **quickly compare a large number of items side by side within a limited amount of time** (e.g., 12 regions × multiple metrics), a **structured table with aligned rows and columns** is the most appropriate choice — more so than narrative prose or bullet lists. A design that pushes detail into "available on request" tends to be a poor fit when the requirement is to compare every item against the same criteria at a glance.

---

# Domain 3: Product and Model Selection (Weight: 12%)

## Official Objectives (from the Exam Guide)
- Select the appropriate Claude product capability (Projects, Research, Chat, Artifacts)
- Distinguish between Claude's model types (Haiku, Sonnet, Opus)
- Match model selection to task requirements (cost, speed, quality)
- Understand and manage context limitations and memory considerations (when to start over, summarize, or retain)

## Commentary

### 3-1. Choosing Among the Model Lineup
- **Haiku family**: fast and low-cost. Suited to simple tasks and high-volume processing
- **Sonnet family**: a balanced option
- **Opus family**: high-performance and high-cost. Suited to tasks that require complex reasoning

As in the official sample question, for "generating a large volume of short customer-reply drafts, where speed and cost matter more than deep reasoning," **using a fast, low-cost model is the correct answer**, while "always use the top-tier model" is incorrect because it wastes cost and latency budget.

### 3-2. Choosing a Product Capability
| Product capability | Best suited for |
|---|---|
| Chat (one-off) | One-off questions, thinking out loud |
| Projects | Ongoing projects, or wanting to keep reference materials together |
| Research | Research tasks spanning multiple sources |
| Artifacts | When you want to save and iteratively edit a self-contained deliverable |

### 3-3. Managing Context Limits and Memory
When a conversation has gotten too long, judge whether it's appropriate to "start a fresh conversation," "summarize and carry the summary forward," or "retain it as project knowledge."

---

# Domain 4: Workflow Integration and Solution Design (Weight: 16%)

## Official Objectives (from the Exam Guide)
- Apply Claude to requirements and use-case analysis
- Use Claude to support research, planning, and process optimization
- Use Claude to support the design, development, and iterative improvement of solutions
- Integrate Claude into existing workflows, strengthening or redesigning them
- Communicate Claude's value and limitations to stakeholders

## Commentary

### 4-1. Patterns for Embedding Claude in Business Processes
One-off use (ad hoc chat), Projects (ongoing materials and context retention), Connectors (integration with external tools, see Domain 5), and automating/templating routine work.

### 4-2. Communicating Value and Limitations
When rolling out Claude, accurately conveying "what it can and can't do" to stakeholders is also part of the design work. Avoid both overselling it (letting people think it's an all-purpose tool) and underselling it (letting people think it's just a chatbot).

### 4-3. Redesigning Workflows vs. Simply Extending Them
Beyond just bolting Claude onto an existing process, the exam also tests the perspective of **redesigning the process itself around Claude's capabilities** (for example, changing the order of an approval flow, or repositioning where human review points sit).

### 4-4. The Correct Procedural Pattern After a Redesign (a key exam theme)
A real example from the question bank (from a Udemy practice exam): when redesigning a task that gathers information from multiple sources to produce a report or presentation, the correct procedure consistently follows the pattern **"① gather from approved sources while recording provenance → ② flag contradictions → ③ human reviewer approval → ④ produce the deliverable → ⑤ distribute."** In other words, keep to the order "gather → verify → human approval → generate → distribute"; reordering steps — such as producing the deliverable before approval or rushing distribution — is incorrect.

### 4-5. Addressing Root Causes (a key exam theme)
For an inefficient workflow where the same data is manually copied into multiple documents and inconsistencies are reconciled by hand, the correct fix at the root is: **"standardize the source fields into a single, reviewed dataset, and have Claude generate each deliverable from that single source."** Band-aid fixes — adding more documents to track the differences, removing review while automating only, or having Claude fix only the downstream output — are all insufficient (this echoes the same thinking as the automation-step design in Domain 7).

### 4-6. Requirements Analysis Grounded in Real Data (Evidence)
For process-improvement requests, the core of Domain 4 is **using actual completion records or logs, when they are available, to identify bottlenecks** — not just describing the current procedure. Simply applying generic best practices (industry standards, etc.) as-is fails to reflect an organization's actual, specific situation. The pattern "map the current state → identify constraints from the data → propose targeted options for human review" comes up repeatedly in the question bank.

---

# Domain 5: Configuration and Knowledge Management (Weight: 12%)

## Official Objectives (from the Exam Guide)
- Configure Claude Projects using instructions and knowledge sources
- Manage uploaded knowledge and connectors (Google Drive, Gmail, etc.)
- Write effective system-level instructions
- Keep Claude's configuration, knowledge sources, and instructions up to date and reflective of current information

## Commentary

### 5-1. Configuring Projects
Set project-specific instructions (custom instructions) to convey tone, format, and background knowledge up front for every response. Upload relevant materials as knowledge sources so you don't have to repeat the same explanation every time.

### 5-2. Managing Connectors
Connect to external tools such as Google Drive and Gmail to work against real data. Manage connection scope and permissions appropriately, also from a governance perspective (Domain 6).

### 5-3. Ongoing Maintenance of Configuration (a key exam theme)
A real example from the question bank: the habit of never updating a project's configuration on the assumption that "once it works, it works forever" is inappropriate. **Even if there is no visible decline in output quality, configuration should be reviewed periodically whenever policies, terminology, or reference documents change** (from a practice question supplied by the user). Neither a mechanical update on a fixed schedule nor reacting only after quality has already degraded is correct — **periodic review driven by the actual pace of change** is the correct pattern.

---

# Domain 6: Governance, Risk, and Responsible Use (Weight: 15%)

## Official Objectives (from the Exam Guide)
- Identify appropriate and inappropriate use cases
- Apply data sensitivity, regulatory, and privacy considerations
- Follow organizational AI policies and governance standards
- Understand the ethical implications of AI use

## Commentary

### 6-1. Handling PII (Personally Identifiable Information) and Sensitive Data
As in the official sample question, when you want Claude to analyze a spreadsheet containing regulated personal data such as customer names and account numbers, **the correct action, per policy, is to remove or anonymize personally identifying information before uploading it**. "Upload it as-is because it's for internal use only" and "just instruct Claude not to retain it" are both insufficient, and "abandon the analysis altogether" is unnecessary since anonymization makes the analysis possible.

The same thinking recurs in real examples from the question bank (from practice questions supplied by the user):
- ✅ An anonymized HR policy summary, a redacted job posting → appropriate
- ❌ Employee ID and start date, name plus salary amount, raw accommodation-request notes → inappropriate (raw personal information)

**A criterion for judging sensitivity** (from a Udemy practice exam): data where **multiple sensitive elements overlap**, such as "a government-issued ID plus a full payment card number linked to a name," most clearly calls for additional handling (deletion, anonymization, or individual authorization). By contrast, a statistical re-identification risk — such as aggregated figures for a small group (e.g., region-level totals from fewer than five respondents) — is worth treating as a distinct category of risk that calls for more nuanced judgment.

### 6-2. Organizational Governance Structures
Document usage policies and guidelines clearly within the organization and make sure people are aware of them. Define who may use Claude, for what work, and within what scope.

**Judging appropriate use cases** (from a Udemy practice exam): a use case such as "Claude drafts from an approved procedure document, and a human reviews and field-tests it before use" is a textbook example of appropriate use. The balance being tested is: **don't ban AI drafting outright** (an overly conservative wrong answer), **don't skip the human review/testing step** (an overly optimistic wrong answer), and **don't over-escalate a task that isn't actually complex enough to belong in Architect/Developer territory**, treating ordinary internal document drafting as more than it is.

### 6-3. The AI Fluency Framework (the "4 Ds," for reference)
Anthropic's proposed four components of "AI fluency." These aren't explicitly listed in the exam domains, but are worth knowing as related background:

| Component | Description |
|---|---|
| **Delegation** | Deciding which tasks to hand to AI and which to keep with humans |
| **Description** | Giving the AI clear, specific instructions and context |
| **Discernment** | Critically evaluating and verifying AI output (directly tied to Domain 2) |
| **Diligence** | Taking personal responsibility for how AI is used, with attention to ethics and compliance |

---

# Domain 7: Troubleshooting and Optimization (Weight: 10%)

## Official Objectives (from the Exam Guide)
- Identify, diagnose, and resolve issues with prompts that aren't working as expected or output that is low quality
- Adjust approach based on feedback and results
- Optimize workflows for greater efficiency and effectiveness

## Commentary

### 7-1. Basic Patterns for Isolating the Cause
| Symptom | Likely cause | Fix |
|---|---|---|
| Output misses the point | Insufficient instructions/context | Add the goal and background information |
| Output is too long/too short | No output format was specified | Specify length, number of bullet points, etc. |
| Output varies every time | The prompt is ambiguous | Switch to more specific instructions |

### 7-2. Scope of Response to Review Feedback (a real example from the question bank)
When a reviewer makes only two specific pieces of feedback, the appropriate response (confirmed from a practice question supplied by the user) is to **address only those two points, leave unrelated parts as they are, and log the changes for the next review**. Changing parts that weren't flagged, rewriting from scratch, or unilaterally adding improvements beyond what was flagged are all incorrect patterns of scope creep.

### 7-3. Designing Reliable Automation Steps (a real example from the question bank)
When automating a routine data-processing step with code execution, the appropriate design choices for a trustworthy integration are:
- **Standardize the input format** so the output is consistently verifiable every time
- Add a **lightweight output-verification step** so a human reviewer can sanity-check the result against the expected range before it moves to the next stage

Conversely, "changing the input format every time," "eliminating human review," and "embedding an undocumented output format" are all incorrect patterns that undermine reliability.

---

# Exam Tip: The "Verb Pattern" Hint

A useful, practical observation for reading comprehension, drawn from an exam-experience article (Qiita) the user collected:

- **Verbs that tend to appear in correct answer choices**: verify, escalate, flag, anonymize/redact — verbs that mean "check first, then proceed in steps"
- **Verbs that tend to appear in incorrect answer choices**: send as-is, assume, skip — verbs that mean "proceed without checking"

This is consistent with the correct-answer pattern across all three official sample questions (cross-check and verify / select the appropriate model / remove or anonymize), and also consistent with the correct-answer pattern across the unofficial practice questions collected in this guide. It's a useful cue when you're unsure.

---

# Exam Policies (from the Official Exam Guide + the Official Pearson VUE Page)

## Registration and Scheduling Process
1. Visit the certification page for the exam on the Anthropic Partner Academy and review the exam details
2. Before registering, download the exam guide and review the "Certification Terms of Use" and "Certification Exam Policy"
3. Register for the exam and complete payment (discounts based on partner tier are applied)
4. Follow the confirmation instructions to create a Pearson VUE account, sign in, and schedule the exam session
5. Choose an available date and select either online proctoring or a Pearson testing center
6. **Rescheduling/cancellation**: the official exam guide states "free up to 24 hours before the scheduled time," while the official Pearson VUE page states "up to 48 hours before" in some places — there is a slight discrepancy. It's safest to allow extra margin and finish up to 48 hours in advance. Changes made after the deadline are non-refundable

## Eligibility
Open to **individuals affiliated with a company that is a member of the Claude Partner Network (CPN)** (registration with a company email address is required). At this time, it is not an exam that anyone can take.

## Identity Verification
On exam day, a valid, unexpired, government-issued photo ID is required. The name must exactly match the name used at registration (to correct a mismatch in advance, contact `certifications-support@anthropic.com`).

## Retake Policy
If you fail, you may retake the exam after a waiting period of **14 days after the first attempt, 30 days after the second, and 90 days after the third**. You may take the exam **up to 4 times within any rolling 12-month period** (the limit applies per exam). The exam fee is charged for each attempt.

## Exam-Day Rules
- For online delivery, you must remain within view of the proctor/webcam at all times during the session
- No notes, books, phones, secondary monitors, or similar items may be present in your workspace
- Communicating or talking with others during the exam is prohibited, as is recording, copying, photographing, or reproducing exam content in any way
- Prohibited items: cell phones, smartwatches, headphones, study materials, and any recording devices
- Misconduct (cheating, attempting to access prohibited resources, disclosing exam content) can lead to invalidated results, revocation of certification, and a ban from future exams

## Confidentiality (Non-Disclosure Agreement)
Agreement to confidentiality and a non-disclosure agreement is required before the exam begins. If you do not agree, the exam session will be terminated without a refund.

## Maintaining and Renewing Certification
- The certification is valid for **12 months** from the date it is awarded
- Renewing within the validity period is done through a **free, unproctored assessment** on the Anthropic Partner Academy (no cost)
- If the certification has lapsed, you must **retake the full exam** at the regular fee
- If exam content has changed substantially, Anthropic may require a full exam retake instead of a renewal assessment

## OnVUE Online Exam Network Requirements
If taking the exam from a corporate network, reachability to the following domains (and their subdomains) is required (partial list): `*.pearsonvue.com`, `*.onvue.com`, `*.pearson.com`, `*.certiport.com`, `*.twilio.com`, `*.wowza.com`, and others. It's recommended to check with your internal network administrator in advance.

---

# How This Differs from Other Claude Certifications (reference: from third-party articles)

Claude certifications consisted of only CCAR-F when the Claude Partner Network launched in March 2026, but expanded to a four-certification structure starting in July 2026.

| Certification | Exam code | Fee | Questions | Primary audience |
|---|---|---|---|---|
| Claude Certified Associate – Foundations | CCAO-F | $99 | 60 | Business roles that use Claude at work (this guide) |
| Claude Certified Developer – Foundations | CCDV-F | $125 | 53 | Engineers building with the Claude API/Agent SDK |
| Claude Certified Architect – Foundations | CCAR-F | $125 | 60 | Architects designing production solutions (covered by `cca-f-study-guide.md` in this repository) |
| Claude Certified Architect – Professional | CCAR-P | $175 | 63 | Mid-to-senior architects and tech leads |

The basic rule of thumb is to choose by job function: CCAO-F for roles that don't assume coding, CCDV-F for roles doing development work with the Claude API/MCP. Note also that **CCAO-F does not count toward Anthropic's Partner Network tier requirements** (it's positioned as a credential for individual skill verification, per a third-party source).

---

# Question Bank (30 questions total: 3 official samples + 8 original practice questions + 19 Udemy practice-exam questions)

> Questions 1–3 are the **official sample questions published in Section 8 of the official Anthropic Exam Guide** (explicitly noted as not excerpted from the actual item pool). Questions 4–11 are **original practice questions** written for this guide. Questions 12–30 are questions, **confirmed against the answer key**, taken from a Udemy practice exam the user actually took (Practice Exams: Claude Certified Associate Foundations CCAO-F). All of these are unofficial and are not actual Anthropic exam questions, but questions 12–30 were actually graded and their correct answers confirmed. The questions are ordered to follow the flow of Domains 1 through 7.

### Question 1 (Domain 2: Evaluating and Validating Outputs) — Official Sample Question

**Question**: An associate asked Claude to summarize a new regulation, and Claude produced a confident summary citing specific section numbers. What is the most appropriate action to take before sending this summary to the compliance team?

- A. Send it as-is, since Claude expressed high confidence.
- B. Cross-check the cited sections against the official regulatory text before sharing it. **(Correct)**
- C. Have Claude rate its own confidence, and send it if the rating is high.
- D. Rewrite the summary to sound more formal before sending it.

**Explanation**: Language models can fabricate specific-looking details, such as citation numbers — this is hallucination. The required step is to verify factual claims, especially citations intended for compliance staff, against a trustworthy source. Self-reported confidence (A, C) is not a reliable indicator of accuracy, and changing the tone (D) does not address the underlying correctness problem.

### Question 2 (Domain 3: Product and Model Selection) — Official Sample Question

**Question**: An associate needs to generate a large volume of short customer-reply drafts, where speed and cost matter more than deep reasoning. Which option is best suited to this task?

- A. Use the most capable and most expensive model for every reply, to maximize quality.
- B. Use a faster, lower-cost model suited to simple, high-volume tasks. **(Correct)**
- C. Disable all product features to reduce cost.
- D. Switch to a different AI platform.

**Explanation**: Matching model selection to task requirements means assigning a faster, lower-cost model to simple, high-volume work, and reserving the most capable model for complex reasoning. Always using the top-tier model (A) wastes cost and latency budget. Disabling features (C) or switching platforms (D) does not address this trade-off.

### Question 3 (Domain 6: Governance, Risk, and Responsible Use) — Official Sample Question

**Question**: A project manager wants to upload a spreadsheet containing customer names and account numbers to have Claude analyze trends. Organizational policy restricts sharing regulated personal data. What is the most appropriate action?

- A. Upload the file as-is, since the analysis is for internal use only.
- B. Remove or anonymize personally identifying information before uploading, in accordance with policy. **(Correct)**
- C. Upload the file but instruct Claude not to retain it.
- D. Abandon the analysis altogether.

**Explanation**: Applying data sensitivity and privacy protections means removing or anonymizing regulated identifying information before use, which allows the analysis to proceed without exposing protected data. Uploading as-is (A) violates policy. Instructing the model not to retain the data (C) does not satisfy the policy's control requirements. Since anonymization makes the analysis possible, abandoning the task (D) is unnecessary.

### Question 4 (Domain 1: Prompting and Task Execution — Task Decomposition) — Original

**Question**: What is the correct order for decomposing the request "create a customer journey map"?
(1) Identify the touchpoints at each stage (2) Define the persona and mapping scope (3) Identify the pain points/opportunities at each touchpoint (4) Integrate the results into the final deliverable (5) Identify the key stages the persona moves through

- A. 2, 5, 1, 3, 4 **(Correct)**
- B. 2, 1, 5, 3, 4
- C. 5, 2, 1, 3, 4
- D. 4, 3, 2, 1, 5

**Explanation**: The correct order moves from the big picture to the details: "define scope → big picture (stages) → detail (touchpoints) → analysis (extract pain points) → integration."

### Question 5 (Domain 1: Prompting and Task Execution — Iterative Refinement) — Original

**Question**: You've been refining a prompt across several iterations, but with each change the output drifts further from the requested template. What is the most appropriate next step?

- A. Stop iterating and accept the version that has drifted the furthest
- B. Discard all previous versions without keeping any record of what worked, and start over from scratch
- C. Keep stacking large changes at once until the output happens to match by chance
- D. Roll back to the prompt version that produced the closest result, and adjust again from that baseline **(Correct)**

**Explanation**: This reflects the basic posture of systematic debugging: return to the best known state, then make small adjustments from there.

### Question 6 (Domain 2: Evaluating and Validating Outputs — Fact-Checking) — Original

**Question**: A marketing staffer had Claude draft a report that includes figures on competitors' market share. Before using this report as-is in an external-facing document, what should be prioritized first?

- A. Use it as-is, since Claude's output is reliable enough
- B. Cross-check the specific figures in the report (such as market share) against primary sources **(Correct)**
- C. Regenerating it with a more capable model removes the need for verification
- D. It's enough to check only that the writing reads well

**Explanation**: Verifiable specific figures carry hallucination risk, so checking them against primary sources is the top priority. The same reasoning as official sample question 1 (Question 1).

### Question 7 (Domain 2: Evaluating and Validating Outputs — Escalation Judgment) — Original

**Question**: A customer success manager is deciding whether a Claude-generated response needs to be escalated before external use. Which factor most strongly supports escalation?

- A. It is an unedited draft for a paying customer that includes a service-level commitment **(Correct)**
- B. It is a brief internal announcement about an upcoming all-hands meeting
- C. It is an internal draft summarizing a call with a customer, for the staffer's own reference
- D. It is a casual paraphrase of an internal status update

**Explanation**: The combination of being external, for a paying customer, carrying a contractual implication (SLA), and unedited, together represent the highest impact (= risk), warranting escalation. B, C, and D are all internal, low-risk contexts.

### Question 8 (Domain 5: Configuration and Knowledge Management — Timing of Configuration Updates) — Original

**Question**: A senior manager is reconsidering a colleague's habit of never updating the team's project configuration, on the reasoning that "once it works, it works forever." What is the most reasonable feedback?

- A. If the team is satisfied with current output, the configuration can stay as-is, and any needed updates can be handled at the chat level
- B. Regardless of whether underlying policies or reference documents have changed, the configuration should be updated on a fixed, periodic schedule
- C. Proactive updates risk breaking a configuration that's already working, so it should only be updated once there is a visible decline in output quality
- D. Policies, terminology, and reference documents change over time, so the configuration should be reviewed and updated periodically even without a visible decline in output quality **(Correct)**

**Explanation**: D is most reasonable because it ties the need for updates to actual change (policy, terminology, reference documents) rather than to "visible decline." A leaves the problem unaddressed, C is purely reactive, and B is an inefficient mechanical schedule unrelated to whether change has actually occurred.

### Question 9 (Domain 6: Governance, Risk, and Responsible Use — Data to Include in Prompts) — Original

**Question**: An HR staffer is deciding what data to include in a Claude prompt for a routine task. Which **two** data items are most appropriate? (Each correct answer stands on its own.)

- A. Team roster employee ID numbers and start dates, to help personalize onboarding communications
- B. A compensation band summary for a specific team, including employee names and current salary amounts
- C. An anonymized HR policy summary that the model will paraphrase **(Correct)**
- D. Accommodation-request notes from an employee's HR file, needed to draft a role-adjustment memo
- E. A redacted job description that the model will tailor for a posting **(Correct)**

**Explanation**: C and E both include content in the prompt only after personally identifying information has been removed or anonymized. A (employee ID + start date), B (name + salary), and D (raw, sensitive accommodation notes) all contain raw personal information and are inappropriate.

### Question 10 (Domain 7: Troubleshooting and Optimization — Scope of Response to Review Feedback) — Original

**Question**: A staffer is addressing a reviewer's feedback on a Claude-drafted document (exactly two specific points). The rest of the document has already been approved as-is. Which next step is most likely to produce another round of strong revisions?

- A. Address the two flagged points, and also revise unrelated sections to keep a consistent tone after editing
- B. Incorporate the two points but, thinking it would be more cohesive, rewrite the document from scratch
- C. Address the two flagged points directly, leave unrelated sections untouched, and log the changes for the next review **(Correct)**
- D. Address the two flagged points plus a third improvement the staffer noticed on their own

**Explanation**: Sticking strictly to the scope of the review feedback and logging the change history is the appropriate approach. A and D exceed scope, and B is an inefficient full rewrite.

### Question 11 (Domain 7: Troubleshooting and Optimization — Designing Automation Steps) — Original

**Question**: An operations staffer is redesigning steps in a routine data-processing workflow using code execution. Which **two** design choices best support a trustworthy integration?

- A. Standardize the input file format the step expects, so the output is consistently verifiable every time **(Correct)**
- B. Change the input file format on every run to accommodate different source systems each week
- C. Since the sandbox environment has already confirmed the code ran without error, eliminate the human review step after code execution
- D. Embed the code-execution step in the workflow without documenting the expected output format or fields
- E. Define a lightweight output-verification step where a human reviewer checks the expected range and sanity of the output before it moves downstream **(Correct)**

**Explanation**: The combination of standardizing input (A) and a human validity-check step (E) is the basic recipe for a trustworthy automation integration. B (inconsistent formats), C (eliminating human review), and D (undocumented) all undermine reliability.

### Question 12 (Domain 2: Evaluating and Validating Outputs — Cross-Checking Before Distribution) — Udemy Practice Exam

**Question**: An operations director asked Claude to summarize new packaging regulations. The summary is based on a trade newsletter written before the final rule was published. The director has the final rule in hand, and warehouse supervisors are set to change labeling practices starting next week. What should the director do before distributing the summary?

- A. Cross-check each obligation, date, and scope statement against the final rule, and correct the summary before it reaches the warehouse supervisors **(Correct)**
- B. Send it as-is with a note that "details may change," and update it once the newsletter publishes a follow-up
- C. Upload the final rule and have Claude regenerate the summary from it, then send it to the supervisors next week as-is
- D. Use Research to find a newer article on the regulation and swap it in as the summary's source citation

**Explanation**: Cross-checking against and correcting the summary with the primary source (the final rule) — a "verify" type of response — is correct. C is good in that it uses the final rule, but its weakness is distributing the result without any human check in between (regenerating the summary does not reduce hallucination risk to zero). B defers verification, and D is an ad hoc swap of citations; both are insufficient.

### Question 13 (Domains 2/5: Evaluating and Validating Outputs / Configuration and Knowledge Management — Designing a Review Handoff) — Udemy Practice Exam

**Question**: A tour-booking reception team uses Claude to draft replies to school group booking requests. Staff need to verify dates, fees, and cancellation policy before sending. A workflow analyst is designing the "handoff" to the staff member who approves each draft. Which design best defines responsibility and review scope?

- A. Have a second Claude chat approve the draft, and have the reviewer confirm the two chats' conclusions agree before sending
- B. Have the reviewer take a close look only when Claude flags uncertainty, and spot-check drafts it didn't flag
- C. Have Claude generate an approval checklist from its own draft, and have the human reviewer treat that checklist as the full review criteria for every booking reply
- D. Document what Claude should include in each draft, and spell out which items — booking facts, policy checks, approval conditions — the human reviewer must verify **(Correct)**

**Explanation**: A and C create a circular structure where the AI itself (or another instance of it) defines the review criteria, which isn't independent human verification. B relies too heavily on Claude's own self-reported flagging. Only D explicitly and independently defines what the human must verify.

### Question 14 (Domain 6: Governance, Risk, and Responsible Use — AI Fluency/Diligence) — Udemy Practice Exam

**Question**: A manager of a cultural heritage program had Claude draft partner-facing materials to be sent outside the organization. Before distribution, they want to apply the "Diligence" competency from the AI Fluency Framework. Which action best reflects Diligence?

- A. Have an experienced colleague proofread tone, clarity, and formatting to confirm it matches the organization's house style
- B. Have a separate Claude chat review it for errors, unsupported claims, and inconsistent figures, apply the suggested fixes as-is, and send it to the partner with a short cover note describing the changes
- C. Add a line disclosing that the material was created with Claude's assistance, explain which parts Claude produced and how it was used, and offer to provide a source list on request
- D. Identify claims that need independent confirmation, cross-check them against authoritative sources on record before distribution, and take personal, professional responsibility for the version the manager sends **(Correct)**

**Explanation**: Diligence is at its core "taking personal responsibility for how AI is used." Only D explicitly combines "independent verification" with "taking ownership of responsibility." A is generic proofreading (a style check), B offloads the task to another AI (with no human ownership), and C is a transparency disclosure, which is a different axis from verification and responsibility.

### Question 15 (Domain 3: Product and Model Selection — Matching Features to Tasks) — Udemy Practice Exam

**Question**: A planning analyst has access to Claude's long-context capability and an approved calculation tool. Rather than using these unnecessarily for simple language tasks, which **two** tasks genuinely call for them?

- A. Use the calculation tool to judge which of two phrasings sounds more welcoming
- B. Use long-context analysis to synthesize an annual review from a large number of lengthy, approved source documents **(Correct)**
- C. Use the calculation tool to perform a multi-step allocation calculation involving multiple rates and quantity constraints **(Correct)**
- D. Use long-context analysis to look up a single formula within a short, approved procedure document
- E. Use long-context analysis to draft a short reply to a one-line question from a colleague

**Explanation**: B and C are tasks that genuinely need the strength of that capability (synthesizing a large volume of sources, complex multi-step calculation). A (a calculation tool is irrelevant to judging phrasing), and D and E (long context is overkill and unnecessary for a short document or a one-line question) are all examples of a mismatch between the capability and the task.

### Question 16 (Domain 6: Governance, Risk, and Responsible Use — Where Human Authority Applies) — Udemy Practice Exam

**Question**: A regional internship program team is considering how to use Claude within an approved workflow where **humans retain authority over external publication and HR decisions**. Select the **two** activities that fit Claude's "assistive" role.

- A. Draft a structured project update for human reviewers, based on approved meeting notes **(Correct)**
- B. Post the final external-facing announcement to the organization's public site once Claude's draft meets house style and the required word count
- C. Have Claude directly update internship placement records whenever it finds a better-fitting placement
- D. Send an email accepting delivery terms proposed by a vendor, on the organization's behalf
- E. Synthesize a literature review from approved, uploaded research materials, while preserving source attribution **(Correct)**

**Explanation**: A and E are purely assistive work — drafting or synthesis intended to be handed off to a human. B (executing external publication), C (executing an HR decision), and D (accepting external contract terms) all have Claude carry out actions in the areas the question explicitly reserves for human authority, which is inappropriate.

### Question 17 (Domain 7: Troubleshooting and Optimization — Diagnosing the Cause) — Udemy Practice Exam

**Question**: A weekly sales report built on a Claude Project had been running reliably for six months. This week, the finance team renamed two columns, added a region column, and replaced the file in the Project's knowledge with a new version. The report now omits the region column and mislabels two metrics. What is the most appropriate diagnosis?

- A. The Project's knowledge is still referencing last month's spreadsheet, so Claude is reading the old figures instead of the new file
- B. The model behind the Project changed after the report was built, so Claude now interprets the same, unchanged instructions differently than before
- C. Adding the region column made the report longer, so Claude is truncating later rows to stay within an output limit
- D. The Project's instructions still reference the old column names and region list, which no longer match the restructured spreadsheet **(Correct)**

**Explanation**: Since the question states explicitly that the file "was replaced with a new version," A (referencing an old file) is wrong. B (model change) and C (output limit) are also unrelated to the clues given in this scenario. Only D matches the actual cause: the data was updated, but the Project's instructions did not keep pace. This is a more concrete, specific instance of the same lesson as Question 8 (the warning against neglecting configuration).

### Question 18 (Domain 2: Evaluating and Validating Outputs — Organizing Information and Choosing an Output Format) — Udemy Practice Exam

**Question**: A program office has minutes from 80 meetings, containing decisions, owners, deadlines, open issues, and a lot of repetitive background context. Leadership needs an actionable summary to use in Monday's planning meeting. Which instruction produces the most actionable summary?

- A. Summarize all 80 sets of minutes in their original order, so nothing is lost
- B. Group the verified decisions, owners, deadlines, and open issues into separate sections, and omit the repetitive background context **(Correct)**
- C. Weave decisions, deadlines, and open issues together with the background context into a single chronological narrative
- D. List only the final decisions, since owners, deadlines, and open issues will keep changing anyway

**Explanation**: B is the model answer, directly matching Domain 2's "organize and curate information, and select the appropriate output format": organize by category and strip out noise (the repetitive background). A stays verbose and unorganized, C's narrative format is hard to act on, and D defeats the purpose by cutting the owner and deadline information that a planning meeting actually needs.

### Question 19 (Domain 6: Governance, Risk, and Responsible Use — The Correct Order of an Approval Flow) — Udemy Practice Exam

**Question**: A marketing team wants to use a newly released Claude feature on customer data. The organization's AI policy has not yet approved this feature for use with customer data, and the team is under pressure from a campaign deadline. What is the correct order of steps?

(1) Submit a request through the organization's approval process for the feature and the proposed data use
(2) Put the plan to use the feature on customer data on hold for now
(3) Execute the approved workflow, with permitted data and controls
(4) Check what the AI policy requires for new features and customer data
(5) Apply the conditions set by the approval (such as data minimization and review steps)

- A. 3, 5, 2, 1, 4
- B. 1, 3, 4, 2, 5
- C. 2, 4, 1, 5, 3 **(Correct)**
- D. 5, 4, 3, 1, 2

**Explanation**: Even under deadline pressure, the logical order is to first **(2) put the plan on hold**, then **(4) check what the policy requires → (1) submit the approval request → (5) apply the resulting conditions → (3) execute the approved workflow**. Any order that executes (3) early, or proceeds without first pausing or before approval, risks unauthorized use. This question turns Domain 6's "follow organizational AI policies and governance standards" into a concrete procedure.

### Question 20 (Domain 1: Prompting and Task Execution — Adapting Strategy to Task Type / Brainstorming) — Udemy Practice Exam

**Question**: An instructor wants fresh ideas for boosting attendance at an optional evening seminar, and plans to later judge the top candidates against a fixed venue budget and a two-hour time window. Every time they've asked Claude before, they've gotten nothing but safe, generic suggestions. What is the most appropriate prompting strategy?

- A. Ask Claude for a single, fully fleshed-out proposal complete with an implementation plan (a single detailed proposal is easier to check against the constraints)
- B. State the budget and time-window constraints up front, and have Claude generate only ideas that fit them from the start (so no time is wasted on ideas that will be rejected)
- C. Before generating ideas, have Claude research attendance statistics for evening seminars
- D. First ask broadly for ideas, then apply the budget and time-window criteria afterward to narrow down and refine them **(Correct)**

**Explanation**: The clue is the framing "we've only gotten safe, generic suggestions so far." As in B, imposing constraints up front tends to push Claude even further toward playing it safe — making the existing problem worse. D is the textbook correct pattern for Domain 1's "adapting strategy to the type of task (brainstorming)": diverge (broad idea generation) first, then converge (narrow using constraints). A (narrowing to a single proposal) removes the chance to diverge, and C is a detour unrelated to the actual issue.

### Question 21 (Domain 1: Prompting and Task Execution — Balancing Constraints and Creativity) — Udemy Practice Exam

**Question**: A marketing team needs original campaign ideas that stay within approved product claims and legally mandated required language. Their previous batch was creative but included two claims that legal rejected. Which approach best balances creativity and compliance?

- A. Ask for maximally creative ideas with no constraints in the prompt at all, and have legal rewrite whichever idea the team likes before the campaign launches
- B. Give Claude the approved claims and prohibited wording as constraints, generate multiple options, and then review them for both creativity and compliance **(Correct)**
- C. Leave brand claims out of the prompt entirely, so Claude's ideas aren't anchored to existing wording
- D. Have Claude closely imitate past approved campaigns, so every suggestion reuses language legal has already approved

**Explanation**: This may look like it contradicts Question 20 ("giving constraints up front leads to generic ideas"), but here the constraints (approved claims, prohibited wording) are **mandatory compliance requirements**, not preference-based narrowing. Stating them up front prevents a repeat of the previous "rejected by legal" outcome, while "generate multiple options → review" still leaves room for creativity — making B the best choice. A creates rework, C removes the compliance constraint itself and raises the risk of repeating the problem, and D leans too far into imitation and fails the "original" requirement.

### Question 22 (Domain 4: Workflow Integration and Solution Design — Communicating Value and Limitations) — Udemy Practice Exam

**Question**: A team is presenting a Claude-assisted research workflow to leadership, who expect immediate cost savings and error-free output. The pilot cut research time by a third, but reviewers were still correcting multiple factual errors in each report. What is the most appropriate message?

- A. Lead with the one-third time savings and projected annual cost savings, and save limitations and the need for review for a later technical briefing
- B. Explain the model's architecture and prompt design in detail, so leadership can judge the workflow's value for themselves
- C. Present the time savings alongside the fact that fact-checking review is still required, the metrics measured during the pilot, and the judgment that remains with humans **(Correct)**
- D. Explain that because the workflow uses citations from multiple independent sources plus senior reviewer review, error-free reports can be expected once it's rolled out fully

**Explanation**: C honestly conveys both the value (time savings) and the limitations (fact-checking is still needed) — exactly Domain 4's "communicate Claude's value and limitations to stakeholders." A defers disclosure of the limitation and leaves leadership's mistaken "expect error-free" assumption unaddressed. B is too technical a level of detail for a leadership audience. D promises "error-free," contradicting the fact that reviewers are actually finding errors — a classic case of overselling.

### Question 23 (Domain 5: Configuration and Knowledge Management — Organizing a Knowledge Base) — Udemy Practice Exam

**Question**: A shared Project has 70 files with vague names, duplicate procedure documents, expired policies, and versions mixed together across regions. Claude frequently retrieves incorrect guidance, and staff in two regions keep receiving answers based on the other region's rules. What is the most appropriate response?

- A. Delete obsolete and duplicate files, give the remaining files clear names, and clearly separate the authoritative version for each region **(Correct)**
- B. Upload an extra copy of the most important, up-to-date procedure document so Claude can find it more easily in each region
- C. Split the Project by region and copy all 70 files as-is into each new Project
- D. Keep every old version and regional version in the knowledge base, and add an instruction asking Claude to figure out "which is current and which region it's for"

**Explanation**: A is the most basic and fundamental fix for knowledge management: remove what's unnecessary and organize what remains. B makes things worse by adding more duplication, C simply replicates the messy file set without resolving anything, and D offloads work that properly belongs to humans onto Claude, which is unreliable.

### Question 24 (Domains 1/4: Prompting and Task Execution / Workflow Integration — Addressing the Root Cause) — Udemy Practice Exam

**Question**: An operations team spends six hours every week building a service report. Staff copy the same data into three documents, manually reconcile conflicting labels, and hand-write similar summaries — and the three versions frequently disagree. What improvement should be prioritized?

- A. Add a fourth document every week to track the differences among the other three versions
- B. Eliminate the weekly review step and have Claude generate the report directly from the raw data extract
- C. Keep all three documents as they are, and once staff finish copying and reconciling, have Claude rewrite each document so at least the summaries read consistently
- D. Standardize the source fields into a single, reviewed dataset, and have Claude generate each needed summary from that single source **(Correct)**

**Explanation**: D is about "directly eliminating the root cause (inconsistent data sources, duplicated entry)" — the same thinking as the input-standardization design in Questions 11 and 3's automation steps. A (adding a document to track differences) is a band-aid that adds more work, B (eliminating review) sacrifices quality assurance, and C (fixing only the downstream output while keeping the three-document structure) leaves the root cause of duplicated work and inconsistency in place.

### Question 25 (Domain 4: Workflow Integration and Solution Design — The Correct Procedure After a Redesign) — Udemy Practice Exam

**Question**: A team produces a monthly executive briefing by gathering updates from email, shared documents, and calendar records, and reconciling discrepancies to build presentation materials. The process is slow but important, and the team wants to redesign it using Claude. What is the correct order of steps in the redesigned workflow?

(1) Build the presentation slides with Claude
(2) Flag conflicting updates for review
(3) Gather updates from each approved source, recording the provenance of each item
(4) Distribute the presentation to leadership
(5) Have reviewers approve the reconciled facts, decisions, and open issues

- A. 3, 2, 5, 1, 4 **(Correct)**
- B. 1, 2, 4, 5, 3
- C. 5, 4, 2, 3, 1
- D. 4, 1, 3, 5, 2

**Explanation**: The logical order is "(3) gather with provenance recorded → (2) flag contradictions → (5) reviewer approval → (1) build the materials → (4) distribute" — gather → verify → human approval → produce the deliverable → distribute. Any other ordering — building materials before approval, or distributing early — contains the error of moving to the next stage before verification is complete.

### Question 26 (Domains 2/6: Evaluating and Validating Outputs / Governance — Transparency of Evidence and Accountability) — Udemy Practice Exam

**Question**: A communications team is producing a public-facing impact report with Claude, based on internal analysis. Leadership wants readers to understand the basis for each conclusion, and to know who is accountable for the content. Select the **two** most appropriate approaches.

- A. Present every conclusion with the same level of confidence, so the report reads as consistently authoritative
- B. Name a specific person as the reviewer/approver, who takes responsibility for standing behind the conclusions after publication **(Correct)**
- C. Separate citations from the body text to improve readability, and offer to provide sources on request
- D. State in the report that the analysis was generated by Claude, so readers understand the conclusions reflect the model's assessment rather than the organization's view
- E. Cite sources for key claims, and disclose significant uncertainties and limitations in the underlying evidence **(Correct)**

**Explanation**: The combination of B (a named, accountable approver) and E (cited sources plus disclosed uncertainty) directly satisfies both requirements — "understanding the basis" and "clear accountability." A flattens confidence levels and undermines transparency of basis, C removes citations from the body text, which works against "understanding the basis," and D is a classic wrong answer that shifts blame for the conclusions onto the model and evades the organization's own accountability.

### Question 27 (Domain 6: Governance, Risk, and Responsible Use — Judging the Sensitivity of PII) — Udemy Practice Exam

**Question**: A customer relations staffer is preparing routine input for Claude. Which category of data most clearly requires additional handling (deletion, redaction, or individual authorization)?

- A. A customer complaint summary that quotes the original message but replaces the customer's name with a ticket number
- B. A government-issued ID number and a full credit card number, both linked to a name **(Correct)**
- C. A list of internal staff names and extensions, for escalation purposes
- D. Aggregated regional satisfaction scores (with fewer than five respondents in some smaller regions)

**Explanation**: B is the clearest case — "a government-issued ID plus a full card number plus a link to a name" — where multiple overlapping pieces of regulated sensitive information coincide. A has already replaced the customer's name, C is low-risk internal information, and D is a more nuanced statistical disclosure risk from re-identification in small regions, which isn't quite "the clearest" case.

### Question 28 (Domain 4: Workflow Integration and Solution Design — Requirements Analysis Grounded in Real Data) — Udemy Practice Exam

**Question**: An archive wants to improve its intake process. Staff have both the current procedure and recent step-by-step completion-time data. Which Claude-assisted activity makes the best use of this evidence?

- A. Have Claude rewrite the intake procedure with clearer stage names and more concise instructions, so staff can move through each step faster
- B. Map the current process, identify likely real bottlenecks from the completion-time data, and propose targeted improvement options for human review **(Correct)**
- C. Automate whichever intake step has the cleanest input data first, so the team can get started quickly, validate it, and show leadership an early win
- D. Have Claude propose an ideal intake process based on general archival best practices, then compare it to the current procedure to close the main gaps

**Explanation**: Only B squarely matches Domain 4's "apply Claude to requirements and use-case analysis" by using the actual data on hand (the completion-time records) to identify bottlenecks. A never uses the completion-time data at all and sticks to rewording, C skips analysis in favor of ease of execution, and D relies on generic best practices rather than the organization's own specific data.

### Question 29 (Domain 2: Evaluating and Validating Outputs — Choosing an Output Format) — Udemy Practice Exam

**Question**: A business analyst needs to present metrics for 12 regions, each including a target variance, a brief explanation, and a follow-up owner. Leadership needs to review this within a 15-minute window and quickly compare across regions. Which output format is most appropriate?

- A. A narrative report that discusses each region in its own section, with explanation length left free to vary as needed for each variance
- B. One chart summarizing overall performance across all regions, with region-level detail available on request
- C. A bulleted list per region, with the metric, variance, explanation, and owner each written as one sentence per item
- D. A structured table with one row per region, and separate columns for the metric, target variance, brief explanation, and owner **(Correct)**

**Explanation**: D's table format is best suited to quickly comparing 12 regions × 4 data points side by side within 15 minutes. A's variable-length narrative is hard to compare, B pushes detail into an on-request format, which works against "quick comparison," and C's prose-style list is harder to scan than a table. This tests Domain 2's "select the appropriate output format" through a concrete example: a table is best when comparing many items on the same criteria.

### Question 30 (Domain 6: Governance, Risk, and Responsible Use — Judging an Appropriate Use Case) — Udemy Practice Exam

**Question**: A warehouse supervisor wants to use Claude to turn an approved procedure memo into a draft safety checklist. The supervisor plans to review and field-test the checklist before anyone uses it, and no personal information is involved. Is this an appropriate use of Claude?

- A. No. All workplace-safety documents should be written entirely by humans; an AI-drafted document shouldn't be allowed even with supervisor review
- B. Yes. Since the source memo is already approved, the checklist can be used as soon as Claude produces it
- C. Yes, because Claude drafts from an approved memo, and the supervisor reviews and tests it before use **(Correct)**
- D. No. Converting internal procedures into a new checklist counts as workflow design and should first be routed to a Claude Architect or Developer

**Explanation**: C is the model answer for "identifying appropriate use cases." Claude is only assisting with the draft, and a human (the supervisor) reviews and field-tests it before use — preserving the "a human retains final responsibility" design that runs through this entire certification. A takes the extreme, inappropriate position of banning AI drafting outright, B skips the crucial step of review/testing before use, and D over-escalates a task of this modest scope into Architect/Developer territory.

---

# Appendix: Key Terminology

| Term | Meaning |
|---|---|
| Hallucination | The phenomenon where an AI generates plausible-sounding but factually incorrect information |
| MQC (Minimally Qualified Candidate) | The hypothetical candidate profile used as the basis for setting the passing standard |
| Criterion-referenced assessment | An evaluation method that determines pass/fail against a fixed performance standard, rather than relative comparison with other candidates |
| PII (Personally Identifiable Information) | Information such as names, addresses, and account numbers that can identify an individual |
| Projects | A Claude feature for keeping related materials and instructions together for ongoing reference |
| Connectors | A feature that connects Claude to external tools such as Google Drive and Gmail |
| Artifacts | A Claude output format that can be saved and iteratively edited as a self-contained deliverable |
| AI Fluency (the 4 Ds) | A framework (proposed by Anthropic) for thinking about AI proficiency in terms of four components: Delegation, Description, Discernment, and Diligence |

## Common Wrong-Answer Patterns (Traps to Watch For)

- **"AI output (or its self-reported confidence) is always accurate, so verification is unnecessary"** → Wrong. Always cross-check important claims and citations against a primary source
- **"Just use the most capable model and it's always optimal"** → Wrong. Balance cost, speed, and quality to match the task
- **"It's for internal use, so personal information can be uploaded as-is"** → Wrong. It must be removed or anonymized
- **"Once configured, it never needs updating"** → Wrong. Review periodically as policies, terminology, and reference documents change
- **"Proactively fix parts that weren't flagged, out of good judgment"** → Wrong. Avoid changes beyond the scope of the review feedback, and log what you changed

---

*This is a study resource compiled from the original text of the official Anthropic exam guide (English and Japanese versions), the official Pearson VUE page, third-party articles, and sample practice questions. Exam content is scheduled to be updated starting December 7, 2026, so be sure to check the latest exam guide before taking the exam.*
