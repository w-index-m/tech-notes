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
- **A specific objective**: make clear what you want to achieve
- **Context**: provide background information, constraints, the target audience, etc.
- **Specifying the output format**: specify bullet points, tables, length, tone, etc.
- **Examples (few-shot)**: showing one or two examples close to the desired output improves accuracy

### 1-2. Task Decomposition
Complex requests tend to produce more accurate results when broken down step by step rather than handed over all at once. A real exam example (from a user-provided practice question): the correct order for decomposing a request to "create a customer journey map" is ① define the persona and mapping scope → ② identify the main stages → ③ identify the touchpoints at each stage → ④ extract pain points and opportunities → ⑤ integrate everything into the final deliverable — that is, an order that moves **from the broad to the detailed, and from analysis to synthesis**.

### 1-3. Iterative Refinement
When output gradually drifts further from the requested template, the systematic approach is **not to keep making large changes hoping to stumble on a match (a poor approach), but to go back to the version that came closest to the request and refine from that baseline** (confirmed via a user-provided practice question).

### 1-4. Adapting Strategy to Task Type
- Analysis: centers on organizing and evaluating data/information
- Research: centers on synthesizing information across multiple sources
- Drafting (writing): centers on adjusting tone and phrasing for the audience
- Brainstorming: centers on generating a divergent list of options

---

# Domain 2: Evaluating and Validating Outputs (Weight: 21% — highest)

## Official Objectives (from the Exam Guide)
- Evaluate the accuracy and completeness of Claude-generated outputs
- Identify hallucinations, inconsistencies, and bias in responses
- Apply fact-checking and verification techniques
- Determine when human review or additional verification is needed
- Edit, adapt, refine, and compare outputs for the intended audience
- Organize and curate information, and select the appropriate output format (artifacts, inline, structured data)

## Commentary

### 2-1. Detecting Hallucinations and Fact-Checking
As shown in the official sample question (below), Claude can **plausibly fabricate specific-looking citation numbers and figures**. Verifiable claims (numbers, section/article numbers, proper nouns, dates) should be checked against primary sources, and the higher the stakes, the more important this is. **Having Claude self-report its own "confidence" is not a reliable indicator of accuracy** (per the official explanation).

### 2-2. Identifying When Human Review Is Needed
- The higher the risk (contracts, regulatory matters, healthcare, legal matters, external publication), the more essential human review becomes
- A real exam example: a case involving "an unedited draft for a paying customer that includes an SLA (service-level) commitment" is treated as the high-impact situation most in need of escalation and review (from a user-provided practice question)

### 2-3. Editing, Adapting for Audience, and Comparing Outputs
Even with the same content, adjust the phrasing and level of detail depending on the target audience (e.g., executives vs. frontline staff). Compare multiple outputs to judge which is closer to the request.

### 2-4. Choosing an Output Format
- **Artifacts**: when you want to save and keep editing something as a standalone deliverable
- **Inline**: for short answers that are complete within the flow of the conversation
- **Structured data**: when downstream processing (e.g., ingestion into another system) is required

---

# Domain 3: Product and Model Selection (Weight: 12%)

## Official Objectives (from the Exam Guide)
- Select the appropriate Claude product features (Projects, Research, Chat, Artifacts)
- Distinguish between Claude's model types (Haiku, Sonnet, Opus)
- Match model selection to task requirements (cost, speed, quality)
- Understand and manage context limitations and memory considerations (when to start over, summarize, or retain context)

## Commentary

### 3-1. Choosing Among the Model Lineup
- **Haiku family**: fast and low-cost. Suited to simple tasks and high-volume processing
- **Sonnet family**: balanced
- **Opus family**: high-performance, high-cost. Suited to tasks requiring complex reasoning

As shown in the official sample question, for "drafting a high volume of short customer replies, where speed and cost matter more than deep reasoning," **using a fast, low-cost model is the correct answer**; "always use the top-tier model" is incorrect, as it wastes cost and latency budget.

### 3-2. Choosing Product Features
| Product feature | Best suited for |
|---|---|
| Chat (single interaction) | One-off questions, thinking something through |
| Projects | Ongoing projects, or when you want to keep reference materials together |
| Research | Research tasks spanning multiple sources |
| Artifacts | When you want to save and iteratively edit something as a standalone deliverable |

### 3-3. Managing Context Limitations and Memory
When a conversation has grown too long, judge whether it is appropriate to "start over in a new conversation," "summarize and carry the summary forward," or "retain it as project knowledge."

---

# Domain 4: Workflow Integration and Solution Design (Weight: 16%)

## Official Objectives (from the Exam Guide)
- Apply Claude to requirements and use-case analysis
- Leverage Claude for research, planning, and process optimization
- Use Claude to support solution design, development, and iterative improvement
- Integrate Claude into existing workflows, strengthening or redesigning those workflows
- Communicate Claude's value and limitations to stakeholders

## Commentary

### 4-1. Patterns for Embedding Claude into Business Processes
One-off use (ad hoc chat), Projects (ongoing materials and context retention), connectors (integration with external tools, see Domain 5), and automating/templating routine work.

### 4-2. Communicating Value and Limitations
Part of good design is accurately communicating to stakeholders "what Claude can and cannot do" when introducing it. Avoid both overselling it (being mistaken for an all-purpose tool) and underselling it (being mistaken for a mere chatbot).

### 4-3. Workflow Redesign vs. Simple Augmentation
The exam also tests the perspective of **redesigning the process itself around Claude's capabilities**, rather than simply bolting Claude onto an existing process unchanged (for example, reordering an approval flow or repositioning human review checkpoints).

---

# Domain 5: Configuration and Knowledge Management (Weight: 12%)

## Official Objectives (from the Exam Guide)
- Configure Claude Projects using instructions and knowledge sources
- Manage uploaded knowledge and connectors (Google Drive, Gmail, etc.)
- Write effective system-level instructions
- Reflect, maintain, and update information in Claude's configuration, knowledge sources, and instructions

## Commentary

### 5-1. Configuring Projects
Set project-specific instructions (custom instructions) to convey the desired tone, format, and background knowledge up front. Upload relevant materials as knowledge sources so you don't have to repeat the same explanation every time.

### 5-2. Managing Connectors
Connect to external tools such as Google Drive and Gmail to work against real data. Connection scope and permissions should also be managed appropriately from a governance perspective (Domain 6).

### 5-3. Ongoing Maintenance of Configuration (a key exam theme)
A real exam example: the habit of never updating project configuration because "once it works, it works forever" is inappropriate. **Even if output quality has not visibly degraded, configuration should be reviewed periodically as policies, terminology, and reference documents change** (from a user-provided practice question). Neither mechanically updating on a fixed schedule nor reacting only after quality has degraded is correct — the correct pattern is **periodic review driven by the actual pace of change**.

---

# Domain 6: Governance, Risk, and Responsible Use (Weight: 15%)

## Official Objectives (from the Exam Guide)
- Identify appropriate and inappropriate use cases
- Apply data sensitivity, regulatory, and privacy considerations
- Follow organizational AI policies and governance standards
- Understand the ethical implications of AI use

## Commentary

### 6-1. Handling PII and Sensitive Data
As shown in the official sample question, when you want Claude to analyze a spreadsheet containing regulated personal data such as customer names and account numbers, **the correct action, per policy, is to remove or anonymize personally identifying information before uploading it**. "Uploading it as-is because it's for internal use only" and "merely instructing Claude not to retain it" are both insufficient, and "abandoning the analysis altogether" is unnecessary since anonymization makes the analysis possible.

The same logic recurs in real exam examples (from user-provided practice questions):
- ✅ An anonymized HR policy summary, a redacted job posting → appropriate
- ❌ Employee ID and start date, name plus salary, an unredacted accommodation-request note → inappropriate (unredacted personal information)

### 6-2. Organizational Governance
Document and communicate usage policies and guidelines within the organization. Define who may use Claude, for what work, and to what extent.

### 6-3. The AI Fluency Framework (the "4 Ds," for reference)
Anthropic's proposed framework of four elements for using AI well. It is not explicitly listed among the exam domains, but it is a related concept worth knowing:

| Element | Description |
|---|---|
| **Delegation** | Deciding which tasks to hand to AI and which tasks humans should own |
| **Description** | Giving AI clear, specific instructions and context |
| **Discernment** | Critically evaluating and verifying AI output (directly tied to Domain 2) |
| **Diligence** | Taking personal responsibility for AI use, with attention to ethics and compliance |

---

# Domain 7: Troubleshooting and Optimization (Weight: 10%)

## Official Objectives (from the Exam Guide)
- Identify, diagnose, and resolve issues with prompts that aren't working as expected or outputs of poor quality
- Adjust approach based on feedback and results
- Optimize workflows for greater efficiency and effectiveness

## Commentary

### 7-1. Basic Patterns for Isolating Causes
| Symptom | Likely cause | Fix |
|---|---|---|
| Output misses the mark | Insufficient instructions/context | Add the goal and background information |
| Output too long/too short | No output format specified | Specify length, number of bullet points, etc. |
| Output varies every time | Prompt is ambiguous | Switch to more specific instructions |

### 7-2. Scope of Response to Review Feedback (a real exam example)
When a reviewer gives exactly two specific pieces of feedback, the appropriate response is to **address only those two points, leave unrelated parts unchanged, and record the changes for the next review** (from a user-provided practice question). Changing parts that weren't flagged on your own initiative, rewriting from scratch, or adding improvements beyond what was flagged are all incorrect patterns, as they exceed scope.

### 7-3. Designing Reliable Automation Steps (a real exam example)
When automating a routine data-processing step via code execution, the appropriate design choices for reliable integration are:
- **Standardize the input format** so that output is consistent and verifiable every time
- Add a **concise output-validation step** so a human reviewer can sanity-check the output against expected ranges before it moves to the next stage

Conversely, "varying the input format every time," "eliminating human review," and "embedding the output format without documenting it" are all incorrect patterns that undermine reliability.

---

# Exam Tip: The "Verb Pattern" Hint

A useful observation from an exam-experience article (Qiita) the user collected, as a practical tip for reading English exam questions:

- **Verbs that tend to appear in correct answers**: verify, escalate, flag, anonymize/redact — verbs that mean "check, then proceed in steps"
- **Verbs that tend to appear in incorrect answers**: send as-is, assume, skip — verbs that mean "proceed without checking"

This is consistent with the correct-answer pattern across all three official sample questions (cross-check and verify / choose the appropriate model / remove or anonymize), and it is also consistent with the correct-answer pattern across the unofficial practice questions collected for this guide. It's a useful tiebreaker when you're unsure.

---

# Exam Policies (from the Official Exam Guide + the Official Pearson VUE Page)

## Registration and Scheduling Process
1. Access the certification page for the exam you intend to take on the Anthropic Partner Academy and review the exam details
2. Before registering, download the exam guide and review the "Certification Terms of Use" and "Certification Exam Policies"
3. Register for the exam and complete payment (discounts reflect your partner tier)
4. Follow the confirmation instructions to create a Pearson VUE account, sign in, and schedule your exam session
5. Choose an available date and select either online proctoring or a Pearson testing center
6. **Rescheduling/cancellation**: the official exam guide states "free of charge up to 24 hours before the scheduled time," while the official Pearson VUE page states "up to 48 hours before" — there is a slight discrepancy. To be safe, reschedule or cancel at least 48 hours in advance. Changes made after the deadline are not refunded

## Eligibility
Open only to **individuals affiliated with a Claude Partner Network (CPN) member company** (registration requires a company email address). This is not currently a certification open to the general public.

## Identity Verification
On exam day, you must present a valid, unexpired, government-issued photo ID. The name must exactly match the name used at registration (to correct a mismatch in advance, contact `certifications-support@anthropic.com`).

## Retake Policy
If you fail, you may retake the exam after a waiting period of **14 days after the first attempt, 30 days after the second attempt, and 90 days after the third attempt**. You may take the exam **up to 4 times within any rolling 12-month period** (the limit applies per exam). The exam fee is charged each time you take the exam.

## Exam-Day Rules
- For online exams, you must remain within view of the proctor/webcam at all times during the session
- No notes, books, phones, secondary monitors, etc. are allowed in your workspace
- Communicating or speaking with others during the exam is prohibited, as is recording, copying, photographing, or reproducing exam content in any way
- Prohibited items: cell phones, smartwatches, headphones, study materials, and any recording device
- Misconduct (cheating, attempting to access prohibited resources, disclosing exam content) can result in invalidated results, revocation of the certification, and a ban on future testing

## Confidentiality (Non-Disclosure Agreement)
Agreement to confidentiality and non-disclosure terms is required before the exam begins. If you do not agree, the exam session will be terminated with no refund.

## Maintaining and Renewing the Certification
- The certification is valid for **12 months** from the date it is awarded
- Renewal within the validity period is completed via a **free, unproctored assessment** on the Anthropic Partner Academy (no cost)
- If the certification lapses, you must **retake the full exam** at the standard fee
- If exam content has changed significantly, Anthropic may require a full retake rather than a renewal assessment

## OnVUE Online Exam Network Requirements
If taking the exam from a corporate network, connectivity to the following domains (and their subdomains) is required (partial list): `*.pearsonvue.com`, `*.onvue.com`, `*.pearson.com`, `*.certiport.com`, `*.twilio.com`, `*.wowza.com`, and others. It is recommended to check with your network administrator in advance.

---

# Differences from Other Claude Certifications (reference: from third-party articles)

At the launch of the Claude Partner Network in March 2026, CCAR-F was the only Claude certification available; the lineup expanded to four certifications starting in July 2026.

| Certification | Exam code | Fee | Questions | Primary audience |
|---|---|---|---|---|
| Claude Certified Associate – Foundations | CCAO-F | $99 | 60 | Business roles using Claude in their work (this guide) |
| Claude Certified Developer – Foundations | CCDV-F | $125 | 53 | Engineers building with the Claude API and Agent SDK |
| Claude Certified Architect – Foundations | CCAR-F | $125 | 60 | Architects designing production solutions (corresponds to `cca-f-study-guide.md` in this repository) |
| Claude Certified Architect – Professional | CCAR-P | $175 | 63 | Mid-level to senior architects and tech leads |

The basic rule of thumb is to choose based on your role: CCAO-F if your work does not assume coding, CCDV-F if you're responsible for development using the Claude API and MCP. Note also that **CCAO-F does not count toward Anthropic's Partner Network tier requirements** (per third-party sources, it is positioned as a certification intended to validate individual skill, not partner-tier status).

---

# Official Sample Questions (3 total, from Exam Guide Section 8)

> The following are example questions published in Anthropic's official exam guide to illustrate question format and difficulty (it is explicitly noted that these are not excerpts from the actual item pool).

### Sample 1 · Domain 2: Evaluating and Validating Outputs

**Question**: An associate asked Claude to summarize a new regulation, and Claude produced a confident summary citing specific section numbers. What is the most appropriate action to take before sending this summary to the compliance team?

- A. Send it as-is, since Claude expressed high confidence.
- B. Cross-check the cited sections against the official regulatory text before sharing. **(Correct)**
- C. Have Claude rate its own confidence, and send it if the rating is high.
- D. Rewrite the summary to sound more formal before sending it.

**Explanation**: Language models can fabricate specific-looking details, such as citation numbers — this is hallucination. The required step is to verify factual claims, especially citations intended for compliance staff, against a trustworthy source. Self-reported confidence (A, C) is not a reliable indicator of accuracy, and changing the tone (D) does not address the correctness problem.

### Sample 2 · Domain 3: Product and Model Selection

**Question**: An associate needs to generate a high volume of short customer-reply drafts, where speed and cost matter more than deep reasoning. What is the best choice for this task?

- A. Use the most capable, most expensive model for every reply to maximize quality.
- B. Use a faster, lower-cost model suited to simple, high-volume tasks. **(Correct)**
- C. Disable all product features to reduce cost.
- D. Switch to a different AI platform.

**Explanation**: Matching model selection to task requirements means assigning faster, lower-cost models to simple, high-volume work and reserving the most capable models for complex reasoning. Always using the top-tier model (A) wastes cost and latency budget. Disabling features (C) or switching platforms (D) does not address this trade-off.

### Sample 3 · Domain 6: Governance, Risk, and Responsible Use

**Question**: A project manager wants to upload a spreadsheet containing customer names and account numbers so Claude can analyze trends. The organization's policy restricts the sharing of regulated personal data. What is the most appropriate action?

- A. Upload the file as-is, since the analysis is for internal use only.
- B. Remove or anonymize personally identifying information before uploading, in accordance with policy. **(Correct)**
- C. Upload the file but instruct Claude not to retain it.
- D. Abandon the analysis altogether.

**Explanation**: Applying data sensitivity and privacy safeguards means removing or anonymizing regulated identifying information before use, which allows the analysis to proceed without exposing protected data. Uploading it as-is (A) violates policy. Instructing the model not to retain the data (C) does not satisfy the policy's control requirements. Since anonymization makes the analysis possible, abandoning the task (D) is unnecessary.

---

# Appendix: Key Terminology

| Term | Meaning |
|---|---|
| Hallucination | A phenomenon where an AI plausibly generates information that is factually incorrect |
| MQC (Minimally Qualified Candidate) | A hypothetical candidate profile used as the baseline for setting the passing standard |
| Criterion-referenced assessment | An assessment method that determines pass/fail against a fixed performance standard, rather than by comparing candidates against one another |
| PII (Personally Identifiable Information) | Information such as name, address, and account number that can identify an individual |
| Projects | A Claude feature that lets you keep related materials and instructions together for ongoing reference |
| Connectors | A feature that connects Claude to external tools such as Google Drive and Gmail |
| Artifacts | A Claude output format that can be saved and iteratively edited as a standalone deliverable |
| AI Fluency (the "4 Ds") | A framework (proposed by Anthropic) for thinking about AI proficiency in terms of four elements: Delegation, Description, Discernment, and Diligence |

## Common Incorrect-Answer Patterns (distractors)

- **"AI output (or its self-reported confidence) is always accurate, so verification is unnecessary"** → Incorrect. Important claims and citations must always be checked against primary sources
- **"Just use the most capable model and it's always optimal"** → Incorrect. Balance cost, speed, and quality against the task's actual requirements
- **"It's fine to upload personal data as-is because it's for internal use"** → Incorrect. Removal/anonymization is required
- **"Once configured, no updates are needed"** → Incorrect. Review periodically as policies, terminology, and reference documents change
- **"Proactively fix parts that weren't flagged, out of helpfulness"** → Incorrect. Avoid changes beyond the scope of the review feedback, and record what was changed

---

# Practice Questions (original to this guide, unofficial)

> ⚠️ In addition to the 3 official sample questions above, the following are **original practice questions** created for this guide, along with content organized from practice questions found on unofficial sites. These are not actual Anthropic exam questions.

### Practice Question 1 (Domain 2: Evaluating and Validating Outputs)

**Question**: A marketing staffer had Claude produce a report containing competitors' market-share figures. Before using this report as-is in external-facing materials, what should be prioritized?

- A. Use it as-is, since Claude's output is sufficiently reliable
- B. Cross-check the specific figures in the report (such as market share) against primary sources **(Correct)**
- C. Regenerate it with a more capable model, which removes the need for verification
- D. It is enough to check only that the writing is readable

**Explanation**: Verifiable specific figures carry hallucination risk, so cross-checking against primary sources is the top priority. The same reasoning as Official Sample Question 1.

### Practice Question 2 (Domain 1: Prompting and Task Execution — Task Decomposition)

**Question**: What is the correct order for decomposing the request "create a customer journey map"?
(1) Identify the touchpoints at each stage (2) Define the persona and mapping scope (3) Identify the pain points and opportunities at each touchpoint (4) Integrate the results into the final deliverable (5) Identify the main stages the persona moves through

- A. 2, 5, 1, 3, 4 **(Correct)**
- B. 2, 1, 5, 3, 4
- C. 5, 2, 1, 3, 4
- D. 4, 3, 2, 1, 5

**Explanation**: The correct order moves from the broad to the detailed: scope definition → broad strokes (stages) → detail (touchpoints) → analysis (identifying pain points) → integration.

### Practice Question 3 (Domain 7: Troubleshooting and Optimization — Designing an Automation Step)

**Question**: An operations staffer is redesigning a step in a routine data-processing workflow using code execution. Which **two** design choices best support reliable integration?

- A. Standardize the input file format the step expects, so that output is consistent and verifiable every time **(Correct)**
- B. Vary the input file format on each run to accommodate a different source system each week
- C. Remove the human review step after code execution, since the sandbox environment has already verified error-free execution
- D. Build the code-execution step into the workflow without documenting the expected output format or fields
- E. Define a concise output-validation step in which a human reviewer checks the output against the expected range and sanity before it moves downstream **(Correct)**

**Explanation**: Standardizing input (A) combined with a human validity-check step (E) is the basis of reliable automation integration. B (inconsistent formats), C (removing human review), and D (undocumented) all undermine reliability.

### Practice Question 4 (Domain 6: Governance, Risk, and Responsible Use — Data to Include in Prompts)

**Question**: An HR staffer is deciding what data to include in a Claude prompt for a routine task. Which **two** data items are most appropriate to include? (Each correct answer stands on its own)

- A. Employee ID numbers and start dates from a team roster, to help personalize onboarding communications
- B. A compensation-band summary for a specific team, including employee names and current salary amounts
- C. An anonymized summary of HR policy, for the model to paraphrase **(Correct)**
- D. A note on an employee's accommodation request, taken from their HR file, needed to draft a role-adjustment memo
- E. A redacted job description, for the model to tailor for a job posting **(Correct)**

**Explanation**: C and E both include content in the prompt only after personally identifying information has been removed or anonymized. A (employee ID + start date), B (name + salary), and D (an unredacted, sensitive accommodation note) all contain raw personal information and are inappropriate.

### Practice Question 5 (Domain 1: Prompting and Task Execution — Iterative Refinement)

**Question**: After several rounds of refining a prompt, each additional change pushes the output further from the requested template. What is the most appropriate next step?

- A. Stop iterating and accept the version that has drifted the most
- B. Discard all previous versions and start over from scratch, without keeping a record of what worked
- C. Keep making large changes simultaneously until the output happens to match by chance
- D. Revert to the prompt version that produced the closest result, and refine from that baseline **(Correct)**

**Explanation**: Returning to the best known state and then making small changes from there is the basic stance of systematic debugging.

### Practice Question 6 (Domain 7: Troubleshooting and Optimization — Scope of Review Response)

**Question**: A staffer is addressing a reviewer's feedback on a draft document Claude produced, consisting of exactly two specific points. The rest of the document has already been approved as-is. Which next step is most likely to produce a strong revision?

- A. Address the two flagged points, and also fix unrelated parts to preserve a consistent tone after editing
- B. Address the two flagged points, but also rewrite the document from scratch, reasoning that it would be more cohesive
- C. Address the two flagged points directly, leave unrelated parts unchanged, and record the changes for the next review **(Correct)**
- D. Address the two flagged points, plus a third improvement the staffer noticed on their own

**Explanation**: Strictly following the scope of the review feedback and recording the change history is appropriate. A and D exceed scope, and B is an inefficient full rewrite.

### Practice Question 7 (Domain 2: Evaluating and Validating Outputs — Escalation Judgment)

**Question**: A customer success manager is deciding whether a Claude-generated response should be escalated before external use. Which factor most strongly supports escalation?

- A. It is an unedited draft for a paying customer that includes a service-level commitment **(Correct)**
- B. It is a brief internal announcement about an upcoming all-team meeting
- C. It is an internal draft summarizing notes from a customer call, for the staffer's own reference
- D. It is a casual rephrasing of an internal status update

**Explanation**: The combination of being external, for a paying customer, carrying contractual implications (an SLA), and unedited represents the highest impact (risk), warranting escalation. B, C, and D are all internal, low-risk contexts.

### Practice Question 8 (Domain 5: Configuration and Knowledge Management — Timing of Configuration Updates)

**Question**: A senior manager is reconsidering a colleague's habit of never updating the team's project configuration, on the reasoning that "once it works, it works forever." What is the most appropriate feedback?

- A. If the team is satisfied with current output, the configuration can stay as-is, and any needed updates can be handled at the chat level
- B. The configuration should be updated on a regular, fixed schedule regardless of whether the underlying policies or reference documents have changed
- C. Since proactive updates risk introducing bugs into a working configuration, updates should happen only when output quality has visibly degraded
- D. Since policies, terminology, and reference documents change over time, the configuration should be reviewed and updated periodically even without visible degradation in output quality **(Correct)**

**Explanation**: D is the most appropriate, because it ties the need for updates to actual change (in policy, terminology, reference documents) rather than to "visible degradation." A leaves the problem unaddressed, C only reacts after the fact, and B is an inefficient, mechanical schedule unrelated to whether change has actually occurred.

---

*This guide is a study resource compiled from the original text of Anthropic's official exam guide (English and Japanese versions), the official Pearson VUE page, third-party articles, and sample practice questions. Because exam content is scheduled to be updated starting December 7, 2026, be sure to check the latest version of the exam guide before taking the exam.*
