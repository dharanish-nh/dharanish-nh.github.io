# CLAUDE.md — AI Assistant Guide for dharanish-nh.github.io

This file describes the codebase structure, conventions, and workflows for AI assistants working on this repository.

## Repository Overview

Personal portfolio website for Dharanish NH — a Robotics Engineer & 3DCP Operations Lead. Built as a pure static site with no build toolchain, no dependencies, and no frameworks. Deployed via GitHub Pages.

**Live site:** `https://dharanish-nh.github.io`

## File Structure

```
dharanish-nh.github.io/
├── index.html                  # Main portfolio page (landing/hub)
├── style.css                   # Single global stylesheet (~1550 lines)
├── care-robot-navigation.html  # Project detail: Care Robot Navigation
├── obstacle-detection.html     # Project detail: Dynamic Obstacle Detection
├── product-development.html    # Project detail: IoT/Robotics Portfolio
├── 404.html                    # GitHub Pages not-found page (small inline <style>)
├── favicon.svg                 # Site icon
├── robots.txt                  # Allows all crawlers, points to sitemap
├── sitemap.xml                 # Lists index + the three project pages
└── readme.md                   # Minimal readme (just the site URL)
```

There is no `src/` folder, no `dist/`, no `node_modules/`, no build output. What you see is what gets served.

## Technology Stack

| Layer | Choice |
|-------|--------|
| Markup | HTML5 (semantic) |
| Styling | CSS3 with CSS custom properties |
| Scripting | Vanilla JavaScript (ES6+) |
| Fonts | Google Fonts — Inter (weights 300–700) via CDN |
| Icons | Font Awesome 6.0.0 via CDN |
| Build | None — static files only |
| Hosting | GitHub Pages (branch: `main`) |

No npm, no Webpack, no Vite, no React, no TypeScript. Do not introduce them unless explicitly asked.

## Styling Conventions

### CSS Architecture

All styles live in `style.css`. There are no component-level CSS files or CSS modules.

**Theme system** uses CSS custom properties defined on `:root` (light mode) and `[data-theme="dark"]`:

```css
:root {
  --primary-color: #0284C7;
  --primary-dark: #0369A1;
  --accent-color: #059669;
  --bg-primary: #F8FAFF;
  --text-primary: #0C1A2E;
  --glass-bg: rgba(248, 250, 255, 0.15);
  --glass-border: rgba(2, 132, 199, 0.2);
  --border-radius: 0.5rem;
  --transition: all 0.4s cubic-bezier(0.25, 0.46, 0.45, 0.94);
  --blur: blur(10px);
  /* ... */
}

[data-theme="dark"] {
  --primary-color: #38BDF8;
  --accent-color: #34D399;
  --bg-primary: #020B18;
  /* ... */
}
```

Other variables: `--secondary-color`, `--text-secondary`, `--text-light`, `--bg-secondary`, `--bg-tertiary`, `--border-color`, `--shadow-sm/md/lg`, `--shadow-glass`.

Always use these variables — never hardcode colors directly in rules.

**Design language:** Glassmorphism — semi-transparent cards with `backdrop-filter: blur()`, layered shadows, and subtle borders.

**Responsive breakpoints:**
- `768px` — tablet/mobile split (primary breakpoint)
- `480px` — small mobile
- `1024px` — wide layout adjustments

### Animation Patterns

The site uses named keyframes for reusable animations:

- `fadeInUp` — scroll-triggered section reveals
- `liquidFlow`, `liquidBounceIn`, `liquidPopIn` — entrance animations
- `liquidShine` — hover shine sweep effect
- `float` — hover float on skill tags (injected from JS in `index.html`, not in `style.css`)
- `blink` — cursor blink in typing effect

Scroll-triggered animations use the Intersection Observer API (threshold `0.1`), adding the `animate-in` class when elements enter the viewport.

## JavaScript Conventions

All JavaScript is inline `<script>` tags at the bottom of each HTML file. There are no external `.js` files.

**Key patterns used:**

1. **Theme toggle** — reads/writes `localStorage` key `theme`, applies `data-theme` attribute on `<html>`
2. **Mobile nav** — below 768px the `.nav-menu` is off-screen; the `.hamburger` button (present on `index.html` and every project page) toggles `.active` on both, updates `aria-expanded`, and closes on Escape
3. **Smooth scroll** — `scrollIntoView({ behavior: 'smooth' })` on nav link clicks
4. **Intersection Observer** — triggers CSS class additions for scroll animations
5. **Parallax** — scroll handler adjusts hero `transform` (index only)
6. **Navbar blur** — `requestAnimationFrame`-throttled scroll handler drives dynamic `backdrop-filter` intensity (index only)
7. **Typing effect** — hero subtitle is typed out character by character (index only)

Keep JavaScript minimal and side-effect free. Avoid global variable pollution — prefer `const`/`let` inside event listeners and IIFE wrappers.

## HTML Conventions

- Use semantic elements: `<nav>`, `<section>`, `<article>`, `<header>`, `<footer>`, `<main>`
- Each `<section>` has an `id` used for anchor navigation (e.g. `id="about"`, `id="projects"`)
- Navigation links use `href="#section-id"` for same-page sections
- Project detail pages open in a new tab from `index.html`: each `.project-card` contains a `.project-link` with `target="_blank"`, and `.project-link::after` stretches over the card so the whole card is clickable (no `onclick` handlers)
- Project detail pages include a `.back-button` that calls `history.back()`, falling back to `index.html` when there is no history (e.g. opened in a new tab)
- Add `aria-label` on icon-only buttons and links for accessibility

## Page Structure Pattern

All pages share the same structure:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <!-- Meta, title, Google Fonts, Font Awesome, style.css link -->
</head>
<body>
  <nav class="navbar">...</nav>
  <section id="...">...</section>
  <!-- more sections -->
  <footer>...</footer>
  <script>/* inline JS */</script>
</body>
</html>
```

Project detail pages follow a consistent internal structure:
1. Back button (`.back-button`)
2. Navbar with links back to `index.html` sections, theme toggle and hamburger
3. Hero (`<header class="project-hero">`: title + one-line subtitle)
4. `<main class="project-content">` with `.project-section` blocks (overview, technical implementation with `.tech-stack`, results with `.project-metrics`/`.metric-card`, innovation, context)
5. Footer and inline script (theme toggle, mobile menu, back button, scroll reveal, footer year)

## Content & Data

There is no CMS, database, or data layer. All content is hard-coded in HTML. When updating content (experience, projects, skills), edit the HTML directly.

**Personal info:**
- Name: Dharanish NH
- Email: n.h.dharnish1996@gmail.com
- LinkedIn: `/dharanish-nh/`
- GitHub: `/dharanish-nh`
- Phone: +31 6 17674020 (`tel:+31617674020`)
- Location: Netherlands

## Development Workflow

Since there is no build step, development is straightforward:

1. Edit HTML/CSS/JS files directly
2. Open `index.html` in a browser to preview
3. Use browser DevTools for iteration
4. Commit and push to `main` — GitHub Pages deploys automatically

**No linting, no testing, no CI pipeline exists.** Changes are deployed on push to `main`.

## Git Conventions

- Branch `main` is the production branch (GitHub Pages serves from it)
- Feature work should be done on separate branches and merged via PR
- Commit messages should be descriptive (e.g. `Add dark mode toggle`, `Fix mobile nav overflow`)

## Key Conventions for AI Assistants

1. **Do not introduce build tools, frameworks, or npm** unless explicitly requested
2. **Keep all styles in `style.css`** — do not create additional CSS files
3. **Keep JavaScript inline** in each HTML file's `<script>` block
4. **Use CSS variables** for all color and spacing values, never hardcode
5. **Test both light and dark themes** when changing visual styles
6. **Test responsive behavior** at 480px, 768px, and 1024px breakpoints
7. **Preserve the glassmorphism aesthetic** — maintain `backdrop-filter` and layered shadow patterns
8. **Don't add comments** to code you didn't change; only comment where logic is non-obvious
9. **Mirror the page structure** of existing pages when adding new project detail pages
10. **Don't add dependencies** — CDN links for Google Fonts and Font Awesome are the only external resources
