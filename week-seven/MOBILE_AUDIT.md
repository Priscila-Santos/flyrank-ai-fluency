# Mobile Audit — Fix Log

## Verified on
- Real device: [ Moto G Power ], [navegador]
- Desktop: [navegador], widths tested: 375px / 768px / 1280px+

## Issues found and fixed

### 1. Leva configurator panel overlapping site navigation (3D Lab)
**Before:** `<Leva />` renders as a fixed-position overlay with a very high
z-index, so opening the configurator places it on top of the header nav,
both collapsed and expanded.
**After:** Switched to `<Leva fill />` inside a positioned container scoped
to the 3D viewer box, with `z-20` (below the header's `z-50`), so the panel
never leaves the viewer area.

### 2. No mobile navigation menu
**Before:** The nav rendered all six links as a flat horizontal list with no
collapse, forcing wrapping/crowding on narrow screens.
**After:** Added a hamburger-triggered mobile menu (`components/mobile-nav.tsx`),
shown below `sm` breakpoint; horizontal list kept for `sm:` and up.

### 3. AI assistant chat felt cramped on desktop
**Before:** Chat container capped at `max-w-3xl`.
**After:** Widened to `max-w-4xl`; no effect on mobile widths.

### 4. Playground and 3D Lab pages lacked context
**Before:** Visitors reported not understanding what the Playground page
demonstrated (see week-five/FEEDBACK.md).
**After:** Added a one-paragraph purpose statement under each page's `<h1>`.

## Confirmed working (no changes needed)
- All navigation links, demo link, GitHub repo link, and Contact links
  tested and functional on mobile.
- No oversized/unoptimized images currently on the site (no real project
  screenshots have been added yet — tracked separately, see Still Ugly List).
- No layout breaks at 375px, 768px, or 1280px+.
- Text size and contrast readable on a real device screen.
