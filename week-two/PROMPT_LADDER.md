# Prompt Ladder

### Track

AI Fluency – Week 2

### Goal

Improve a prompt for generating portfolio case studies through iterative prompt engineering.

---

## Baseline (Weak Prompt)

### Prompt

```text
Help me write a case study for my project.
```

### Representative Output

> This project demonstrates my technical skills and passion for software development. I worked hard to build a functional application using modern technologies and learned valuable lessons throughout the process.

### Notes

**What changed in the prompt**

None (baseline).

**What improved in the output**

Nothing to compare yet.

**What still failed**

The response is generic, could describe almost any project, and doesn't explain my decisions or outcomes.

**What I would try next**

Add a clearer goal.

---

## Version 1 — Layer: Clearer Goal

### Prompt

```text
Help me write a portfolio case study that explains the problem, what I did, and the outcome of my project.
```

### Representative Output

> Problem...
>
> What I Did...
>
> Outcome...

### Notes

**What changed in the prompt**

Added a clear goal.

**What improved in the output**

The response now follows a logical structure instead of being a generic paragraph.

**What still failed**

The content is still generic because the AI doesn't know anything about the project.

**What I would try next**

Add real project context.

---

## Version 2 — Layer: Real Context

### Prompt

```text
Help me write a portfolio case study.

The project is an Academic Planner built with React for my Algorithms Project I course.

Structure it as Problem, What I Did, and Outcome.
```

### Representative Output

> The project was created to help students organize assignments...

### Notes

**What changed in the prompt**

Added project context.

**What improved in the output**

The case study became specific to my project instead of describing a generic application.

**What still failed**

The writing still sounds like AI marketing copy rather than something I would actually say.

**What I would try next**

Define the audience.

---

## Version 3 — Layer: Audience

### Prompt

```text
Help me write a portfolio case study.

The audience is a frontend engineering manager hiring AI-assisted frontend developers.

The project is an Academic Planner built with React for my Algorithms Project I course.

Use Problem, What I Did, Outcome.
```

### Representative Output

> The project demonstrates frontend engineering skills through...

### Notes

**What changed in the prompt**

Added a specific audience.

**What improved in the output**

The explanation focuses more on engineering decisions instead of explaining basic web development concepts.

**What still failed**

The tone is still overly polished and contains some buzzwords.

**What I would try next**

Add writing constraints.

---

## Version 4 — Layer: Constraints

### Prompt

```text
Help me write a portfolio case study.

Audience:
Frontend engineering manager.

Project:
Academic Planner built with React.

Structure:
Problem
What I Did
Outcome

Constraints:
Avoid buzzwords.
Do not exaggerate.
Keep the writing evidence-based.
Do not invent metrics.
```

### Representative Output

> I built the application to help students organize academic work...

### Notes

**What changed in the prompt**

Added explicit writing constraints.

**What improved in the output**

The writing became more factual and believable, with fewer clichés.

**What still failed**

The wording still doesn't consistently reflect my own voice.

**What I would try next**

Provide quality criteria.

---

## Version 5 — Layer: Quality Criteria

### Prompt

```text
Help me write a portfolio case study.

Audience:
Frontend engineering manager.

Project:
Academic Planner built with React for my Algorithms Project I course.

Structure:
Problem
What I Did
Outcome

Constraints:
Avoid buzzwords.
Don't invent achievements.
Use evidence instead of claims.

Quality Criteria:
The case should sound like a real junior engineer explaining genuine decisions.
Every sentence should be something I could confidently say in an interview.
```

### Representative Output

> I built this project to create a single place for students to organize assignments and deadlines while improving my React development skills. Throughout the project I focused on reusable components, responsive layouts, and organizing the interface around the user's workflow rather than technical features.

### Notes

**What changed in the prompt**

Added quality criteria.

**What improved in the output**

The response became more natural, more specific, and closer to how I actually describe my work.

**What still failed**

Some sentences were still longer than I would normally write, so I would edit them manually.

**What I would try next**

Review the draft aloud and simplify any wording that doesn't sound like me.

---

## Honest "This Didn't Help" Moment

Adding the **audience** improved the technical focus, but it didn't significantly improve the writing style. The output still sounded like generic AI copy until I introduced explicit constraints and quality criteria. This reminded me that knowing *who* the reader is isn't enough—AI also needs guidance on *how* the writing should sound.

---

## Final Reusable Prompt

```text
Write a portfolio case study for a frontend engineering manager hiring AI-assisted frontend developers.

Project:
[Describe the project here.]

Structure the response using three sections:

1. Problem
2. What I Did
3. Outcome

Constraints:
- Avoid buzzwords and marketing language.
- Do not invent achievements, metrics, or responsibilities.
- Base every statement on the project description.
- Explain engineering decisions rather than listing technologies.
- Keep the tone clear, practical, curious, honest, and evidence-based.

Quality Criteria:
The final case study should sound like a real engineer describing genuine work. Every sentence should be something the author could confidently explain during a technical interview.
```

---

