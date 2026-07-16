# Prompting Fundamentals on Real Tasks v2

## Task

**FL-01 Target Task 3: Study University Subjects**

**Classification:** Collaborate with AI

**Definition of Success:**

> Understand each topic well enough to complete assessments independently and achieve strong academic performance.

---

## V0 — Naive Prompt

```text
Explain databases.
```

**Typical Output:**

> A general explanation of databases.

**Observed Improvement:**

None (baseline).

---

## V1 — Role Assignment

```text
You are a university professor teaching Information Systems.

Explain databases.
```

**Observed Improvement:**

- The explanation becomes more instructional and easier to follow.
- The language is more appropriate for a university-level audience.

---

## V2 — Context and Motivation

```text
You are a university professor teaching Information Systems.

I'm studying for a university exam in Information Systems.

My goal is to understand the concepts well enough to solve problems independently instead of memorizing definitions.

Explain relational databases.
```

**Observed Improvement:**

- The response shifts from a generic encyclopedia-style definition to a lesson designed for learning.
- The explanation becomes more relevant to my learning objective.

---

## V3 — Few-shot Examples

```text
When explaining a concept:

Concept

Definition

Simple example

Common mistake

Practice question

Now explain relational databases.
```

**Observed Improvement:**

- The explanation becomes much easier to study.
- Concrete examples help reinforce understanding.
- The practice question encourages active learning.

---

## V4 — Output Structure

```text
Use exactly these sections:

Definition

Why it matters

Example

Common mistakes

Practice exercise

Summary
```

**Observed Improvement:**

- The response becomes organized like study material.
- The consistent structure makes the content easier to review before an exam.

---

## V5 — Step Decomposition

```text
Before answering:

Step 1
Identify the prerequisite concepts.

Step 2
Explain each prerequisite.

Step 3
Teach the main concept.

Step 4
Create a practice question.

Step 5
Explain the correct answer.
```

**Observed Improvement:**

- The response becomes much more similar to a real tutoring session.
- Building prerequisite knowledge first makes the main concept easier to understand.
- The practice question and explanation help verify whether I truly learned the topic rather than simply reading the answer.

---

# Claude vs. ChatGPT Comparison

| Aspect | Claude | ChatGPT |
|--------|---------|----------|
| **Tone** | More like a professor giving a concise explanation. | More like a tutor teaching the topic step by step. |
| **Depth** | Focuses on the essential concepts with minimal extra detail. | Provides additional background, examples, and conceptual explanations. |
| **Structure** | Closely follows the requested steps while keeping each section concise. | Also follows the requested steps but expands them into a more complete study guide. |
| **Practice Question** | Presents an open-ended question that encourages analytical thinking. | Presents a multiple-choice question and encourages the student to reason before revealing the answer. |
| **Explanation Quality** | Emphasizes intuition and helps the learner understand *why* the complexity grows the way it does. | Connects the mathematical reasoning to the Big-O result with more detailed explanations, making the derivation easier to follow. |
| **Potential Weakness** | Assumes slightly more prior mathematical knowledge from the student. | Can be more verbose than necessary for students who already understand the prerequisites. |

### Overall Comparison

For this educational task, Claude produced a concise, lecture-style explanation that is well suited for learners who want a quick conceptual overview. ChatGPT generated a more detailed, tutorial-style explanation with additional context, examples, and guided reasoning. Both models followed the prompt effectively, but they supported learning in different ways: Claude prioritized brevity and intuition, while ChatGPT emphasized reinforcement through structured explanations and practice.
