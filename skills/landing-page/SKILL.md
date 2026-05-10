---
name: landing-page
description: Build a complete custom Next.js landing page from scratch. Trigger when user types `/landing-page`. Format: `/landing-page CompanyName | industry [| tagline] [| target] [| CTA]`. Orchestrates landing-page-copy → landing-page-seo + ui-ux-pro-max in parallel → build.
---

# Landing Page — Orchestrator

Output directory: `./generated/{slug}-landing` (relative to current working directory, or configure via `LANDING_PAGE_OUTPUT_DIR` env var)

---

## Step 1 — Parse Input

Split args on `|` (trim):
- `[0]` → `company_name` (if missing, invent a creative, sector-appropriate name)
- `[1]` → `industry` (infer if missing)
- `[2]` → `tagline_hint` (optional)
- `[3]` → `target_audience` (optional)
- `[4]` → `cta_goal` (optional, default: "Commencer gratuitement")

`slug` = company_name lowercased, accents stripped, spaces → hyphens.

Resolve output path:
```bash
OUTPUT_DIR="${LANDING_PAGE_OUTPUT_DIR:-./generated}/{slug}-landing"
```

---

## Step 2 — Content Brief (landing-page-copy)

Invoke the **landing-page-copy** skill with the parsed inputs. Wait for the full `CONTENT_BRIEF` output before proceeding.

The brief contains: hero copy, features, testimonials, FAQ, about, pricing plans, blog articles, case studies, navigation labels. **This copy is the source of truth for all text in the generated site — do not invent copy during code generation.**

---

## Step 3 — Design + SEO (run in parallel)

### 3-A. Design Intelligence (ui-ux-pro-max)

Run these 3 commands in parallel:

```bash
python3 ~/.claude/skills/ui-ux-pro-max/scripts/search.py "{industry}" --design-system -p "{company_name}"
python3 ~/.claude/skills/ui-ux-pro-max/scripts/search.py "{industry}" --domain color -n 3
python3 ~/.claude/skills/ui-ux-pro-max/scripts/search.py "{industry} landing page" --domain typography -n 3
```

> **Note**: requires the [ui-ux-pro-max skill](https://github.com/anthropics/claude-code/tree/main) to be installed. If unavailable, synthesize a design brief directly from industry + brand context.

Synthesize into a **design brief**:
- Visual mood (3-5 adjectives)
- Color palette: primary, secondary, accent, bg, bg-soft, bg-card, surface, text, text-muted, text-inverse, border, border-subtle, dot
- Typography: heading font + body font (must exist in Google Fonts)
- Layout decisions: hero type, features layout, testimonials style
- Animation style: subtle / moderate / expressive
- Signature detail: one distinctive visual element that makes this page memorable

### 3-B. SEO Plan (landing-page-seo)

Invoke the **landing-page-seo** skill with company_name, industry, and the CONTENT_BRIEF. Wait for the full `SEO_PLAN` output.

The plan contains: meta tags per page, schema markup types, sitemap config, Core Web Vitals build hints, AI SEO hints.

---

## Step 4 — Write All Files

Create `{OUTPUT_DIR}`, then write every file from scratch using the Write tool. Use CONTENT_BRIEF for all copy and SEO_PLAN for all meta/schema. No templates, no placeholder text.

### 4-A. Infrastructure

**`package.json`**:
```json
{
  "name": "{slug}-landing",
  "version": "0.1.0",
  "private": true,
  "scripts": { "dev": "next dev", "build": "next build", "start": "next start" },
  "dependencies": {
    "next": "14.2.0",
    "react": "^18",
    "react-dom": "^18",
    "lucide-react": "^0.378.0",
    "framer-motion": "^11.0.0"
  },
  "devDependencies": {
    "@next/mdx": "^14.2.0",
    "@mdx-js/loader": "^3.0.0",
    "@mdx-js/react": "^3.0.0",
    "@types/mdx": "^2.0.0",
    "typescript": "^5",
    "@types/node": "^20",
    "@types/react": "^18",
    "@types/react-dom": "^18",
    "tailwindcss": "^3.4.1",
    "postcss": "^8",
    "autoprefixer": "^10.0.1"
  }
}
```

**`next.config.js`**:
```js
const withMDX = require('@next/mdx')()
module.exports = withMDX({
  output: 'export',
  images: { unoptimized: true },
  pageExtensions: ['js', 'jsx', 'mdx', 'ts', 'tsx'],
})
```

**`tsconfig.json`**: standard Next.js tsconfig with `@/*` path alias for `./`

**`tailwind.config.ts`**:
- `darkMode: 'class'`
- `fontFamily`: heading + body CSS vars
- `animation`: `marquee`, `float`, `line-grow`
- `keyframes`: marquee `translateX(0%)→translateX(-50%)`, float, lineGrow

**`postcss.config.js`**: tailwind + autoprefixer

**`mdx-components.tsx`**: map `h1 h2 h3 p ul li strong blockquote` to styled elements using CSS vars

**`app/globals.css`**:
- `@tailwind base/components/utilities`
- CSS vars in `:root` — all design tokens from the design brief
- `html.dark { ... }` — override ALL vars for dark mode
- `body` using CSS vars, with transition on background-color + color
- Custom utilities: `.text-gradient`, `.dot-grid`, `.glass-card`, `.bracket-card`, `.clip-diagonal-bottom`, `.clip-diagonal-top`
- **RULE: every color reference in components uses CSS vars — zero hardcoded hex**

---

### 4-B. Components

Write each as `.tsx`. Every component reads copy from `lib/data.ts`. All colors via CSS vars.

**`components/DarkModeToggle.tsx`**:
- `'use client'`, reads `localStorage` on mount, toggles `dark` class on `<html>`
- Persists preference, Sun/Moon icons from lucide-react

**`components/LogoCarousel.tsx`**:
- `'use client'`, pure CSS marquee (`animate-marquee`), no JS scroll
- Brand names from `lib/data.ts`, duplicated for seamless loop

**`components/ContactForm.tsx`**:
- `'use client'`, state: `'idle' | 'sending' | 'sent' | 'error'`
- `<form data-netlify="true" name="contact">` with hidden `form-name`
- Fields: name, email, company, message
- Submit: `fetch('/', { method: 'POST', body: new URLSearchParams(new FormData(form)) })`

**`components/Navbar.tsx`**: fixed, scroll-aware, mobile menu, `<DarkModeToggle />`

**`components/Hero.tsx`**:
- Framer Motion enter animation with `useReducedMotion` check
- Stats row with `<data value="{stat.value}">` (AI SEO)
- Signature design detail from design brief

**`components/Features.tsx`**: `whileInView` Framer Motion, staggered delay

**`components/Testimonials.tsx`**: `whileInView` Framer Motion per card

**`components/FAQ.tsx`**:
- `'use client'`, accordion
- `<dl>/<dt>/<dd>` semantic markup (FAQPage schema + AI SEO)

**`components/Pricing.tsx`**:
- `'use client'`, monthly/annual billing toggle with −20% badge
- `price` vs `price_annual` switch

**`components/About.tsx`**: story + values

**`components/Footer.tsx`**: tagline + nav + copyright

**Component rules:**
- `'use client'` only on interactive/browser-API components
- `import type { X }` for type-only imports
- Dynamic lucide icons: `(Icons as any)[name] as React.ComponentType<{size?:number;strokeWidth?:number}> | undefined`
- All colors via CSS vars — zero hardcoded hex
- Framer Motion for animations (no IntersectionObserver)
- All text from `lib/data.ts`

---

### 4-C. Pages

**`app/layout.tsx`**:
- Google Fonts via `next/font/google`
- Metadata from SEO_PLAN homepage meta
- JSON-LD: Organization + WebSite (or SoftwareApplication) from SEO_PLAN
- Dark mode anti-flash in `<head>`:
  ```js
  try{if(localStorage.getItem('theme')==='dark'||(localStorage.getItem('theme')===null&&window.matchMedia('(prefers-color-scheme: dark)').matches)){document.documentElement.classList.add('dark')}}catch(e){}
  ```
- `suppressHydrationWarning` on `<html>`
- `<link rel="preconnect" href="https://fonts.googleapis.com">`

**`app/page.tsx`**: Navbar + Hero + LogoCarousel + Features + Testimonials + Pricing + FAQ + ContactForm + Footer

**`app/tarifs/page.tsx`**: Metadata + JSON-LD (Offer + FAQPage) from SEO_PLAN + Navbar + Pricing + FAQ + ContactForm + Footer

**`app/a-propos/page.tsx`**: Metadata + JSON-LD (AboutPage + Organization) + Navbar + About + Footer

**`app/blog/layout.tsx`** + **`app/blog/page.tsx`**: listing from `lib/blog.ts`

**`app/blog/{slug-1}/page.mdx`** + **`app/blog/{slug-2}/page.mdx`**:
- Content from CONTENT_BRIEF `blog[]`
- `<article>` + `<time datetime="...">` semantic wrap
- TL;DR in first paragraph (AI SEO citability)

**`app/cas-clients/layout.tsx`** + **`app/cas-clients/page.tsx`**: listing from `lib/case-studies.ts`

**`app/cas-clients/{slug-1}/page.mdx`** + **`app/cas-clients/{slug-2}/page.mdx`**:
- Content from CONTENT_BRIEF `case_studies[]`
- Explicit `## Challenge / ## Solution / ## Résultats` H2 structure (AI SEO)
- BreadcrumbList JSON-LD from SEO_PLAN

**`app/sitemap.ts`**: SEO_PLAN `sitemap_config` for all pages + posts + case studies

**`app/robots.ts`**: allow all, sitemap URL

---

### 4-D. Data Files

**`lib/data.ts`** — all copy from CONTENT_BRIEF:
```ts
export const siteData = {
  nav: { links: [...], cta: "..." },
  hero: { badge, headline_a, subheadline, cta_primary, cta_secondary, social_proof_bar, stats },
  logoCarousel: { section_label, brands: [...] },
  features: { badge, heading, sub, items: [...] },
  testimonials: [...],
  faq: { items: [...] },
  about: { headline, story, values: [...] },
  pricing: { heading, sub, plans: [...] },
  contact: { heading, sub, form_cta },
  footer: { tagline }
}
```

**`lib/blog.ts`**: blog registry from CONTENT_BRIEF

**`lib/case-studies.ts`**: case study registry from CONTENT_BRIEF

**Hero image** — pick from these Unsplash placeholders:
- Tech/SaaS/Fintech: `https://images.unsplash.com/photo-1518770660439-4636190af475?w=1600&q=80`
- Health/Wellness: `https://images.unsplash.com/photo-1571019613454-1cb2f99b2d8b?w=1600&q=80`
- Food/Bio: `https://images.unsplash.com/photo-1504674900247-0877df9cc836?w=1600&q=80`
- Sport/Fitness: `https://images.unsplash.com/photo-1517836357463-d25dfeac3438?w=1600&q=80`
- Fashion/Luxury: `https://images.unsplash.com/photo-1558618666-fcd25c85cd64?w=1600&q=80`
- Real estate: `https://images.unsplash.com/photo-1560518883-ce09059eeffa?w=1600&q=80`
- Education: `https://images.unsplash.com/photo-1522202176988-66273c2fd55f?w=1600&q=80`
- Default: `https://images.unsplash.com/photo-1497366216548-37526070297c?w=1600&q=80`

---

## Step 5 — Build

```bash
cd {OUTPUT_DIR}
npm install
npm run build
```

**TypeScript errors — fix and retry:**
- `import { X }` → `import type { X }` for type-only imports
- Dynamic icon: `(Icons as any)[name] as ComponentType<...> | undefined`
- Fix all TypeScript errors before reporting success

---

## Step 6 — Report

```
🏢  Company:    {company_name} — {industry}
🎨  Design:     {mood} | {heading font} / {body font}
✨  Signature:  {distinctive detail}
📝  Headline:   "{headline_a}"
📄  Pages:      / · /tarifs · /a-propos · /blog · /cas-clients
🔍  SEO:        {page title} | {schema types}
📁  Output:     {OUTPUT_DIR}

Local preview: cd {OUTPUT_DIR} && npm run dev
```
