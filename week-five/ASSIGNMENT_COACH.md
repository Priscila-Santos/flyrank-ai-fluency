# Personal Agent Design Specification (FL-06)

**Author:** Priscila Santos
**Agent name:** Source-Grounded Study Notes Agent
**Version:** 1.0 — supersedes the earlier "AI Internship Assignment Coach" concept

> **Note on this revision:** My original FL-06 draft specified an AI Internship
> Assignment Coach on Claude Project. During construction (FL-07) I pivoted to
> a narrower, more testable agent — one that turns my own local study
> materials into grounded, honest study notes — built directly into my
> portfolio's Next.js app instead of Claude Project. That pivot is explained
> in full under "Platform Choice" below. This document describes the agent
> that was actually specified *before* the build it corresponds to, so FL-07
> can reference it directly instead of an outdated spec.

---

## 1. Job To Be Done

The Source-Grounded Study Notes Agent turns a local source document (lecture
notes, documentation, an article I've saved) into study notes I can actually
trust — notes that never contain information the source doesn't support.

Instead of a general-purpose tutor that answers from its own training data,
this agent is deliberately narrow: it reads *my* material, drafts notes from
*only* that material, and checks its own draft against the source before
calling the answer final. If the source doesn't cover something I asked
about, it says so instead of filling the gap from general knowledge.

The job is not "explain this topic well." The job is "produce notes I can
cite back to a specific source, with no invented content."

---

## 2. User

**Primary user:** Priscila Santos — Information Systems undergraduate,
Frontend AI Engineering Intern at FlyRank AI.

**Context of use:** Studying for university courses (e.g., Algorithms,
Information Systems) and reviewing technical documentation I want to retain
(e.g., React internals) using markdown notes I've already collected.

### Usage frequency

* 2–4 times per week during active coursework
* Primarily short sessions (one topic, one source) rather than long ones
* Usage grows before exams, when I'm consolidating scattered notes into
  revision material

---

## 3. Typical Workflow

### Step 1 — Identify the source
If I name a topic without a specific source, the agent calls `listSources`
and either matches the best candidate or asks which one I mean.

### Step 2 — Load grounding material
The agent calls `readSource` with the chosen slug. If the source isn't
found, it says so directly instead of guessing or inventing content.

### Step 3 — Draft notes
The agent writes structured notes using only information present in the
source body.

### Step 4 — Check grounding
The agent calls `checkGrounding`, which runs a deterministic word-overlap
check between the draft and the source text.

### Step 5 — Revise once if needed
If unsupported claims are flagged, the agent revises the notes once and
re-checks.

### Step 6 — Finalize honestly
After a hard cap of two grounding checks, the agent finalizes the notes and
explicitly lists any claim it could not verify against the source, rather
than silently dropping or hiding the problem.

---

## 4. Tools and Data

| Tool | Purpose | Access Plan |
|---|---|---|
| `listSources` | Lists available local source documents (slug, title, topic, level) | Reads `content/sources/*.md` via Node `fs/promises`; read-only, no network |
| `readSource` | Reads the full markdown body of a chosen source | Same directory, read-only; rejects any slug not present on disk |
| `checkGrounding` | Deterministic word-overlap check between draft notes and source text | Pure in-process computation; no external calls, no data leaves the app |
| Model (Gemini 2.5 Flash via Vercel AI SDK) | Drafts notes, decides when to call tools, revises on grounding failures | Called server-side only, via a single provider API key stored in Vercel environment variables |

### Data sources
The agent uses only markdown files I've placed in `content/sources/` —
currently my own notes and reference material (e.g., a React Hooks
reference document). It does **not** assume access to:

* the open internet or live search
* my university's LMS or private course systems
* any file outside the `content/sources/` directory

This is a deliberate scope cut from my original Assignment Coach draft,
which assumed broader access (GitHub repos, assignment pages, uploaded
files). Narrowing to a single local directory keeps grounding checks
deterministic and keeps the whole loop testable end-to-end within the
~10-hour build budget.

---

## 5. Draft System Instructions

```
You are a Source-Grounded Study Notes Agent. Your job is to produce study
notes that are strictly grounded in local markdown source documents — never
in outside knowledge.

Follow this decision loop on every request:

1. Identify the source. If the user does not name a specific source, call
   listSources first and either pick the best match or ask which source
   they want.
2. Load grounding material. Call readSource with the chosen slug. If the
   source is not found, explain that clearly and stop — do not invent
   content.
3. Draft study notes using ONLY information present in the source body.
4. Check grounding by calling checkGrounding with the draft and source slug.
5. If checkGrounding reports unsupported claims, revise once and re-check.
6. Hard cap: call checkGrounding at most 2 times total per request. After
   the second check, finalize the notes even if unsupported claims remain —
   explicitly tell the user which claims could not be verified.
7. If the source does not cover something the user asked about, say so
   directly instead of filling the gap from general knowledge.
```

---

## 6. Evaluation Cases (Pre-Build)

Written before construction, in the FL-03 style, so I'd know what "working"
meant before I started building.

### Eval 1 — Topic Match, No Source Named
**Input:** "Give me study notes on useEffect" (no source slug given).
**Expected behavior:** Agent calls `listSources`, matches `react-hooks.md`,
proceeds without asking an unnecessary clarifying question.
**Pass criteria:** Correct source is selected without user intervention.

### Eval 2 — Missing Source
**Input:** A request naming a topic with no matching source on disk (e.g.,
"notes on Kubernetes" when no Kubernetes source exists).
**Expected behavior:** Agent states plainly that no matching source was
found, rather than answering from general knowledge.
**Pass criteria:** No study notes are produced; the gap is stated honestly.

### Eval 3 — Grounded Draft, First Pass
**Input:** A request on a well-covered topic (e.g., "explain the dependency
array in useEffect").
**Expected behavior:** Draft notes pass `checkGrounding` with no
unsupported claims, and the agent finalizes without needing a second check.
**Pass criteria:** Final notes contain zero fabricated facts; one
grounding check is sufficient.

### Eval 4 — Grounded Draft, Revision Required
**Input:** A request broad enough that a first draft likely includes
generalized/textbook phrasing not present in the source verbatim or in
substance.
**Expected behavior:** First `checkGrounding` call flags unsupported
claims; agent revises once and re-checks.
**Pass criteria:** Second check shows an improved grounded ratio, and the
final notes reflect the revision — not the original flagged draft.

### Eval 5 — Hard Cap Enforcement
**Input:** A request engineered to be difficult to fully ground (e.g.,
asking for notes that synthesize across the source in ways that produce
low word-overlap even when factually correct).
**Expected behavior:** After exactly 2 `checkGrounding` calls, the agent
stops looping and finalizes, explicitly listing any remaining unsupported
claims rather than looping indefinitely or silently hiding them.
**Pass criteria:** No more than 2 grounding-check tool calls occur; the
user is told what's still unverified.

---

## 7. Risks and Guardrails

The agent must always:

* distinguish source-supported claims from unsupported ones, and say so
  explicitly rather than presenting both with equal confidence;
* stop and report when a requested source doesn't exist, instead of
  answering from general knowledge;
* respect the 2-call cap on `checkGrounding` so a single request can't loop
  indefinitely.

The agent must never:

* fabricate content attributed to a source;
* silently drop an unsupported claim without telling the user;
* read files outside `content/sources/`, or reach out to the network for
  additional context.

No action in this agent is irreversible or destructive — it only reads
local files and returns text — so no confirmation gate is required before
tool use, unlike a coach-style agent that might edit files or submit work
on a user's behalf.

---

## 8. Platform Choice

**Selected platform:** Custom agent route inside my existing Next.js
portfolio, using the Vercel AI SDK (`streamText`, tool calling) with Gemini
2.5 Flash as the model.

### Why

* Reuses the `/api/chat`-style route pattern I was already building for the
  portfolio, so the agent ships as a live, linkable demo rather than a
  separate Claude Project only I can access.
* Tool calling (`listSources`, `readSource`, `checkGrounding`) maps
  directly onto the Vercel AI SDK's `tool()` interface with Zod schemas —
  no bespoke plumbing needed.
* Keeps the whole loop — request → tool calls → grounding check → revision
  → final answer — inside one deployable app, which made the two-minute
  end-to-end screen capture required for FL-07 straightforward to produce.

### Alternative considered: Claude Desktop + Filesystem MCP

This was my original direction, explored in `AGENT_AND_MCP.md`. It's a
faster way to get *file access* working (the Filesystem MCP server is a
copy-paste setup), and it's the more natural fit for grading an "MCP tool
use" requirement literally.

**Reasons not selected:**
* It isn't deployable or demoable as a link — anyone reviewing my
  portfolio would need their own Claude Desktop + MCP setup to see it run,
  which works against the portfolio's whole purpose of being evidence a
  hiring manager can access directly.
* My existing Next.js/Vercel AI SDK stack already gives me tool calling and
  a UI for free; adding MCP would mean maintaining two agent
  implementations instead of one.
* The core requirement — grounded, tool-using, non-fabricating notes — does
  not require the MCP protocol specifically; a directly-implemented tool
  achieves the same job-to-be-done with less infrastructure.

---

## 9. Scope Validation

| Task | Hours |
|---|---:|
| Design agent loop + tool contracts | 1 |
| Implement `sources.ts` + `study-agent-tools.ts` | 2 |
| Implement `study-agent.ts` system prompt + wiring | 1.5 |
| Build `/api/agent` route + chat UI | 2 |
| Add first real source document + manual testing | 1.5 |
| Fix deployment (env vars, provider config) | 1 |
| Documentation (build log, this spec) | 1 |

**Estimated total:** **10 hours** — within the ~10-hour target scope.
