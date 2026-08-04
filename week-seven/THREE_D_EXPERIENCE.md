# 3D Model Viewer (FE-AA2)
 
An interactive WebGL scene built with **React Three Fiber**, **@react-three/drei**,
and **leva**: drag-and-drop `.glb` loading with auto-staged lighting/shadows
and a live material configurator, shipped as its own lazy-loaded route so
the rest of the site never pays for it.
 
Live demo: [`/lab/3d`](https://priscila-portfolio.vercel.app/lab/3d)
Full write-up: [`week-seven/THREE_D_EXPERIENCE.md`](./week-seven/THREE_D_EXPERIENCE.md)
 
## What it does
 
- Loads a small default sample model (Khronos' `Duck.glb`, ~120KB) so the
  scene is never empty.
- Drag any `.glb` file onto the canvas to replace the loaded model — handled
  with `URL.createObjectURL` on the dropped file, revoked on swap/unmount.
- **Interaction beyond orbiting:** a `leva` panel lets a visitor change base
  color, metalness, roughness, toggle wireframe, switch environment presets
  (city / sunset / warehouse / forest / studio), and control auto-rotate
  speed — applied by traversing the loaded scene graph's materials, so it
  works on any dropped model, not just the default one.
- `drei`'s `<Stage>` auto-centers and auto-scales whatever is loaded and
  adds environment lighting + soft contact shadows.
- `OrbitControls` handles orbit/zoom/pan, including touch gestures on
  mobile, without extra code.
## Loading responsibly
 
- **Lazy-loaded canvas.** The route imports the viewer through
  `next/dynamic({ ssr: false })`, so three.js + fiber + drei + leva
  (~600KB combined) only load on `/lab/3d`, never on any other page.
- **Fallback-first, opt-in canvas.** The page opens on a static, motion-free
  card until the visitor taps "Enable 3D view," or the device auto-qualifies
  (WebGL present, no `prefers-reduced-motion`, no low-power/data-saver
  signal from `navigator.connection` / `navigator.deviceMemory`).
- **Small default asset + capped renderer settings**: `dpr={[1, 1.5]}` and
  `powerPreference: "low-power"` instead of the device's full pixel ratio.
## Perf note
 
Default model transfer is ~120KB; the 3D vendor bundle itself
(three.js + fiber + drei + leva) is the larger cost at ~600KB uncompressed,
which is why it's gated behind the fallback instead of loading on page
visit. On a throttled mid-tier mobile profile (4x CPU, Fast 3G), frame rate
holds around 45–55fps during interaction; contact shadows were the biggest
cost found during the FE-10 pass — see the full numbers and methodology in
[`week-seven/THREE_D_EXPERIENCE.md`](./week-seven/THREE_D_EXPERIENCE.md).
 
## What I'd add with more time
 
- Compress and self-host a heavier default model with DRACO instead of
  relying on an already-tiny sample asset.
- A low-power toggle inside the configurator itself (drop shadows, cap DPR
  further) rather than only gating at the "enable the canvas" level.
- Swap the default Duck for a small model tied to one of my other case
  studies, so this isn't a standalone tech demo.
### Version note
 
`@react-three/fiber`'s latest major (v9) requires React 19. This project is
pinned to React 18 (Next.js 14's peer requirement), so dependencies are
installed as `@react-three/fiber@^8` and `@react-three/drei@^9` to stay
compatible — see Setup below.
 
## Setup
 
```bash
npm install three @react-three/fiber@^8 @react-three/drei@^9 leva
npm install -D @types/three
```
 
No environment variables are required; the viewer runs entirely client-side.
 
