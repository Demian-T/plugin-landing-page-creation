---
name: landing-page-copy
description: "Generates a complete conversion-optimized content brief for a landing page. Applies copywriting methodology, CRO principles, and marketing psychology to produce all page copy before any code is written. Call this before building a landing page. Inputs: company_name, industry, tagline_hint (optional), target_audience (optional), cta_goal (optional)."
---

# Landing Page Copy Generator

Produce a **structured content brief** — all copy ready to paste into components. No code. Just words, ordered for maximum conversion.

## Inputs (passed as context)

- `{company_name}` — brand name
- `{industry}` — sector (infer if vague)
- `{tagline_hint}` — optional seed for headline direction
- `{target_audience}` — who we're writing for
- `{cta_goal}` — primary conversion action (default: "Start free trial")

---

## Step 1 — Audience & Positioning Intelligence

Before writing a single word, synthesize:

**Audience pain profile** (infer from industry):
- Primary frustration (the thing they complain about at 11pm)
- Current workaround (what they use today, and why it's annoying)
- Desired outcome (the transformation they actually want)
- Language they use (concrete, not corporate)

**Positioning angle** — pick ONE:
- **Speed**: faster results than alternatives
- **Simplicity**: less complexity than alternatives
- **Outcome**: clearest ROI / transformation
- **Trust**: most credible / established
- **Niche**: built specifically for this audience

**Marketing psychology levers** to activate (pick 3 most relevant):
- Social proof (others have done it)
- Loss aversion ("without this, you lose X")
- Specificity ("save 4 hours/week" beats "save time")
- Anchoring (show premium value before price)
- Reciprocity (give something useful first)
- Authority (credentials, numbers, client names)
- FOMO / urgency (limited, early access, waitlist)

---

## Step 2 — Generate All Copy

Output a complete `CONTENT_BRIEF` object with every section below. Make it specific to the company and industry — no generic filler.

### Hero Section
```
badge:          [3-5 words, e.g. "Trusted by 500+ studios" or "Now with AI automation"]
headline_a:     [Primary: outcome-focused, specific, 6-10 words]
headline_b:     [Alt: pain-first, rhetorical question variant]
headline_c:     [Alt: niche + speed variant]
subheadline:    [1-2 sentences expanding the headline. Specific, benefit-driven. No buzzwords.]
cta_primary:    [Action verb + what they get — NOT "Sign Up". E.g. "Start My Free Trial", "Get My Custom Plan"]
cta_secondary:  [Softer commitment — "See how it works" / "Book a 15-min demo"]
social_proof_bar: [1 line under CTA: "Join 2,400+ {role} who {outcome}" or "★★★★★ 4.9 on G2"]
stat_1: { value: "X", label: "..." }
stat_2: { value: "X", label: "..." }
stat_3: { value: "X", label: "..." }
```

**Headline writing rules:**
- Specific > generic: "Cut reporting from 4h to 15min" beats "Save time"
- Use customer language: mirror how they describe the problem
- Avoid: "streamline", "optimize", "innovative", "next-generation", "revolutionize"
- Avoid exclamation marks

### Logo Carousel (Social Proof)
```
section_label:  ["Ils nous font confiance" or industry-appropriate equivalent]
brand_names:    [8-10 fictional but plausible company names for this industry]
```

### Features Section
```
section_badge:   [optional small label above heading]
section_heading: [2-5 words, e.g. "Everything you need"]
section_sub:     [1 sentence]

features: [4-6 items]
  - icon:        [lucide icon name, e.g. "Zap", "Shield", "BarChart3"]
    name:        [2-4 words]
    description: [1-2 sentences. Lead with the outcome, not the feature.]
```

### Testimonials
```
testimonials: [3 items]
  - quote:    [2-4 sentences. Specific result. No "game changer" clichés.]
    name:     [First Last]
    role:     [Job title]
    company:  [Company name]
    avatar_initial: [first letter of name]
```

### FAQ Section
```
faqs: [5-7 items — address real objections, not softball questions]
  - q: [Question in customer's voice]
    a: [Concise, direct answer. No corporate speak.]
```

**FAQ psychology**: address these objection types:
- "Is this really for me?" (fit)
- "Is it worth the money?" (ROI)
- "How fast does it work?" (time-to-value)
- "Will I need to change everything?" (switching cost)
- "Is my data safe?" (trust/security)
- "What if I don't like it?" (risk reversal)

### About Section
```
about_headline:  [Bold claim about why the company exists]
about_story:     [2-3 sentences: the problem founder faced → insight → what they built]
values: [3 items]
  - name:        [2-3 words]
    description: [1 sentence]
```

### Pricing Section
```
section_heading: ["Pricing" or something more specific]
section_sub:     [1 line, e.g. "No hidden fees. Cancel anytime."]

plans: [3 items — Starter, Pro, Enterprise or industry equivalents]
  - name:          [Plan name]
    price:         [monthly price as string, e.g. "49" or "Sur devis"]
    price_annual:  [~20% off monthly equivalent, e.g. "39"]
    period:        ["/mois" or equivalent]
    description:   [1 sentence positioning this plan]
    cta:           [e.g. "Start Free", "Get Started", "Contact Sales"]
    highlighted:   [true for middle/recommended plan]
    features: [4-6 strings]
```

### Contact Form
```
contact_heading:  [e.g. "Let's talk", "Start your project", "Get a free audit"]
contact_sub:      [1 sentence]
form_cta:         [Button text, e.g. "Send message", "Request a demo"]
```

### Blog Articles (2)
```
articles:
  - slug:      [url-friendly slug]
    title:     [SEO-optimized headline, includes keyword]
    summary:   [2 sentences. Useful, specific.]
    read_time: ["X min read"]
    keywords:  [2-3 target keywords]
```

**Blog topics** — choose based on:
- Top-of-funnel educational content (drives organic traffic)
- Middle-of-funnel comparison/how-to (captures intent)

### Case Studies (2)
```
case_studies:
  - slug:      [url-friendly slug]
    company:   [Client company name]
    industry:  [Client's sector]
    challenge: [1 sentence: what was broken before]
    solution:  [1 sentence: what {company_name} enabled]
    result:    [Specific metric: "Reduced X by Y% in Z weeks"]
    summary:   [2 sentences for the card]
```

### Navigation
```
nav_links:
  - label: "Fonctionnalités"   href: "#features"
  - label: "Témoignages"       href: "#testimonials"
  - label: "Tarifs"            href: "/tarifs"
  - label: "À propos"          href: "/a-propos"
  - label: "Blog"              href: "/blog"
  - label: "Cas clients"       href: "/cas-clients"
nav_cta: [same as cta_primary, shorter if needed]
```

---

## Step 3 — CRO Checklist (self-validate before outputting)

Before finalizing the brief, verify:

- [ ] Headline is **specific** — contains a number, timeframe, or concrete outcome
- [ ] Hero CTA **tells them what they get**, not just what to do
- [ ] At least 2 testimonials contain **specific metrics or timeframes**
- [ ] FAQ includes at least one **risk-reversal** answer (money-back, free trial, cancel anytime)
- [ ] Pricing has a **clearly recommended plan** (highlighted: true)
- [ ] Blog titles include **target keywords** (not just clever titles)
- [ ] No section uses: "streamline", "optimize", "innovative", "game-changer", "revolutionize", "seamlessly", "robust", "leverage", "cutting-edge"

---

## Output Format

Output the full brief as a structured markdown block labeled `CONTENT_BRIEF`. The calling skill will parse this directly into `lib/data.ts`, `lib/blog.ts`, and `lib/case-studies.ts`.

```markdown
## CONTENT_BRIEF

### positioning
...

### hero
...

### logo_carousel
...

### features
...

### testimonials
...

### faq
...

### about
...

### pricing
...

### contact
...

### blog
...

### case_studies
...

### nav
...
```
