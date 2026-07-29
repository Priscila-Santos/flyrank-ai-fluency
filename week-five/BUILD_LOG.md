# FL-07 Build Log — Source-Grounded Study Notes Agent

Real, dated notes written *as I build*, not a clean story written after the
fact. Each entry: what I tried, what broke, what I changed, and anything I
cut from the original spec and why.

---

## YYYY-MM-DD

- Started from the FL-04 workflow spec and the AGENT_AND_MCP.md decision to
  build inside the existing Next.js portfolio rather than Claude Desktop +
  MCP, reusing the /api/chat tool pattern.
- Added lib/ai/sources.ts (fs-based read of content/sources/*.md),
  lib/ai/study-agent-tools.ts (listSources, readSource, checkGrounding),
  lib/ai/study-agent.ts (system prompt with the decision loop + 2-pass cap),
  and app/api/agent/route.ts.
- Added content/sources/react-hooks.md as the first real source document.
- [Fill in: first test result — what did you send it, what happened?]

## YYYY-MM-DD

- [What broke this time? e.g. "checkGrounding flagged true statements
  because the word-match threshold was too strict — loosened from X to Y."]
- [What did you change, and why?]
- [Anything cut from the original plan and why — e.g. "planned a URL-fetch
  tool for external sources, cut for MVP scope; local files alone satisfy
  the 'real data source' requirement and keep grounding checks
  deterministic."]

## YYYY-MM-DD

- [Final pre-recording pass: confirm the full loop — listSources/readSource
  → draft → checkGrounding → (revise if needed) → final notes — runs
  end-to-end without manual intervention.]
- [Note anything still rough/known-limited for the "still ugly" list, e.g.
  "checkGrounding is a keyword-overlap heuristic, not true semantic
  verification — documented as a known limitation, not hidden."]

---

## Known limitations / deliberate scope cuts

- checkGrounding uses a deterministic word-overlap heuristic rather than a
  second model call for semantic verification — chosen to keep the tool
  fast, cheap, and debuggable for an MVP; a real semantic check would be a
  natural next iteration.
- Sources are limited to markdown files bundled in content/sources/ rather
  than arbitrary uploads or external URLs — kept intentionally narrow per
  the "narrowest version of the core job" instruction in the brief.
- [Add your own as you go.]
