# FL-07 Deviation Note

This build follows the specification in
`FL-06_Personal_Agent_Design.md`, revised from my original FL-06 draft.
Three deviations from that original draft are documented below, each with
the reason behind it.

| Original FL-06 draft | What was actually built | Why |
|---|---|---|
| AI Internship Assignment Coach — reviews assignment briefs, plans implementation, checks submissions against evaluation criteria | Source-Grounded Study Notes Agent — turns local markdown sources into notes that never contain unsupported claims | The Coach's scope (reading assignment briefs, GitHub repos, judging submission completeness) was hard to reduce to a single testable end-to-end loop within ~10 hours. The Study Notes Agent has one clear job, one clear pass/fail signal (is every claim grounded in the source?), and reuses the FL-04 workflow I'd already mapped out — a narrower, more buildable scope for Checkpoint 1. |
| Platform: Claude Project (free) | Platform: custom Next.js route + Vercel AI SDK, deployed on Vercel | A Claude Project isn't independently deployable or linkable — a reviewer would need their own Claude access to see it run. Building the agent into my existing portfolio stack (already chosen in FL-04/`THREE_ROADS.md`) makes it a live, shareable demo, which matters for a portfolio whose whole purpose is giving a hiring manager direct evidence. |
| Model: unspecified, implicitly assumed Anthropic API per assignment recommendation | Model: Google Gemini 2.5 Flash via `@ai-sdk/google` | The Anthropic API requires a paid key for meaningful usage. The program's platform requirement is "completely free" — Gemini's free tier meets that constraint while providing the same tool-calling capability the agent's loop depends on. This is a cost-driven substitution, not a change to what the agent can do. |

The full reasoning behind each decision — including the specific tuning and
testing that led to them — is in `BUILD_LOG.md`.

**Net effect:** the agent's *job*, *tool contract*, and *decision loop*
match the FL-06 spec exactly (see `FL-06_Personal_Agent_Design.md`, §§1–6).
What changed is the platform it runs on and the model provider behind it —
both changes made for reasons independent of what the agent needs to
accomplish.
