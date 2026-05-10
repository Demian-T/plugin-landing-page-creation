# plugin-landing-page-creation

A Claude Code plugin that generates complete, conversion-optimized Next.js landing pages from a single prompt.

## What it does

Type `/landing-page CompanyName | industry` and Claude will:

1. **Write conversion copy first** — applies CRO principles, marketing psychology, and structured copywriting methodology to produce every section's text before touching code
2. **Plan SEO** — generates meta tags, JSON-LD schema (Organization, FAQPage, BreadcrumbList, Article…), sitemap config, and AI search optimization hints
3. **Design** — runs UI/UX design intelligence to pick a color palette, typography, and a signature visual detail specific to the brand and industry
4. **Build** — writes every file from scratch: Next.js 14 App Router, Tailwind CSS, Framer Motion, dark mode, Netlify Forms contact form, monthly/annual pricing toggle
5. **Deliver** — a production-ready static export with 7 pages, 2 blog articles, 2 case studies, and a logo carousel

## Skills included

| Skill | Trigger | What it does |
|-------|---------|--------------|
| `landing-page` | `/landing-page` | Orchestrator — runs the full pipeline |
| `landing-page-copy` | Used internally | Generates all page copy via copywriting + CRO + psychology methodology |
| `landing-page-seo` | Used internally | Generates SEO plan: meta tags, schema, sitemap, AI SEO hints |

## Usage

```
/landing-page CompanyName | industry
/landing-page CompanyName | industry | tagline hint
/landing-page CompanyName | industry | tagline | target audience | CTA goal
```

**Examples:**
```
/landing-page Clearflow | B2B SaaS finance automation
/landing-page Asaya | wellness coaching | Find your balance | women 30-50 | Book a session
/landing-page NexVita | health supplements
```

## Output

Generated in `./generated/{slug}-landing/` (relative to current working directory).

Override with env var:
```bash
LANDING_PAGE_OUTPUT_DIR=/path/to/projects
```

### Pages generated

| URL | Purpose |
|-----|---------|
| `/` | Homepage — hero, features, testimonials, pricing, FAQ, contact |
| `/tarifs` | Pricing detail with monthly/annual toggle |
| `/a-propos` | Brand story and values |
| `/blog` | Article listing + 2 full articles |
| `/cas-clients` | Case study listing + 2 full case studies |

### Tech stack

- **Next.js 14** App Router, static export (`output: 'export'`)
- **Tailwind CSS** with CSS custom properties for theming
- **Framer Motion** for scroll-triggered animations
- **Dark mode** with anti-flash script and localStorage persistence
- **Netlify Forms** ready contact form
- **MDX** for blog and case study pages
- **JSON-LD** structured data per page

## Requirements

### Required
- Claude Code with skills support

### Recommended
- [ui-ux-pro-max skill](https://skills.sh/) for design intelligence (graceful fallback if unavailable)
- Node.js 18+ for local preview

## Installation

```bash
npx skills add Demian-T/plugin-landing-page-creation
```

Or clone and reference locally:

```bash
git clone https://github.com/Demian-T/plugin-landing-page-creation
```

## Configuration

The plugin respects an optional `.claude/landing-page.local.md` in your project:

```markdown
---
output_dir: ./sites
default_locale: fr
---
```

## License

MIT
