# TechWrap Solution — Website

React + TypeScript + Vite. Plain CSS per component (no Tailwind/Bootstrap
dependency), organized so each section of the Home page is a self-contained,
reusable component.

## Run it

```bash
npm install
npm run dev       # http://localhost:5173
npm run build      # production build to /dist
```

## Structure

```
src/
  components/
    Navbar/            Navbar.tsx + Navbar.css
    Hero/               entrance-animated hero, headline + 2 CTAs + visual panel
    TrustedBy/          logo strip
    Services/           6 capability cards, hover animation
    ProductPreview/      product mockup + floating stat chips
    Testimonials/       4-video slider (see below)
    FinalCTA/            closing conversion band
    Footer/
    Reveal/              generic scroll-reveal wrapper used across sections
  hooks/
    useScrollReveal.ts        IntersectionObserver-based reveal-on-scroll
    usePrefersReducedMotion.ts follows the OS reduced-motion setting
    useTestimonialSlider.ts    slider state: index, autoplay, pause-on-interact
  pages/
    Home.tsx             composes the sections above, in spec order
  styles/
    variables.css         design tokens (color, type, spacing, motion)
    global.css             resets + reveal/hero keyframes
    buttons.css            shared .tw-btn primitives
  types/
    index.ts               shared content types (ServiceItem, TestimonialItem, ...)
```

## Testimonials video slider

Exactly 4 slides, defined in `Testimonials.tsx`. Behavior:

- Auto-advances every 7s (`useTestimonialSlider`), pauses on hover, focus,
  or touch, and resumes after.
- Prev/next arrow buttons and pagination dots, both keyboard-accessible.
- Swipe left/right on touch devices.
- Only the active slide's `<video>` plays; the rest pause and reset.
- Respects `prefers-reduced-motion`: autoplay is skipped entirely (manual
  navigation still works) and the slide-change transition is disabled.

**Add your real videos**: drop 4 files into `public/videos/` following the
naming convention in `public/videos/README.md`, then update the `name`,
`role`, `company`, and `quote` fields in `Testimonials.tsx`.

## Animation approach

- Hero uses a CSS keyframe entrance (staggered via `--d` custom property)
  so the first paint isn't blocked by JS/observer setup.
- Every other section uses the shared `<Reveal>` component — wraps any
  block, fades/translates it in once it's ~20% in the viewport.
- All animation is wrapped in `@media (prefers-reduced-motion: no-preference)`
  or gated through `usePrefersReducedMotion()`, so motion-sensitive users
  get an instantly-visible, static page instead.

## What reused what

`Navbar`, `Footer`, design tokens, and button styles were kept as-is from
the existing architecture. `Reveal` and the reduced-motion hook are new
shared primitives — every new section (Services, ProductPreview,
Testimonials, FinalCTA) uses them instead of writing its own scroll/motion
logic, so future pages can reuse the same building blocks.
