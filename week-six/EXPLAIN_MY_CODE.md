# Explain It Like You Built It

**Track:** General AI Fluency — Week 6
**Author:** Priscila Santos
**Piece of the build:** How my portfolio's AI chat actually "knows" things about me, and how it decides what to say when it doesn't

---

## What I picked, and why

My portfolio has an AI assistant at `/ai` that answers questions like "What technologies do you use?" or "Tell me about a recent project." Before this week, I could tell you *that* it worked, but not really *how* — I was treating it like a black box: "I ask something, the AI answers, that's it." I picked this piece specifically because I realized I couldn't explain two things a friend would immediately ask: *where does it get information about me*, and *what stops it from making things up*.

## The misconception I had

I assumed Gemini (the model I use, via `@ai-sdk/google`) somehow "knew" about me — like it had read my site, or searched for me, the way a person would Google me. That's not what's happening at all.

Gemini is a general-purpose language model. Out of the box, it knows nothing about me, the same way Claude or ChatGPT wouldn't unless someone told them. The "knowledge" about me isn't coming from the model — it's coming from my own code.

## Where the information actually lives

In `lib/ai/portfolio-chat.ts`, I have a small hardcoded array:

```ts
const portfolioContext = [
  "Priscila Santos is a first-semester Information Systems student and a Frontend AI Engineering Intern at FlyRank.",
  "Her portfolio includes work with React, TypeScript, Next.js, Tailwind CSS, and Spring Boot.",
  "Portfolio topics include Academic Planner, Playground, and Sentiment Analysis API.",
].join("\n");
```

This gets pasted directly into the system prompt that's sent to Gemini on *every single request*, no matter what the visitor asks:

```ts
export const portfolioChatSystemPrompt = `
You are the helpful portfolio assistant for Priscila Santos.

Portfolio context:
${portfolioContext}
...
`;
```

The key thing I got wrong before: this is not a search. There's no lookup step, no database, no "find the relevant fact for this question." The entire `portfolioContext` block is sent along with the system prompt every time, whether the question needs it or not. This is sometimes called "context stuffing" or basic prompt grounding — it's the simplest possible way to give a model facts, and it only works because my context is short enough to fit comfortably in every request.

## The full flow, for both scenarios

1. A visitor types a question and hits send.
2. The client (`useChat`) sends the full conversation to `/api/chat`.
3. My route handler calls `streamText({ model, system: portfolioChatSystemPrompt, messages, tools })`. The `system` string (which already contains `portfolioContext`) and the conversation `messages` are sent to Gemini together, in the same call.

**Scenario A — the answer is in the context.** Someone asks about my tech stack. Gemini reads the system prompt, finds "React, TypeScript, Next.js, Tailwind CSS, and Spring Boot" already sitting there, and answers from it directly.

**Scenario B — the answer is not in the context.** Someone asks something unrelated, like my favorite movie. Nothing in `portfolioContext` covers that. What stops Gemini from inventing an answer isn't a technical check — it's a plain-text instruction at the end of the same prompt:

```
Do not invent projects, achievements, credentials, links, or personal details.
If a visitor asks for information that is not available in the portfolio
context, say so clearly and suggest that they contact Priscila directly.
```

This is only a request the model can choose to follow, not an enforced rule. There is no second pass that verifies the answer against a source afterward — unlike the `checkGrounding` tool I built for the Study Notes Agent in FL-06/07, which actually checks the draft against the source text before finalizing. My portfolio chat has no equivalent safeguard today.

## Where formatting comes from

Gemini writes its answers as plain markdown text — `**bold**`, `*italics*`, and pipe-table syntax when it decides that fits. On the frontend, `chat-message.tsx` uses `ReactMarkdown` with the `remarkGfm` plugin to turn that raw markdown into real HTML (`<strong>`, `<table>`, etc.). I never told the model when to use a table or bold text — there's no instruction for that in my system prompt. It decides that on its own, based on patterns it learned during training (tables for comparisons, bold for key terms, and so on).

## What this taught me, and where I'm taking it next

Comparing this to the Study Notes Agent from the "Build the Agent" assignment made the gap obvious: my Study Notes Agent has real tool calls, a grounding check, and a decision loop — it's an actual agent with a verification step. My portfolio chat is closer to a single unverified prompt-stuffing call: no tools, no lookup, no check on the output, and a very thin, hardcoded slice of facts about me.

Now that I understand the mechanism, I want to apply the same agent pattern here: give the portfolio assistant real tools (reading richer project/case-study content instead of a three-line hardcoded array) and a grounding check similar to `checkGrounding`, so it can actually verify its answers about my work instead of relying on an instruction it might not follow. That's a concrete next step I plan to take before treating this feature as finished.
