# Workflow Overview

## Name

**Source-Grounded Study Notes Pipeline**

### Purpose

Transform study materials (PDFs, articles, documentation) into structured study notes with summaries, quiz questions, and revision suggestions.

This is a realistic workflow you'll actually be able to reuse during university.

---

## Workflow Diagram

```text
          Input
             │
             ▼
 Upload PDF/article to NotebookLM
             │
             ▼
 NotebookLM creates grounded summary
             │
             ▼
 Copy summary into ChatGPT
             │
             ▼
 ChatGPT creates study notes
             │
             ▼
 ChatGPT critiques notes
             │
             ▼
 ChatGPT produces final revision sheet
             │
             ▼
          Final Output
```

This already satisfies the requirement of **3+ distinct steps**.

---

## Step 1 — Gather

Tool:

NotebookLM

Input:

* PDF
* Lecture slides
* Documentation
* Research paper

Prompt

```
Read only the uploaded sources.

Create a structured summary containing:

- Key concepts
- Important definitions
- Main arguments
- Practical examples
- Any terminology I should memorize

Do not invent information.
Only use information from the uploaded sources.
```

Output

Grounded summary.

---

## Step 2 — Draft

Tool

ChatGPT

Prompt

```
Using the NotebookLM summary below, create study notes.

Organize them into:

# Overview

# Key Concepts

# Important Definitions

# Examples

# Things to Remember

# Common Mistakes

# 10 Quiz Questions

Use markdown formatting.
```

Output

Well-formatted study guide.

---

## Step 3 — Review

Prompt

```
Review these study notes.

Identify:

- Missing information
- Ambiguous explanations
- Repeated content
- Concepts that need clarification

Then rewrite the notes to improve clarity without adding unsupported information.
```

Output

Improved notes.

---

## Step 4 — Format

Prompt

```
Convert the revised notes into a one-page revision sheet.

Requirements:

- concise
- bullet points
- exam-focused
- keep only essential information
```

Output

Final cheat sheet.

---

## Handoffs

| Step       | Output           | Next Step Uses    |
| ---------- | ---------------- | ----------------- |
| NotebookLM | grounded summary | ChatGPT Draft     |
| Draft      | study notes      | Review            |
| Review     | improved notes   | Formatting        |
| Formatting | revision sheet   | Final deliverable |

---

## Five Test Runs

Choose five different real sources.

For example:

### Run 1

Information Systems lecture PDF

Output

Study guide

Time

12 minutes

---

### Run 2

React documentation page

Output

React Hooks notes

Time

11 minutes

---

### Run 3

Spring Boot tutorial

Output

REST API notes

Time

10 minutes

---

### Run 4

AI Engineering article

Output

Workflow notes

Time

9 minutes

---

### Run 5

Database normalization chapter

Output

Normalization cheat sheet

Time

11 minutes

---

## Time Comparison

Example numbers (replace them with your actual timings if you measure them):

| Task           | Manual     | Workflow   |
| -------------- | ---------- | ---------- |
| Read material  | 25 min     | 8 min      |
| Summarize      | 20 min     | 3 min      |
| Organize notes | 15 min     | 3 min      |
| Create quiz    | 10 min     | 1 min      |
| Review         | 10 min     | 2 min      |
| **Total**      | **80 min** | **17 min** |

Setup time:

```
NotebookLM setup: 20 minutes (one-time)

Workflow execution:

17 minutes

Estimated savings after setup:

63 minutes per study session
```

Being honest about the one-time setup cost is explicitly part of the grading rubric.

---

## Failure Points

Mention these in your report:

### NotebookLM

* Only analyzes uploaded sources.
* Cannot answer questions beyond the provided material.

### ChatGPT

* May simplify technical concepts too much.
* Can introduce formatting inconsistencies.
* May overgeneralize if the NotebookLM summary lacks detail.

### Human Review Required

* Verify technical accuracy.
* Ensure no important concepts were omitted.
* Confirm terminology matches the original source.
* Check quiz questions are relevant.
* Proofread formatting before sharing or studying.

---

## Deliverable Structure

The walkthrough document can follow this outline:

```text
Title

Objective

Workflow Diagram

Tools Used

Step 1
Prompt
Output

Step 2
Prompt
Output

Step 3
Prompt
Output

Step 4
Prompt
Output

Five Workflow Runs

Time Comparison

Failure Points

Human Review

Conclusion
```
