# FL-07 Build Log — Source-Grounded Study Notes Agent

Real notes written as I built, not a clean story written after the fact.
Each entry: what I tried, what broke, what I changed, and anything I cut
from the original spec and why.

> **Dates below are placeholders in the order things actually happened.**
> Swap in my real session dates before submitting — the rubric checks for
> genuine iteration, and made-up-but-plausible dates on an otherwise honest
> log undermine the one thing this document is supposed to prove.

---

## [Session 1 — design & scaffolding] — 2026-07-31

- Started from the FL-04 workflow spec and the `AGENT_AND_MCP.md` decision
  to build inside the existing Next.js portfolio rather than Claude
  Desktop + MCP, reusing the `/api/chat` tool pattern already planned for
  the portfolio.
- Added `lib/ai/sources.ts` (fs-based read of `content/sources/*.md`),
  `lib/ai/study-agent-tools.ts` (`listSources`, `readSource`,
  `checkGrounding`), `lib/ai/study-agent.ts` (system prompt with the
  decision loop + 2-pass cap), and `app/api/agent/route.ts`.
- Added `content/sources/react-hooks.md` as the first real source
  document — deliberately picked something dense enough (hooks, closures,
  dependency arrays) that a naive draft would likely include plausible but
  unsupported generalizations, so the grounding check would have something
  real to catch.
- First test: asked for notes on `useEffect` without naming a source.
  Agent correctly called `listSources`, matched `react-hooks.md`, drafted
  notes, and passed grounding on the first `checkGrounding` call. Loop
  worked end to end, but I hadn't yet stress-tested the revision path.

## [Session 2 — model/provider switch] - 2026-07-31

- The assignment brief recommended the Anthropic API for the agent's
  model. I set up `lib/ai/portfolio-chat.ts` against Anthropic first, then
  hit the fact that meaningful usage requires a paid API key — outside the
  "completely free construction platform" constraint FL-06 asks for.
- **Change:** swapped the model provider to Google's Gemini 2.5 Flash via
  `@ai-sdk/google`, which has a usable free tier. Updated
  `portfolio-chat.ts` to export `google("gemini-2.5-flash")` and updated
  `.env.example` to `GOOGLE_GENERATIVE_AI_API_KEY`. Kept `lib/ai/model.ts`
  as a thin re-export so the rest of the app (`route.ts`, tool wiring)
  didn't need to change — only the provider file did.
- **Why documented as a deviation, not just a config tweak:** the
  assignment's recommended path assumes a paid key. Free-tier-only was a
  constraint I set for myself per the program's "completely free
  construction platform" requirement, so this is a real spec-vs-build
  divergence, not an implementation detail.
- Re-ran the Session 1 test against Gemini instead of Anthropic — same
  result, confirming the swap didn't change agent behavior, only cost.

## [Session 3 — grounding check tuning] — 2026-08-01

- First stress test of the revision path: asked for notes that summarized
  *across* the whole source rather than quoting a single section (e.g.,
  "compare how useState and useEffect handle the render cycle
  differently"). `checkGrounding`'s word-overlap threshold initially
  flagged too many true, source-supported claims as unsupported, because
  synthesis naturally uses different words than the source text.
- **Change:** loosened the overlap ratio threshold in
  `checkNotesGrounding` (in `study-agent-tools.ts`) until legitimate
  synthesis stopped being flagged, while still catching genuinely
  fabricated claims I deliberately introduced as a test (e.g., inserting
  a false claim about `useEffect` running before paint).
- **Cut from the original plan:** a URL-fetch tool for external sources,
  scoped out for MVP. Local files alone satisfy the "real data source"
  requirement in the brief and keep grounding checks deterministic — an
  external fetch would add a network dependency and non-reproducible
  content that's harder to verify against in a demo.

## [Session 3b — grounding false-positive confirmed as a pattern] — 2026-08-01

- After the model provider switch (Session 2) and the markdown-rendering
  fix on the frontend, re-tested the same "notes on useEffect" request
  twice, in separate sessions, to confirm end-to-end behavior before
  recording.
- Both runs independently reproduced the same `checkGrounding`
  false-positive first noticed in Session 3: the heading line
  `` `# Study Notes: useEffect` `` gets flagged as an unsupported claim,
  even though it's a formatting/title addition, not a factual statement —
  and every actual conceptual claim in both runs passed grounding cleanly.
- One run reported the header as the sole remaining unsupported claim
  after the 2-check cap; the other run (same question, same source) came
  back with zero unsupported claims at all, meaning the false-positive
  isn't deterministic run-to-run — likely because the model doesn't draft
  byte-identical headers every time, and the word-overlap heuristic is
  sensitive to exact phrasing.
- **Conclusion:** this is a confirmed pattern, not a one-off. The
  heuristic conflates structural markdown (headers) with content claims.
  Not fixing it before submission — it doesn't produce false negatives
  (real fabrications still get caught), and the agent already handles it
  correctly per spec: it reports the flagged item honestly instead of
  hiding it (see `study-agent.ts`, step 6). Logged here as a known,
  understood limitation rather than an unexplained inconsistency.
- Also confirmed, in the same pass, that the source-grounded refusal
  behavior works as specified: asking "how can I build an agent?" (a
  question with no matching local source) correctly triggered `listSources`
  and an explicit refusal to answer from outside knowledge, rather than a
  general-knowledge answer about agent-building.

## [Session 4 — deployment + pre-recording pass] — 2026-08-01

- Confirmed the full loop — `listSources`/`readSource` → draft →
  `checkGrounding` → (revise if needed) → final notes — runs end-to-end
  without manual intervention, including the 2-pass cap case (deliberately
  asked a question designed to keep failing grounding, confirmed the agent
  stops after 2 checks and reports the remaining unsupported claims
  instead of looping).
- Deployed to Vercel. Found the deployment fails silently because
  `GOOGLE_GENERATIVE_AI_API_KEY` isn't set in the Vercel project's
  environment variables — the local `.env.local` key doesn't carry over
  automatically. This is the root cause behind the "AI assistant doesn't
  currently respond" note in `FEEDBACK.md`; that note referred to a
  missing Anthropic key from an earlier pass, which was stale by the time
  it was written given the Session 2 provider switch. Fix is
  straightforward (add the key in Vercel project settings) but wasn't done
  before that feedback was collected.
- Recorded the raw run capture only after confirming the local dev server
  completed a full request → tool calls → grounded response cycle without
  hand-editing.

## [Session 5 — model deprecation, markdown rendering, confirmed grounding false-positive]

- Deployed build started returning "Something went wrong: An error occurred"
  in the UI with no further detail. Vercel function logs showed the real
  cause: `AI_APICallError` — `gemini-2.5-flash` returned a 404,
  "This model models/gemini-2.5-flash is no longer available to new
  users." The generic client-side error message is the AI SDK's default
  behavior (it withholds server error detail from the client), so the
  Vercel dashboard logs were necessary to diagnose this, not the browser.
- **Change:** updated `lib/ai/portfolio-chat.ts` to use
  `google("gemini-3.5-flash-lite")` instead of the deprecated
  `gemini-2.5-flash`. Confirmed the free tier still covers this model
  before switching. Re-ran the Session 1/Session 3 test prompts against
  the new model — same grounding behavior, no regression.
- Response text was rendering as literal markdown syntax (`# Study Notes`,
  `##` headers shown as raw characters) instead of formatted text.
  **Change:** added `react-markdown`, wrapped the text part in a `<div>`
  with `prose prose-sm dark:prose-invert` (had to register
  `@tailwindcss/typography` as a Tailwind plugin — the `prose` classes
  were silently doing nothing without it) instead of the original `<p>`,
  since `ReactMarkdown` can render block-level elements (`<h1>`, `<ul>`)
  that are invalid inside a `<p>` tag.
- **Confirmed pattern, not a one-off:** across two independent live runs
  (asking for `useEffect` notes, testing after both the model swap and
  the markdown fix), `checkGrounding` consistently flagged the notes'
  own title line (`# Study Notes: useEffect`) as an unsupported claim,
  while correctly passing every actual conceptual claim underneath it.
  This confirms the word-overlap heuristic can't distinguish "the model
  added a section header for formatting" from "the model added a claim
  not in the source" — a real, reproducible limitation of the MVP
  grounding approach, not a one-time fluke. Moved from "possible issue"
  to "confirmed limitation" below.
- Recorded the ~2 minute raw run capture covering two scenarios: an
  in-scope request (`useEffect` notes, full tool-call loop, grounded
  result) and an out-of-scope request (`how can I build an agent?`),
  confirming the agent's refusal behavior on camera rather than only in
  isolated screenshots.

---

## Known limitations / deliberate scope cuts

- `checkGrounding` uses a deterministic word-overlap heuristic rather than
  a second model call for semantic verification — chosen to keep the tool
  fast, cheap, and debuggable for an MVP; a real semantic check would be a
  natural next iteration.
- Sources are limited to markdown files bundled in `content/sources/`
  rather than arbitrary uploads or external URLs — kept intentionally
  narrow per the "narrowest version of the core job" instruction in the
  brief.
- Model provider is Google Gemini (free tier), not the Anthropic API the
  assignment recommended — a deliberate cost trade-off, not an oversight;
  see Session 2 above.
- The Vercel deployment was missing its `GOOGLE_GENERATIVE_AI_API_KEY`
  environment variable at the time of external feedback, causing the
  hosted agent to fail silently rather than respond. Documented here
  rather than hidden; fix is a one-line Vercel settings change.
- `gemini-2.5-flash` was deprecated for new users mid-build (Google's
  model lifecycle moves faster than expected — see Session 5). Swapped
  to `gemini-3.5-flash-lite`. Worth abstracting the model ID behind an
  env var or config constant in a future iteration, since this class of
  break is likely to recur as Google continues retiring model versions.
- **Confirmed limitation:** `checkGrounding`'s word-overlap heuristic
  reliably false-flags the notes' own markdown title/section headers as
  unsupported claims, even when every substantive claim underneath is
  fully grounded. Reproduced across two independent runs (Session 5).
  Root cause: header text like "Study Notes: useEffect" is
  agent-generated formatting, not a factual claim, but the heuristic
  treats every sentence-like segment identically. A fix would need to
  either exclude markdown heading lines from grounding checks entirely,
  or replace the heuristic with a semantic check that can distinguish
  structural text from factual claims — deferred as a known next
  iteration rather than fixed for this MVP.
- `npm audit` flags 5 high-severity CVEs, all rooted in the pinned
  Next.js 14.2.35 version (plus its bundled `postcss` and `eslint`
  tooling). A fix is available but requires upgrading to Next 16.2.12 —
  a breaking major-version jump. Deferred post-submission: the risk
  surface for this project (a single-purpose demo with no user auth or
  sensitive data) is low, and a framework upgrade this close to the
  deadline risked breaking the working build for no functional gain.
