---
name: landing-page-seo
description: Generates a complete SEO plan for a landing page before code is written. Covers site architecture, meta tags per page, schema markup types, XML sitemap config, robots.txt, and AI search optimization hints. Call after landing-page-copy and before building. Input: company_name, industry, pages list, content_brief (from landing-page-copy).
---

# Landing Page SEO Planner

Produce a **structured SEO plan** covering all pages. No code — just the SEO configuration ready to be injected into `app/layout.tsx`, `app/sitemap.ts`, `app/robots.ts`, and per-page metadata.

## Inputs

- `{company_name}` — brand name
- `{industry}` — sector
- `{pages}` — list of pages to cover (default: `/`, `/tarifs`, `/a-propos`, `/blog`, `/cas-clients`)
- `{content_brief}` — hero headline, features copy, etc. from landing-page-copy

---

## Step 1 — Site Architecture Validation

Evaluate whether the default page set is optimal for this industry. Ask:

1. Does this industry need a `/contact` or `/demo` standalone page? (B2B SaaS → yes)
2. Are there high-intent keyword pages missing? (e.g. `/comparatif`, `/alternatives-[competitor]`, `/[use-case]`)
3. Should `/blog` be `/ressources` or `/guides` for this industry?
4. Does the pricing page need its own `/tarifs` or is it a `#pricing` anchor sufficient?

Output a **final page list** with URL, title, and purpose. Default:

| URL | Page Title | Purpose |
|-----|-----------|---------|
| `/` | Homepage | Conversion — hero + proof + features |
| `/tarifs` | Tarifs | Plan comparison, upgrade intent |
| `/a-propos` | À propos | Trust building, brand story |
| `/blog` | Blog | SEO organic traffic |
| `/cas-clients` | Cas clients | Trust + decision stage |

Add up to 2 additional pages if strongly justified by industry.

---

## Step 2 — Meta Tags Per Page

For each page, generate:

```
page:         [url]
title:        [50-60 chars. Primary keyword near front. Brand at end after " | "]
description:  [140-160 chars. Action verb. Benefit. Soft CTA. Includes primary keyword.]
og_title:     [Same as title or slightly more engaging]
og_description: [Same as description or conversational variant]
keywords:     [3-5 primary keywords for this page]
```

**Title tag rules:**
- Homepage: `{Primary keyword} | {Company Name}` or `{Company Name} — {Core value proposition}`
- Feature/pricing: `{Feature/Pricing} {Company} — {Benefit}`
- Blog: `{Article Title} | {Company Blog}`
- 50-60 chars to avoid SERP truncation

**Meta description rules:**
- Unique per page — no copy-paste
- Include primary keyword naturally
- Soft CTA: "Découvrez", "Commencez gratuitement", "Comparez les plans"
- 140-160 chars max

---

## Step 3 — Schema Markup Plan

For each page, specify which JSON-LD schemas to implement:

### Homepage (`/`)
```json
Organization {
  name, url, logo, description,
  sameAs: [social profile URLs — LinkedIn, Twitter/X, GitHub if relevant],
  contactPoint: { contactType: "customer service", availableLanguage: "French" }
}
WebSite {
  name, url,
  potentialAction: SearchAction (if site has search)
}
```

### Pricing (`/tarifs`)
```json
// For each plan:
Offer {
  name, price, priceCurrency, description,
  seller: { "@type": "Organization", name }
}
FAQPage (if pricing FAQ section exists) {
  mainEntity: [{ "@type": "Question", name, acceptedAnswer }]
}
```

### Blog post pages
```json
Article {
  headline, datePublished, dateModified,
  author: { "@type": "Organization", name },
  publisher: { "@type": "Organization", name, logo },
  image, description
}
BreadcrumbList {
  itemListElement: [Home → Blog → Article]
}
```

### Case study pages
```json
Article (type: "TechArticle" or generic)
BreadcrumbList {
  itemListElement: [Home → Cas clients → Case name]
}
```

### About (`/a-propos`)
```json
AboutPage {
  name, description, url,
  mainEntity: Organization (same as homepage)
}
```

**SoftwareApplication schema** — add to homepage if industry is SaaS/tech:
```json
SoftwareApplication {
  name, applicationCategory: "BusinessApplication",
  operatingSystem: "Web",
  offers: { price, priceCurrency }
}
```

---

## Step 4 — XML Sitemap Configuration

```ts
// sitemap.ts entries
pages: [
  { url: "/",            changeFreq: "weekly",  priority: 1.0 },
  { url: "/tarifs",      changeFreq: "weekly",  priority: 0.9 },
  { url: "/a-propos",    changeFreq: "monthly", priority: 0.6 },
  { url: "/blog",        changeFreq: "daily",   priority: 0.8 },
  { url: "/cas-clients", changeFreq: "monthly", priority: 0.7 },
  // blog posts:       changeFreq: "monthly",  priority: 0.6
  // case studies:     changeFreq: "monthly",  priority: 0.7
]
```

Adjust priorities based on conversion intent:
- High conversion intent pages (/, /tarifs): 0.9-1.0
- Trust / middle funnel (/cas-clients): 0.7-0.8
- Top funnel / content (/blog, /a-propos): 0.5-0.7

---

## Step 5 — Core Web Vitals Hints

Based on the planned components, flag these build-time optimizations:

**Images:**
- Use `next/image` with `priority={true}` on hero image
- WebP format via `unoptimized: true` (static export) + pre-optimized assets
- Explicit `width` and `height` to prevent CLS

**Fonts:**
- Use `next/font/google` with `display: 'swap'`
- Add `preconnect` to `fonts.googleapis.com` in `<head>`

**CSS:**
- Use CSS vars — avoids class-name recomputation on theme switch
- Avoid `@import` in CSS (use `next/font` instead)

**JavaScript:**
- `'use client'` only on interactive components (DarkModeToggle, ContactForm, Pricing toggle)
- Static sections (Hero text, Features, Footer) = Server Components = no JS bundle cost

---

## Step 6 — AI SEO (Citability) Hints

For AI Overviews, Perplexity, ChatGPT to cite this site:

**Structure hints for the code generation phase:**
- FAQ section must use `<dl>` or semantic Q/A HTML (not just div/p)
- Stats in the hero should have `<data value="X">` wrapping
- Blog articles need `<article>` with `<time datetime="...">` and clear `<h1>`
- Case studies need explicit "Challenge / Solution / Result" H2 structure
- About page needs `<address>` for company contact info

**Content hints:**
- Include 1-2 FAQ answers that directly answer "What is {company_name}?" and "How does {company_name} work?" (these get cited directly by AI)
- Blog articles should have a TL;DR summary in the first paragraph
- Stats must include source/context (even if internal: "based on customer data")

---

## Output Format

Output the plan as `SEO_PLAN`:

```markdown
## SEO_PLAN

### page_list
[Final page list with any additions]

### meta_tags
[Per-page title, description, og_title, og_description, keywords]

### schema_plan
[Per-page schema types + key fields]

### sitemap_config
[Priority + changeFreq per page]

### build_hints
[CWV + AI SEO hints to pass to code generation]
```
