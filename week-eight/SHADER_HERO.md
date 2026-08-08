# FE-AA3 — Signature Hero: A Fullscreen Shader

## What was built

A fullscreen fragment-shader "aurora" hero on the Home page (`/`), replacing
the previous plain-background hero. The shader itself lives in
`features/hero/shaders/aurora.ts`; the R3F plumbing is in
`features/hero/components/aurora-canvas.tsx`; `features/hero/components/shader-hero.tsx`
decides whether to show the live shader or a static fallback.

## Remix note

Started from the session's shader playground's domain-warped fbm flow
field — that's the piece that actually produces the "aurora" look, and I
kept its structure (fbm feeding into a second fbm call, offset by time).
Everything else was rebuilt for this assignment: the color palette (pulled
directly from `IDENTITY_KIT.md`'s dark-mode tokens instead of the
playground's original colors), the mouse-influence term, the vignette, and
the grain pass are all new, not present in the original playground version.

## The uv / time / mouse mental model, in my own words

- **uv** is "where am I on the plane, from 0 to 1 in each axis." Every pixel
  the fragment shader runs for gets its own `vUv`, passed from the vertex
  shader. I center it (`uv - 0.5`) and correct for aspect ratio so the noise
  pattern isn't stretched into ovals on a wide monitor.
- **u_time** is "how many seconds since the canvas mounted." I don't use it
  directly to move things — I use it as an *offset* fed into the noise
  function (`warp + vec2(0.0, u_time * 0.05)`), so the same noise field is
  sampled at a slightly different position every frame. That's why it
  drifts instead of jumping.
- **u_mouse** arrives as a normalized 0..1 UV (R3F gives this for free via
  `event.uv` on a pointer event over the mesh, no manual rect math needed).
  I don't use it to move a shape — I use it to bend the *sampling
  coordinate* toward the cursor before the noise function even runs, which
  is why the flow field leans rather than something visibly "chasing" the
  mouse.

## Reduced-motion / performance fallback (one-liner)

If `prefers-reduced-motion` is set or the browser lacks WebGL, the hero
renders a static CSS gradient using the exact same rose/blue palette and no
JS bundle at all; otherwise the WebGL canvas runs with `dpr` capped at 1.5
and its render loop fully stopped (`frameloop="never"`) whenever the tab is
hidden.

## Performance fix after first deploy

The first deployed version auto-upgraded to the shader canvas inside a
plain `useEffect` on mount. A Lighthouse run on that deployment (mobile,
Slow 4G) showed a real regression: **Total Blocking Time 5,750ms** and a
**540ms render delay on the headline** (the page's actual LCP element),
with the same chunk (`496...js`, the R3F/three.js bundle) showing up
repeatedly in the long-task list for the entire trace — meaning the
shader's render loop was competing with the headline paint from the
first frame. This is the same root cause already documented and fixed
for `/lab/3d` in `week-seven/AUDIT.md`, just newly reintroduced by the
hero's auto-play behavior having no equivalent guard.

**Fix:** the WebGL probe and the `import()` of the shader chunk are now
deferred to `requestIdleCallback` (with a `setTimeout` fallback for
Safari), so they only run once the browser has spare main-thread time —
after first paint, not competing with it. The hero also now checks
`useLowPowerContext` (previously only used by the 3D Lab) before
auto-upgrading, so a slow connection or low-memory device stays on the
static gradient even with WebGL support and no `prefers-reduced-motion`.

## Manual testing done

- Chrome DevTools → Rendering → "Emulate CSS prefers-reduced-motion:
  reduce" → confirms the static gradient renders instead of the canvas.
- Switched tabs while the shader was running, confirmed (via the
  Performance tab) that the R3F render loop stops when `document.hidden`
  is true and resumes on tab focus.
- Verified headline/CTA text contrast against the busiest part of the
  shader (rose/blue overlap band) using the scrim, not just the shader's
  own vignette.
