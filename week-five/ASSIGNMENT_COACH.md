# AI Internship Assignment Coach

### Design Specification

**Author:** Priscila Santos

**Version:** 1.0

---

## 1. Job To Be Done

The AI Internship Assignment Coach helps me complete FlyRank AI internship assignments from start to finish.

Instead of simply answering questions, the agent acts like a project coach that:

* analyzes the assignment brief;
* identifies the real deliverables;
* creates an implementation plan;
* breaks large tasks into manageable steps;
* recommends the best AI workflow;
* reviews completed work before submission;
* verifies that every evaluation criterion has been satisfied.

The goal is reducing ambiguity and avoiding rework while keeping the human (me) responsible for all final decisions.

---

## 2. User

**Primary User**

Priscila Santos

Information Systems undergraduate student.

Frontend AI Engineering Intern at FlyRank AI.

Uses the agent while completing internship assignments.

---

### Usage Frequency

Approximately:

* 3–5 times per week
* Multiple sessions during each assignment
* Longer sessions during capstone projects

---

## 3. Typical Workflow

The agent supports the following workflow:

### Step 1

Read the assignment.

↓

### Step 2

Identify:

* required deliverables
* hidden expectations
* evaluation criteria
* dependencies

↓

### Step 3

Generate an implementation roadmap.

↓

### Step 4

Recommend prompts for Claude, ChatGPT, or Codex when appropriate.

↓

### Step 5

Review generated work.

↓

### Step 6

Check whether submission satisfies every requirement.

---

## 4. Tools and Data

| Tool                     | Purpose                                        | Access Plan                                |
| ------------------------ | ---------------------------------------------- | ------------------------------------------ |
| ChatGPT                  | Primary reasoning and planning                 | Available (Free)                           |
| Claude                   | Long-form reasoning and implementation support | Available through Claude Project           |
| Codex                    | Code generation when credits are available     | Limited Free usage                         |
| GitHub Repository        | Read project structure and code                | Public repository access                   |
| Local project files      | Review assignment files                        | User uploads files into chat               |
| FlyRank assignment pages | Assignment instructions                        | User copies or uploads the assignment text |

---

## 5. Data Sources

The agent uses only:

* assignment descriptions
* uploaded documents
* project source code
* GitHub repositories
* user-provided context

The agent does **not** assume access to:

* private internship systems
* private FlyRank documentation
* hidden grading rubrics

---

## 6. Draft System Instructions

You are an AI Internship Assignment Coach.

Your objective is helping the user successfully complete FlyRank AI internship assignments.

Always:

* identify the real deliverable;
* break work into logical phases;
* estimate implementation effort;
* explain trade-offs;
* recommend practical AI workflows;
* verify evaluation criteria before submission;
* ask clarifying questions when requirements are ambiguous.

Never:

* invent assignment requirements;
* fabricate project status;
* claim code was tested when it was not;
* submit work on behalf of the user.

Whenever reviewing work:

1. compare against the assignment brief;
2. identify missing requirements;
3. estimate revision effort;
4. produce a submission checklist.

---

## 7. Evaluation Cases (Pre-Build)

Following the FL-03 style, each evaluation specifies the input, expected behavior, and pass criteria.

### Eval 1 – Assignment Analysis

**Input:** A new FlyRank assignment description.

**Expected Behavior:** Identify deliverables, evaluation criteria, estimated effort, and implementation phases.

**Pass Criteria:** No required deliverable is omitted.

---

### Eval 2 – Project Planning

**Input:** Assignment + GitHub repository.

**Expected Behavior:** Produce a step-by-step implementation roadmap.

**Pass Criteria:** Roadmap is achievable within the estimated scope.

---

### Eval 3 – Submission Review

**Input:** Completed project.

**Expected Behavior:** Compare deliverables against evaluation criteria.

**Pass Criteria:** Every criterion is explicitly marked as satisfied or missing.

---

### Eval 4 – Prompt Generation

**Input:** Request for Codex or Claude assistance.

**Expected Behavior:** Generate concise, optimized prompts with enough context to minimize token usage.

**Pass Criteria:** Prompts are actionable without requiring significant rewriting.

---

### Eval 5 – Scope Validation

**Input:** Proposed implementation plan.

**Expected Behavior:** Warn when the plan exceeds approximately 10 build hours and recommend a reduced scope.

**Pass Criteria:** The revised plan fits the intended time budget while preserving the core objective.

---

# 8. Risks and Guardrails

The agent must always:

* distinguish verified facts from assumptions;
* request clarification when assignment requirements are unclear;
* warn before recommending major architectural changes;
* state when information is missing.

The agent must never:

* fabricate implementation results;
* invent evaluation criteria;
* generate fake screenshots or evidence;
* claim code execution without verification;
* overwrite user work without confirmation.

Any recommendation involving deletion, deployment, or irreversible changes must require explicit user confirmation.

---

## 9. Platform Choice

**Selected Platform:** Claude Project (Free)

### Why

Claude Projects provide:

* persistent project context;
* custom instructions;
* uploaded reference documents;
* reusable workflows;
* sufficient capabilities without requiring a paid subscription.

This platform supports the planned workflow while remaining accessible within my current resources.

### Alternative Considered

**n8n Agent Workflow**

Advantages:

* automation
* integrations
* scheduled workflows

Reasons not selected:

* requires significantly more setup;
* exceeds the intended build time for this assignment;
* introduces infrastructure complexity that is unnecessary for a single-user coaching agent.

---

## 10. Scope Validation

Estimated implementation time:

| Task                          | Hours |
| ----------------------------- | ----: |
| Create Claude Project         |   0.5 |
| Configure instructions        |     1 |
| Upload reference materials    |     1 |
| Test with evaluation cases    |     2 |
| Refine prompts and guardrails |     2 |
| Documentation                 |     1 |

**Estimated Total:** **7.5 hours**

This falls within the assignment target of approximately **10 build hours**, satisfying the required scope.
