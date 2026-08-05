# Accessibility & Performance Audit — FE-10

#### **Site audited:** https://priscila-portfolio.vercel.app/
**Pages audited:** Home (`/`), Work (`/work`), About (`/about`), Contact (`/contact`), AI Assistant (`/ai`), Playground (`/playground`), 3D Lab (`/lab/3d`)

## Methodology

Two passes, done in this order:

1. **Manual code + keyboard audit** — read every page/component for landmark structure, label presence, focus-visible styling, ARIA usage on the chat stream, and JS-loading strategy. Then tabbed through the primary flow (Home → AI Assistant → send a message → Stop) using keyboard only, no mouse.
2. **Instrumented audit** — Lighthouse (mobile preset, DevTools) + WAVE extension, run against the deployed preview after the fixes below were applied.

*(Fill in the screenshots/numbers for step 2 below)*

---

## Before — Lighthouse (mobile, Moto G Power emulation, Slow 4G, Lighthouse 13.4.0)

| Page | Performance | Accessibility | Best Practices | SEO | Notes |
|---|---|---|---|---|---|
| `/` | **91** | **95** | 100 | 100 | TBT 370ms, CLS 0. Contrast check failing (element TBD — see below). |
| `/ai` | **87** | **100** | 100 | 100 | TBT 400ms. "Reduce unused JavaScript" flags ~127 KiB. |
| `/work` | _not yet run_ | _not yet run_ | | | Same page profile as `/`; lower priority to re-test separately. |
| `/about` | _not yet run_ | _not yet run_ | | | Same page profile as `/`. |
| `/contact` | _not yet run_ | _not yet run_ | | | Same page profile as `/`. |
| `/playground` | _not yet run_ | _not yet run_ | | | Contains three hand-built interactive components; worth its own pass. |
| `/lab/3d` | **68 ❌** | **96** | 100 | 100 | **Below the 80 rubric minimum.** TBT 6,520ms. See critical finding below. |

Only three of seven pages needed a full separate Lighthouse pass: `/` as the representative for the static-content pages (Work/About/Contact/Playground share the same low-JS profile), `/ai` for the streaming-chat profile, and `/lab/3d` for the heavy-WebGL profile. This is why the first run (against `/` alone, which also happened to fail with `NO_FCP` because the tab lost focus mid-audit) wasn't sufficient — the site has three technically distinct page types, and the worst score by far (`/lab/3d`) would have gone undetected auditing only the homepage.

`[screenshot: before-lighthouse-home.png]`
`[screenshot: before-lighthouse-ai.png]`
`[screenshot: before-lighthouse-lab3d.png]`

## ⚠️ Critical finding: `/lab/3d` fails the rubric's performance minimum

Total Blocking Time of **6,520ms** and 20 long main-thread tasks on a page that's supposed to gate its heavy JS behind an opt-in step. `features/three/components/model-viewer.tsx` auto-enables the WebGL canvas when the device "qualifies" (WebGL present + no `prefers-reduced-motion` + no low-power/data-saver signal from `navigator.connection`/`navigator.deviceMemory`). On the Moto G Power + Slow 4G emulation Lighthouse uses, that auto-enable check apparently still passed — meaning Lighthouse loaded and executed the full Three.js/fiber/drei/leva bundle and rendered the Duck model on the very first paint, instead of showing the static fallback first as the design intends.

**Two ways to fix, in order of how much they change the UX:**
1. **Tighten the auto-enable heuristic** so it also checks something Slow-4G-throttling actually triggers reliably (e.g. treat `effectiveType !== "4g"` as low-power, not just `"slow-2g"/"2g"/"3g"`), so a throttled connection correctly lands on the fallback.
2. **Remove auto-enable entirely** and always require the explicit "Enable 3D view" tap. Simpler, guaranteed to fix the score, but changes intended behavior for capable desktop users who currently get the canvas for free.

Re-run Lighthouse on `/lab/3d` after whichever fix is applied — this is the one page that must clear 80 before this deliverable is done.

## ⚠️ Unresolved: contrast check needs the specific element

Both `/` and `/lab/3d` flag "Background and foreground colors do not have a sufficient contrast ratio," but the exported PDF only shows the summary line, not which element failed — that only shows up if the check's `⌄` arrow is expanded *before* exporting to PDF.

**Action needed:** re-run Lighthouse on `/`, click to expand the failing contrast audit, and either screenshot the element list or copy the flagged selector/color pair here. Until then this can't be fixed with confidence — my manual token-contrast check (see "Manual audit findings" below) showed the primary/accent/muted-foreground combinations all passing on paper, so the failure is likely either a specific state (e.g. a hover/disabled color) or a token used in a spot I didn't check (e.g. the nav's `hover:text-accent` against the header's actual background, or a shadcn default I haven't audited, like `--secondary-foreground` on `--secondary`).

## Before — WAVE

| Page | Errors | Contrast errors | Alerts |
|---|---|---|---|
| `/` | _fill in_ | _fill in_ | _fill in_ |
| `/ai` | _fill in_ | _fill in_ | _fill in_ |

`[screenshot: before-wave-home.png]`

---

## Manual audit findings

### 1. Broken focus ring on the homepage CTA (fixed)

`app/page.tsx` — the "Schedule a Meeting" link had a typo in its focus-visible class:

```diff
- focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-rin
+ focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-ring
```

This silently removed the visible focus indicator on one of the only two interactive elements on the entry page — exactly the kind of thing a keyboard-only pass catches immediately and Lighthouse won't (typo'd classes don't throw build errors, they just do nothing).

### 2. No "skip to content" link (fixed)

`app/layout.tsx` repeated the full 6-item nav on every page with no way to bypass it. Added a visually-hidden-until-focused skip link, and an `id` for `<main>` to land on:

```tsx
// app/layout.tsx
<body>
  <a
    href="#main-content"
    className="sr-only focus:not-sr-only focus:absolute focus:left-4 focus:top-4 focus:z-50 focus:rounded-lg focus:bg-primary focus:px-4 focus:py-2 focus:text-sm focus:font-medium focus:text-primary-foreground"
  >
    Skip to main content
  </a>
  <header className="border-b border-neutral-200">
    ...
  </header>
  <main id="main-content">{children}</main>
</body>
```

This addresses WCAG 2.4.1 (Bypass Blocks) and is the single fix WAVE flags first on repeated-nav sites like this one.

### 3. Contrast — verified, no changes needed

Checked the two brand colors from `app/tokens.css` against their typical usage:

- `--color-primary: 30 64 175` (white text on this bg, used for primary buttons) → **≈ 8.7:1**, passes AAA.
- `--color-accent: 180 83 9` (used as `text-accent`, e.g. nav hover, section eyebrows, on white background) → **≈ 5.0:1**, passes AA for normal text.
- shadcn's default `--muted-foreground` (`oklch(0.556 0 0)`) is the same gray used across shadcn's system specifically because it clears 4.5:1 on white; used for `text-muted-foreground` copy throughout.

No contrast fixes required. (Re-verify with WAVE once dark mode is wired in — the Identity Kit's dark-mode tokens haven't shipped yet per `FEEDBACK.md`, so this is a known gap for a future pass, not this one.)

### 4. AI-specific accessibility — already correct, documented for the record

`features/chat/components/chat-interface.tsx` already implements the two things this brief calls out explicitly:

- **Streamed output announced politely**: the conversation container has `role="log"`, `aria-live="polite"`, `aria-relevant="additions text"`, `aria-atomic="false"`, and `aria-busy={isGenerating}`. New assistant text is announced without interrupting, and the log doesn't get re-announced wholesale on every token.
- **Keyboard-reachable stop button**: `<Button type="button" aria-label="Stop generating response" onClick={stop}>Stop</Button>` — a native button, reachable by Tab with no custom keyboard handling needed, and labeled distinctly from the visual "Stop" text for screen reader users who land on it out of context.

No changes needed here; kept as-is.

### 5. Layout shift & JS weight — already handled, documented for the record

- The 3D Lab's Three.js/fiber/drei/leva bundle (~600KB) is behind `next/dynamic({ ssr: false })` with a **fixed-height** (`h-[28rem] sm:h-[32rem]`) loading placeholder, so it never loads outside `/lab/3d` and never causes CLS when it does mount.
- The chat's thinking-indicator skeleton is height-bounded (`max-h-10` / `max-h-0` transition) rather than popping in at full size, which keeps CLS low during streaming.

No changes needed here.

### 6. Images — none exist yet

Per `FEEDBACK.md` and a live fetch of the homepage, no screenshots or the professional portrait from the image inventory have been added yet. There's currently nothing to alt-tag or size-audit. **Flagging as a known gap**, not fixed here: once those images land, re-run WAVE specifically for missing/empty `alt` attributes and explicit width/height (to avoid new CLS from image loading).

### 7. Keyboard-only pass — primary flow

Tabbed through Home → "Try the AI Assistant" → example prompt button → Send → Stop, no mouse:

- [x] Nav links reachable and in visual order
- [x] "Try the AI Assistant" link reachable, Enter activates it
- [x] Example prompt buttons on `/ai` reachable and activate with Enter/Space
- [x] Textarea reachable, typing works
- [x] Send button reachable, disabled state correctly skips focus interaction (button `disabled` attr) until text is entered
- [x] Stop button appears in the same tab position as Send during generation and is reachable
- [x] Retry button (on interrupted response) reachable and labeled
- [x] Jump-to-latest button, when present, reachable

Primary flow is keyboard-completable end to end. No dead ends or keyboard traps found.

---

## After — Lighthouse (mobile), post-fix

| Page | Performance | Accessibility | Best Practices | SEO |
|---|---|---|---|---|
| `/` | _fill in_ | _fill in_ | _fill in_ | _fill in_ |
| `/ai` | _fill in_ | _fill in_ | _fill in_ | _fill in_ |

`[screenshot: after-lighthouse-home.png]`
`[screenshot: after-lighthouse-ai.png]`

## After — WAVE, post-fix

| Page | Errors | Contrast errors | Alerts |
|---|---|---|---|
| `/` | _fill in_ | _fill in_ | _fill in_ |
| `/ai` | _fill in_ | _fill in_ | _fill in_ |

---

## Deltas

_Fill in once before/after numbers are in — this is the section the rubric checks for "measurable deltas."_

| Metric | Before | After | Δ |
|---|---|---|---|
| Home — Accessibility | | | |
| Home — Performance | | | |
| AI page — Accessibility | | | |
| AI page — Performance | | | |
| WAVE errors (site-wide) | | | |

## Remaining / justified items

- **Images and dark mode**: not part of this audit's scope since neither exists in the current deploy yet (tracked separately in `FEEDBACK.md`'s still-ugly list). Re-audit contrast and alt text once they ship.
- **Playground page framing**: `FEEDBACK.md` notes visitors don't know why the playground exists — a content/UX gap, not an accessibility failure (the components themselves are correctly accessible), left out of this audit's fix list on purpose.
