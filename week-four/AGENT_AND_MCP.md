# Agent Concepts and MCP Fundamentals

### Workflow

A workflow is a predefined sequence of steps. The execution path is known in advance and each step hands its output to the next.

My pipeline follows exactly this pattern:

```
Upload source
        ↓
NotebookLM summarizes
        ↓
ChatGPT creates notes
        ↓
ChatGPT reviews notes
        ↓
ChatGPT formats revision sheet
```

The order never changes.

The AI is **not deciding what to do next**.

It simply performs the next predefined step.

---

### Agent

An agent has autonomy.

Instead of following a fixed sequence, it can:

* decide which tool to use
* determine whether another step is necessary
* repeat work if results are insufficient
* gather additional information
* stop when it judges the objective is complete

The AI controls the process rather than simply executing it.

---

> My FL-04, **Source-Grounded Study Notes Pipeline**, is a **workflow** because it follows a fixed sequence of predefined steps. The process always starts by uploading study materials to NotebookLM, which generates a grounded summary. That summary is then passed to ChatGPT to create study notes, followed by a review step and a final formatting step into a revision sheet. Each stage has a clearly defined input and output, and the next action is predetermined rather than chosen by the AI.
>
> According to Anthropic's *Building Effective Agents*, workflows are systems where the developer defines the execution path in advance. My pipeline matches this definition because the AI does not decide what to do next—it simply executes each predefined step and passes its output to the next stage. There is no planning, tool selection, or autonomous decision-making during execution.
>
> Therefore, my FL-04 should be classified as a **multi-step AI workflow**, not an autonomous agent.


| My FL-04 Workflow                               | An Agent Would...                                                 |
| ----------------------------------------------- | ----------------------------------------------------------------- |
| Follows the same four steps every time          | Decide which steps are necessary                                  |
| Uses NotebookLM then ChatGPT in a fixed order   | Choose between different tools based on the task                  |
| Never changes the execution path                | Plan, loop, or skip steps dynamically                             |
| Requires the user to move outputs between tools | Invoke tools autonomously and continue until the goal is achieved |

Therefore, my FL-04 pipeline should be classified as:

> **A multi-step AI workflow, not an autonomous agent.**

That is exactly the distinction Anthropic makes.

---

# 2. Understanding MCP

MCP stands for **Model Context Protocol**.

A useful analogy from the official documentation is that MCP is like **USB-C for AI applications**: instead of each AI application needing a custom integration for every external system, MCP provides a common protocol so models can interact with many tools in a consistent way.

The three core building blocks are:

### Tools

Actions the model can perform.

Examples:

* read a file
* search GitHub
* execute SQL
* call an API
* send email

These perform work.

---

### Resources

Information exposed to the model.

Examples:

* local files
* PDFs
* databases
* documentation
* repositories

These provide context rather than actions.

---

### Prompts

Reusable instructions provided by an MCP server.

Instead of rewriting the same long prompt repeatedly, the server can expose standardized prompts that the model can invoke.

---

# 3. Which MCP client should I use?

Since I'm already using **Claude**, the easiest option is **Claude Desktop** with an MCP server.

Good beginner servers include:

* Filesystem MCP
* GitHub MCP
* SQLite MCP
* Memory MCP

The **Filesystem MCP** is by far the simplest and fully satisfies the assignment.

---

# 4. Three tasks that chat alone cannot perform

After connecting the Filesystem MCP server, I can demonstrate:

### Task 1

Read a local file.

Example:

> Open my local README.md file and summarize it.

Without MCP:

❌ Claude cannot access my computer.

With MCP:

✅ It opens the file directly.

---

### Task 2

List files inside a folder.

Example:

> Show every file inside my FlyRank project.

Again:

Chat alone cannot inspect my hard drive.

---

### Task 3

Search across local files.

Example:

> Find every file containing the phrase "workflow".

Again:

Impossible without tool access.

---

# 5. How your workflow could become an agent

Give it a concrete name.

For example:

> **Autonomous Study Assistant**

Instead of:

```
NotebookLM
↓

ChatGPT

↓

Review

↓

Formatting
```

it could operate like this:

```
Receive topic
        ↓
Search local notes
        ↓
Need more information?
        │
      Yes
        ↓
Search documentation
        ↓
Summarize
        ↓
Evaluate completeness
        │
Still missing concepts?
        │
      Yes
        ↓
Search again
        ↓
Generate notes
        ↓
Self-review
        ↓
Export
```

Notice the key difference:

The AI decides:

* whether more research is needed
* whether the output is complete
* whether to loop back and improve it

That adaptive decision-making is what makes it an agent.
