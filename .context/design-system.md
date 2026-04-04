---
last-reviewed: 2026-04-04
---
# Design System

## Colors

- Background: `#09090b` (--bg)
- Card surface: `rgba(255,255,255,0.03)` (--bg-card)
- Card border: `rgba(255,255,255,0.06)` (--bg-card-border)
- Text: `#a1a1aa` (--fg)
- Bright text: `#fafafa` (--fg-bright)
- Muted text: `#52525b` (--fg-muted)
- Accent (links, stats): `#3b82f6` (--accent)
- Border: `rgba(255,255,255,0.06)` (--border)

## Pastel Palette (pillar pages)

- Problem: coral `#ff9b8e` (--pastel-coral), dim 12%, glow 6%
- Framework: mint `#7edcb5` (--pastel-mint), dim 12%, glow 6%
- Impact: violet `#b4a0f4` (--pastel-violet), dim 12%, glow 6%

Each pillar color has three opacity variants: full color for tags/text, dim for callout backgrounds, glow for illustration containers.

## Typography

- Sans: `Inter` (--sans), used for headings, nav, body on landing/pillar pages
- Serif: `Newsreader` (--serif), used for thesis article body and callout text
- Mono: `JetBrains Mono` (--mono), used for tags, code, labels, SVG text

## Layout

- Max content width: `72rem` (--max), used for landing and pillar sections
- Article width: `48rem`, used for thesis long-form reading
- Mobile breakpoint: `768px`

## SVG Graphics

- All inline SVG, no external images
- Text uses JetBrains Mono, 10-13pt range
- Shapes use pillar pastel colors with fill-opacity 0.1-0.5 and stroke-opacity 0.5-0.8
- Stroke-width: 2px for shapes, 1.5px for connectors
- ViewBoxes cropped tight to content — avoid dead whitespace

## Pillar Page Structure

- `.pillar-hero`: split grid (text + graphic), used for page heroes
- `.pillar-section`: full-width content section with border-top separator
- `.pillar-section--split` / `--split-reverse`: two-column layout with visual
- `.pillar-callout-section`: standalone full-width callout (never inside a split)
- `.pillar-steps`: grid of numbered step cards
- `.pillar-code`: full-width code block

## Principles

- Dark background throughout. No light mode.
- Inline SVG illustrations on pillar pages. Pastel accents pop against dark.
- No JavaScript. The site is fully static HTML+CSS.
- Navigation: sans-serif, small, top-aligned. Does not compete with content.
- Callouts never live inside split sections — always standalone below.
