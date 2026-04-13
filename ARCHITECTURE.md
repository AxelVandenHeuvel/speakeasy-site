# Speakeasy Site — Architecture

## Overview

A single-page static landing site for the Speakeasy macOS app. No build tools, no framework — just one HTML file.

## Structure

```
speakeasy-site/
├── index.html        ← Entire site (HTML + inline CSS + inline JS)
├── ARCHITECTURE.md   ← This file
└── README.md
```

## How It Works

**`index.html`** contains everything:

- **CSS (inline `<style>`)** — All styling including CSS custom properties, animations, and responsive breakpoints
- **HTML** — Two sections: hero (title, waveform, download button) and features (3-column layout)
- **JS (inline `<script>`)** — Generates the waveform visualization bars dynamically (48 bars with bell-curve height distribution and staggered animations)

### Key Visual Elements

| Element | How it works |
|---------|-------------|
| Grain texture | SVG `<feTurbulence>` filter rendered in a fixed-position SVG overlay |
| **Intro scroll cinematic** | 240vh tall `.intro` with sticky inner container; `.scene` scales via scroll progress, zooming through the door into the hero |
| Brick wall | CSS background using a tiled SVG data-URI pattern (staggered brick rectangles) |
| Speakeasy door | Pure CSS — arched top via `border-radius`, two inset panels via `::before` / `::after`, brass peephole + handle + "SPEAKEASY" plaque |
| Lamp glow | Radial gradient with a slow `lamp-pulse` opacity animation |
| Waveform | 48 JS-generated bars animated in a single `requestAnimationFrame` loop — each has its own phase/speed for the ambient pulse, plus a gaussian cursor-proximity boost added on top |
| Gold edge lines | Fixed `<div>` elements with a `linear-gradient` background |
| Entrance animations | CSS `@keyframes fadeUp` / `fadeIn` with incremental `animation-delay` values |
| Ambient glow | `::before` pseudo-element on `.hero` with a radial gradient |

### Scroll animation details

The intro works in three parts:
1. **Scroll container (`.intro`)** — 240vh tall, provides the scroll "runway"
2. **Sticky wrapper (`.intro-sticky`)** — 100vh, `position: sticky`, pins the scene while you scroll
3. **Scene (`.scene`)** — absolute-positioned layers (lamp glow, wall, street, door). Scaled via `transform: scale(...)` on each scroll frame.

Scroll progress (0→1) is computed from `-intro.getBoundingClientRect().top / (intro.offsetHeight - window.innerHeight)`. Scale = `1 + progress^2.1 * 18` (ease-in). Opacity fades to 0 in the last 12% so the hero reveals smoothly. `transform-origin` is set dynamically to the door's center via JS (recomputed on resize), so the camera "walks into" the door regardless of viewport size.

### Fonts

Loaded from Google Fonts:
- **Bodoni Moda** — Display/headings (art deco aesthetic)
- **DM Sans** — Body text

### Download Button

The download link (`<a href="#download">`) is a placeholder. Replace `#download` with the actual DMG URL (e.g., a GitHub Releases link).

## Deployment

Static file — deploy anywhere:
- **GitHub Pages**: Push to `main`, enable Pages in repo settings
- **Netlify / Vercel**: Connect repo, zero config needed
- **Any web server**: Just serve `index.html`
