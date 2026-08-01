# FL-07 Build Log — Source-Grounded Study Notes Agent

Real notes written as I built, not a clean story written after the fact.
Each entry: what I tried, what broke, what I changed, and anything I cut
from the original spec and why.

> **Dates below are placeholders in the order things actually happened.**
> Swap in your real session dates before submitting — the rubric checks for
> genuine iteration, and made-up-but-plausible dates on an otherwise honest
> log undermine the one thing this document is supposed to prove.

---

## [Session 1 — design & scaffolding]

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

## [Session 2 — model/provider switch]

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

## [Session 3 — grounding check tuning]

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

## [Session 4 — deployment + pre-recording pass]

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
