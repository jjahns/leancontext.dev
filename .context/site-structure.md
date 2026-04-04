---
last-reviewed: 2026-04-04
---
# Site Structure

## Plain HTML — No Build Step

This is a static HTML site served directly from the main branch via GitHub Pages. There is no build step, no static site generator, no framework.

## File Layout

```
/index.html              → landing page (hero, three pillar cards, adoption guide, principles, evidence, CTA)
/problem/index.html      → pillar: the problem (degradation loop, evidence, mechanism)
/framework/index.html    → pillar: the framework (three tiers, four rules, lean ancestry, knowledge boundary, hybrid model)
/impact/index.html       → pillar: the human impact (amplification loop, calibration, session discipline, sustain, the bet)
/thesis/index.html       → the full thesis (long-form article)
/blog/index.html         → blog (placeholder)
/about/index.html        → about the author and the standard
/css/style.css           → shared stylesheet
/404.html                → 404 page
```

## Navigation

All pages share the same nav: Problem | Framework | Impact | Thesis | Blog | GitHub.
All footers include an About link.
The nav uses relative paths. Active page gets `class="active"`.

## Relative Paths

All links use relative paths. From the root: `problem/`, `framework/`, `thesis/`, `css/style.css`. From subpages: `../`, `../problem/`, `../framework/`, `../css/style.css`.

No absolute paths. No build-time URL resolution. Everything works at any deployment path.

## GitHub Pages

- Source: Deploy from branch → main → / (root)
- No GitHub Actions workflow needed.
- Custom domain: add CNAME file to root when ready.
