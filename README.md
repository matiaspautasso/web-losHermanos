# Los Hermanos — Wholesale Distributor Landing Page

> Static landing page for a food wholesale distributor (cold cuts, cheeses & dairy) in Córdoba, Argentina. Built without a framework or build step — pure HTML, Tailwind CSS via CDN, and vanilla JS.

![HTML5](https://img.shields.io/badge/HTML5-E34F26?style=flat&logo=html5&logoColor=white)
![Tailwind CSS](https://img.shields.io/badge/Tailwind_CSS-38B2AC?style=flat&logo=tailwind-css&logoColor=white)
![JavaScript](https://img.shields.io/badge/Vanilla_JS-F7DF1E?style=flat&logo=javascript&logoColor=black)
![Railway](https://img.shields.io/badge/Deployed_on-Railway-0B0D0E?style=flat&logo=railway&logoColor=white)
![Zero build step](https://img.shields.io/badge/build_step-none-brightgreen?style=flat)

---

## Purpose

Small and mid-size businesses often need a professional web presence fast — without the overhead of a CMS, a JS framework, or a build pipeline. This project solves that: a production-ready, mobile-first landing page that a real business can use today, deployed in minutes, with no ongoing infrastructure to maintain.

The client is a wholesale distributor of cold cuts, cheeses, and dairy products serving supermarkets, delis, and food-service businesses in Córdoba, Argentina. The site's primary conversion goal is a WhatsApp contact — not a form, because that's how the market actually closes deals.

---

## What You Get

- **Full-page parallax hero** with a stats bar anchored to the bottom edge, showing value propositions at a glance
- **Multi-section parallax** — each section renders its own background layer at a different scroll speed, creating visual depth across the entire page
- **Fixed glassmorphism navbar** — `backdrop-filter: blur` with brand colors, collapses into a hamburger on mobile
- **Animated WhatsApp CTA** — pulse ring animation draws attention to the primary conversion action without being intrusive
- **Product category grid** — 6 categories (cold cuts, cheeses, dairy, canned goods, other retail) with hover zoom and per-category WhatsApp deeplinks with pre-filled messages
- **About / Story section** — asymmetric image mosaic with badge overlays, benefits grid, and Instagram social proof
- **Fully responsive** — layout, typography, and spacing tuned separately for mobile (375px), tablet (768px), and desktop

---

## Engineering Highlights

### Zero build step, zero dependencies in production
The page ships as a single `index.html` file. Tailwind CSS, Iconify, and Google Fonts load from CDN. There is nothing to compile, bundle, or watch — the dev server is browser-sync running `--server` for live reload. This means the handoff to any non-technical person is a single file.

### Parallax implemented correctly for mobile
`background-attachment: fixed` is hardware-accelerated on desktop but broken on iOS Safari and most Android browsers. Instead, the parallax is implemented by absolutely positioning a `div.bg-img` layer that extends 20% beyond the container in both directions (`top: -20%; bottom: -20%`) and translating it via a `scroll` event listener capped at each section's visible range. This gives smooth parallax on every device without hacks.

```js
// Scroll handler — applies translateY only when section is in viewport
window.addEventListener('scroll', () => {
  parallaxSections.forEach(section => {
    const rect = section.getBoundingClientRect();
    if (rect.bottom < 0 || rect.top > window.innerHeight) return;
    const speed = parseFloat(section.dataset.parallaxSpeed) || 0.3;
    const offset = rect.top * speed;
    section.querySelector('.bg-img').style.transform = `translateY(${offset}px)`;
  });
}, { passive: true });
```

### Custom Tailwind design token system
A brand color palette is defined once inside `tailwind.config` (injected via CDN script tag), with semantic scale keys (`brand-950` → `brand-400`, `brand-gold`, `brand-red`) that map directly to the client's logo colors. This makes global theme changes a one-line edit rather than a find-and-replace across the file.

### `100svh` for iOS viewport consistency
The hero section uses `height: 100svh` on mobile instead of `100vh`. The `svh` (small viewport height) unit accounts for the browser chrome (address bar, bottom tab bar) so the hero never overflows or shows a scroll gap on iOS or Android Chrome.

### WhatsApp deeplinks with pre-filled intent per category
Each product category card links directly to WhatsApp with a URL-encoded message pre-filled to that specific category (e.g. `?text=Hola%2C%20quiero%20consultar%20por%20fiambres`). This reduces friction from tap to conversation to near zero — the user never has to type.

### `{ passive: true }` on scroll listeners
The scroll listener for parallax is registered with `{ passive: true }`, which tells the browser it will never call `preventDefault()` on the event. This lets the browser skip waiting for the JS thread before compositing the next frame — avoiding the main cause of scroll jank on mobile.

---

## Demo Execution Architecture

```
┌─────────────────────────────────────────────────────────┐
│                       Browser                           │
│                                                         │
│  index.html                                             │
│  ├── <script> tailwind.config (brand tokens inline)     │
│  ├── Tailwind CSS CDN  ──────────────► utility classes  │
│  ├── Iconify CDN  ───────────────────► SVG icons        │
│  ├── Google Fonts CDN  ──────────────► Inter typeface   │
│  ├── /assets/  ──────────────────────► logo PNG         │
│  └── /imagenes/  ────────────────────► product photos   │
└─────────────────────────────────────────────────────────┘

Dev environment:
  npm run dev  →  browser-sync --server --files "*.html" --port 3000
                  (auto-reload on any HTML save)

Production:
  Railway detects package.json → runs npm start
  npm start    →  npx serve . --listen $PORT
                  (static file server, no Node.js app)
```

No database. No backend. No environment variables needed locally. Clone → `npm run dev` → done.

---

## Use Cases

| Who | What they get |
|-----|---------------|
| **Wholesale distributors / food businesses** | A conversion-focused landing page with direct WhatsApp contact per product line |
| **Freelance / agency developers** | A reference for parallax without `background-attachment: fixed`, custom Tailwind tokens via CDN, and mobile viewport fixes |
| **Frontend learners** | A real production site that uses zero frameworks — good for understanding what HTML, CSS, and 50 lines of JS can actually do |
| **Clients with limited budgets** | A site that can be hosted for free or near-free (Railway free tier, GitHub Pages, Netlify) with no CMS or server costs |

---

## Short LinkedIn Description

> Built a production landing page for a wholesale food distributor in Córdoba — no framework, no build step, no backend. Just HTML, Tailwind CSS via CDN, and vanilla JS.
>
> The interesting constraint: parallax that actually works on iOS. `background-attachment: fixed` is broken on mobile Safari, so I implemented it by translating an oversized background div on scroll with a passive listener — smooth on every device.
>
> WhatsApp deeplinks with pre-filled messages per product category bring the user from tap to live sales conversation in one step. Deployed to Railway in under 5 minutes.
>
> Stack: HTML · Tailwind CSS (CDN) · Vanilla JS · Railway

---

## Local Setup

```bash
git clone <repo-url>
cd web-losHermanos
npm install
npm run dev
# → http://localhost:3000
```

No `.env` file needed. No build step.

---

## Deployment

The site is deployed on Railway using `npx serve` as the static server. Railway auto-detects `package.json` and runs the `start` script. The `$PORT` environment variable is injected automatically.

```json
"start": "npx serve . --listen $PORT"
```
