---
name: edit-deck
version: 0.2.0
description: |
  Edit a Slidev seed-round pitch deck following the YC recommended format.
  Use when asked to update slide content, add slides, fix formatting,
  change stats, scaffold a new deck, or deploy. Proactively suggest
  when the user mentions "deck", "slides", "presentation", "pitch", or "Slidev".
allowed-tools:
  - Bash
  - Read
  - Edit
  - Write
  - Grep
  - Glob
  - Agent
  - AskUserQuestion
---



# Edit Deck

You are editing a Slidev-based seed-round pitch deck. The canonical structure follows the YC seed deck template by Aaron Harris ("How to Build Your Seed Round Pitch Deck").

> "Focus on narrative. The rest is commentary." — Aaron Harris

---

## First Run: Setup + Scaffold

When the skill is invoked for the first time on a project (no `slides.md` exists, or user asks to "set up" / "start a new deck"), run this flow:

### Step 1: Project setup

Check what already exists and set up what's missing:

```bash
# Check for existing Slidev project
ls package.json slides.md style.css 2>/dev/null
```

**If no Slidev project exists**, scaffold one:
```bash
npm init -y
npm install @slidev/cli @slidev/theme-default
```

Add scripts to `package.json`:
```json
{
  "scripts": {
    "dev": "slidev",
    "slides:build": "slidev build",
    "slides:export": "slidev export"
  }
}
```

### Step 2: Vercel + GitHub setup

**Check for existing deploy config:**
```bash
ls deploy.sh vercel.json .vercel/project.json 2>/dev/null
```

**If no deploy pipeline exists**, set one up:

1. **Git repo** — initialize if needed:
   ```bash
   git init
   echo "node_modules/\ndist/\n.vercel/" > .gitignore
   ```

2. **Vercel project** — link or create:
   ```bash
   npx vercel link
   ```
   This will prompt the user to authenticate and select/create a project.

3. **Create `deploy.sh`:**
   ```bash
   #!/usr/bin/env bash
   set -e
   SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
   cd "$SCRIPT_DIR"

   echo "▶ Building slides..."
   npm run slides:build

   echo "▶ Deploying to Vercel..."
   npx vercel deploy --prod --yes

   echo "✓ Done."
   ```
   Then: `chmod +x deploy.sh`

4. **GitHub remote** — if not set:
   ```bash
   git remote get-url origin 2>/dev/null || echo "No remote"
   ```
   If no remote, ask the user for their GitHub repo URL and add it:
   ```bash
   git remote add origin <URL>
   ```

**If deploy config already exists**, read `deploy.sh`, `CLAUDE.md`, and `vercel.json` to learn the existing pipeline. Report what you found.

### Step 3: Generate template deck

Generate a `slides.md` following the YC 10-slide structure. Use placeholder content that clearly marks what the user needs to fill in.

**Generate `style.css`** with a clean design system (or read the existing one).

**Generate the template `slides.md`** — see "Template Deck Content" section below.

### Step 4: Ask the user for their data

After generating the template, ask the user to provide:

1. **Company name** and one-line description
2. **Problem** — what pain point do you solve?
3. **Solution** — what does your product do?
4. **Traction** — key metrics (MRR/ARR, users, growth rate, retention). Any chart data?
5. **Insight** — what makes this work? What's your unfair advantage?
6. **Business model** — how do you make money? Pricing?
7. **Market** — how big is the opportunity?
8. **Team** — founders: names, roles, backgrounds. Headshot files?
9. **The Ask** — how much are you raising? Use of funds? Target milestones?

Collect whatever the user provides and fill in the template. Leave `[PLACEHOLDER]` markers for anything not yet provided.

### Step 5: Build + verify

```bash
npx slidev build
```

If the build succeeds, report success and offer to:
- Start dev server: `npx slidev`
- Deploy: `./deploy.sh`
- Continue editing specific slides

---

## Template Deck Content

When generating a new deck, create `slides.md` that is a **pixel-faithful Slidev reproduction** of the YC seed deck template by Aaron Harris (Google Slides source: `17nFIwCyf2Kz-Ao5HGnmvNZ74L8eSKA2C2Qdaoe-47OM`).

**Visual rules — match the Google Slides exactly:**
- White background on every slide. No dark/cover slides.
- Black text (`#000`). No grey secondary text, no accent colors.
- Titles use a large serif font (Google Fonts "Playfair Display" approximates the original). Centered horizontally, positioned in the upper portion of the slide.
- Body text uses bullet points (`●` disc style) in a regular sans-serif, left-aligned with generous left margin, vertically centered in the remaining space.
- Page number in the bottom-right corner of every slide except the title slide (slide 1). Rendered as a small grey number.
- No eyebrow labels, no cards, no colored backgrounds, no borders, no CSS variable theming. The design is intentionally spartan.
- Traction slides (4 & 5) use a 2-column layout: a line chart on the left (~70% width) and a short dash-list of key stats on the right (~30% width). The chart is an SVG hockey-stick curve.
- Team slide uses a 3-column grid of placeholder images with role descriptions centered below each.

The content below uses the **exact placeholder text** from the YC template. When the user provides their real data, replace these placeholders — but the scaffolded deck should ship with this content verbatim so it matches the original.

```markdown
---
theme: default
title: 'Seed deck template, Inc.'
author: 'Seed deck template, Inc.'
fonts:
  sans: 'Inter'
  serif: 'Playfair Display'
  mono: 'JetBrains Mono'
  provider: google
defaults:
  layout: default
transition: fade
layout: default
---

<!-- Slide 1: Title -->
<div style="height:100%;display:flex;flex-direction:column;align-items:center;justify-content:center;text-align:center;">
<h1 style="font-family:'Playfair Display',serif;font-size:3.2rem;font-weight:700;color:#000;margin:0 0 0.5rem;">Seed deck template, Inc.</h1>
<p style="font-size:1.3rem;color:#000;font-weight:400;">A template that tells you how to build a seed deck</p>
</div>

---
layout: default
---

<!-- Slide 2: Problem -->
<div style="padding:3rem 4rem;height:100%;display:flex;flex-direction:column;">
<h2 style="font-family:'Playfair Display',serif;font-size:2.8rem;font-weight:700;color:#000;margin:0 0 1.5rem;">Founders build bad seed decks</h2>
<ul style="font-size:1.15rem;color:#000;line-height:2.2;list-style:disc;padding-left:2rem;">
  <li>This wastes a huge amount of time</li>
  <li>Bad seed decks mean they don&#x2019;t raise the money they need</li>
  <li>No money means companies are forced to survive on revenue</li>
</ul>
<div style="margin-top:auto;text-align:right;font-size:0.75rem;color:#999;">3</div>
</div>

---
layout: default
---

<!-- Slide 3: Solution -->
<div style="padding:3rem 4rem;height:100%;display:flex;flex-direction:column;">
<h2 style="font-family:'Playfair Display',serif;font-size:2.8rem;font-weight:700;color:#000;margin:0 0 1.5rem;">Our template solves this problem</h2>
<ul style="font-size:1.15rem;color:#000;line-height:2.2;list-style:disc;padding-left:2rem;">
  <li>It&#x2019;s fast to use</li>
  <li>Simple to explain</li>
  <li>Improves lives and cleans teeth</li>
</ul>
<div style="margin-top:auto;text-align:right;font-size:0.75rem;color:#999;">4</div>
</div>

---
layout: default
---

<!-- Slide 4: Traction -->
<div style="padding:3rem 4rem;height:100%;display:flex;flex-direction:column;">
<h2 style="font-family:'Playfair Display',serif;font-size:2.8rem;font-weight:700;color:#000;margin:0 0 1rem;">All the companies are using us</h2>
<div style="display:grid;grid-template-columns:2.2fr 1fr;gap:2rem;flex:1;align-items:center;">
  <div>
    <div style="font-size:0.85rem;font-weight:600;color:#000;margin-bottom:0.5rem;">Decks created per month</div>
    <svg viewBox="0 0 500 250" width="100%" style="overflow:visible;">
      <line x1="40" y1="10" x2="40" y2="220" stroke="#ccc" stroke-width="1"/>
      <line x1="40" y1="220" x2="490" y2="220" stroke="#ccc" stroke-width="1"/>
      <text x="5" y="15" font-size="9" fill="#666">1,000</text>
      <text x="18" y="68" font-size="9" fill="#666">750</text>
      <text x="18" y="120" font-size="9" fill="#666">500</text>
      <text x="18" y="173" font-size="9" fill="#666">250</text>
      <text x="55" y="240" font-size="8" fill="#666" transform="rotate(-45,55,240)">1/1/2017</text>
      <text x="92" y="240" font-size="8" fill="#666" transform="rotate(-45,92,240)">2/1/2017</text>
      <text x="129" y="240" font-size="8" fill="#666" transform="rotate(-45,129,240)">3/1/2017</text>
      <text x="166" y="240" font-size="8" fill="#666" transform="rotate(-45,166,240)">4/1/2017</text>
      <text x="203" y="240" font-size="8" fill="#666" transform="rotate(-45,203,240)">5/1/2017</text>
      <text x="240" y="240" font-size="8" fill="#666" transform="rotate(-45,240,240)">6/1/2017</text>
      <text x="277" y="240" font-size="8" fill="#666" transform="rotate(-45,277,240)">7/1/2017</text>
      <text x="314" y="240" font-size="8" fill="#666" transform="rotate(-45,314,240)">8/1/2017</text>
      <text x="351" y="240" font-size="8" fill="#666" transform="rotate(-45,351,240)">9/1/2017</text>
      <text x="388" y="240" font-size="8" fill="#666" transform="rotate(-45,388,240)">10/1/2017</text>
      <text x="425" y="240" font-size="8" fill="#666" transform="rotate(-45,425,240)">11/1/2017</text>
      <text x="462" y="240" font-size="8" fill="#666" transform="rotate(-45,462,240)">12/1/2017</text>
      <polyline points="55,215 92,213 129,212 166,210 203,208 240,205 277,198 314,185 351,165 388,130 425,75 462,15" fill="none" stroke="#4285F4" stroke-width="2.5"/>
    </svg>
  </div>
  <div style="font-size:1rem;color:#000;line-height:2;">
    <div>-&nbsp;&nbsp;50% growth per month. Every month.</div>
    <div>-&nbsp;&nbsp;100% retention</div>
  </div>
</div>
<div style="margin-top:auto;text-align:right;font-size:0.75rem;color:#999;">5</div>
</div>

---
layout: default
---

<!-- Slide 5: More Traction -->
<div style="padding:3rem 4rem;height:100%;display:flex;flex-direction:column;">
<h2 style="font-family:'Playfair Display',serif;font-size:2.8rem;font-weight:700;color:#000;margin:0 0 1rem;">Money raised thanks to our decks</h2>
<div style="display:grid;grid-template-columns:2.2fr 1fr;gap:2rem;flex:1;align-items:center;">
  <div>
    <div style="font-size:0.85rem;font-weight:600;color:#000;margin-bottom:0.5rem;">Dollars per month</div>
    <svg viewBox="0 0 500 250" width="100%" style="overflow:visible;">
      <line x1="60" y1="10" x2="60" y2="220" stroke="#ccc" stroke-width="1"/>
      <line x1="60" y1="220" x2="490" y2="220" stroke="#ccc" stroke-width="1"/>
      <text x="0" y="15" font-size="8" fill="#666">$50,000,000</text>
      <text x="0" y="68" font-size="8" fill="#666">$40,000,000</text>
      <text x="0" y="120" font-size="8" fill="#666">$30,000,000</text>
      <text x="0" y="173" font-size="8" fill="#666">$20,000,000</text>
      <text x="0" y="210" font-size="8" fill="#666">$10,000,000</text>
      <text x="75" y="240" font-size="8" fill="#666" transform="rotate(-45,75,240)">1/1/2017</text>
      <text x="110" y="240" font-size="8" fill="#666" transform="rotate(-45,110,240)">2/1/2017</text>
      <text x="145" y="240" font-size="8" fill="#666" transform="rotate(-45,145,240)">3/1/2017</text>
      <text x="180" y="240" font-size="8" fill="#666" transform="rotate(-45,180,240)">4/1/2017</text>
      <text x="215" y="240" font-size="8" fill="#666" transform="rotate(-45,215,240)">5/1/2017</text>
      <text x="250" y="240" font-size="8" fill="#666" transform="rotate(-45,250,240)">6/1/2017</text>
      <text x="285" y="240" font-size="8" fill="#666" transform="rotate(-45,285,240)">7/1/2017</text>
      <text x="320" y="240" font-size="8" fill="#666" transform="rotate(-45,320,240)">8/1/2017</text>
      <text x="355" y="240" font-size="8" fill="#666" transform="rotate(-45,355,240)">9/1/2017</text>
      <text x="390" y="240" font-size="8" fill="#666" transform="rotate(-45,390,240)">10/1/2017</text>
      <text x="425" y="240" font-size="8" fill="#666" transform="rotate(-45,425,240)">11/1/2017</text>
      <text x="460" y="240" font-size="8" fill="#666" transform="rotate(-45,460,240)">12/1/2017</text>
      <polyline points="75,218 110,217 145,216 180,215 215,213 250,210 285,205 320,195 355,178 390,148 425,95 460,15" fill="none" stroke="#4285F4" stroke-width="2.5"/>
    </svg>
  </div>
  <div style="font-size:1rem;color:#000;line-height:2;">
    <div>-&nbsp;&nbsp;US$s, not C$s</div>
    <div>-&nbsp;&nbsp;95% better than fundraises without our decks</div>
  </div>
</div>
<div style="margin-top:auto;text-align:right;font-size:0.75rem;color:#999;">6</div>
</div>

---
layout: default
---

<!-- Slide 6: Insight / Why It Works -->
<div style="padding:3rem 4rem;height:100%;display:flex;flex-direction:column;">
<h2 style="font-family:'Playfair Display',serif;font-size:2.8rem;font-weight:700;color:#000;margin:0 0 1.5rem;">Our decks work because of science</h2>
<ul style="font-size:1.15rem;color:#000;line-height:2.2;list-style:disc;padding-left:2rem;">
  <li>Time to create a deck is 90% less</li>
  <li>Edward Tufte approved</li>
  <li>We pick better color schemes</li>
  <li>Have you heard of the power of paper?</li>
</ul>
<div style="margin-top:auto;text-align:right;font-size:0.75rem;color:#999;">7</div>
</div>

---
layout: default
---

<!-- Slide 7: Business Model -->
<div style="padding:3rem 4rem;height:100%;display:flex;flex-direction:column;">
<h2 style="font-family:'Playfair Display',serif;font-size:2.8rem;font-weight:700;color:#000;margin:0 0 1.5rem;">We make money on every deck</h2>
<ul style="font-size:1.15rem;color:#000;line-height:2.2;list-style:disc;padding-left:2rem;">
  <li>5% of all capital raised + 1% royalty on business revenue</li>
  <li>That&#x2019;s $6m in revenue last year alone</li>
  <li>Free to produce new decks, because we don&#x2019;t do any work, it&#x2019;s a google slide template</li>
</ul>
<div style="margin-top:auto;text-align:right;font-size:0.75rem;color:#999;">8</div>
</div>

---
layout: default
---

<!-- Slide 8: Market / Future Growth -->
<div style="padding:3rem 4rem;height:100%;display:flex;flex-direction:column;">
<h2 style="font-family:'Playfair Display',serif;font-size:2.8rem;font-weight:700;color:#000;margin:0 0 1.5rem;">Future growth</h2>
<ul style="font-size:1.15rem;color:#000;line-height:2.2;list-style:disc;padding-left:2rem;">
  <li>There are 1000 slide decks made in SF every day</li>
  <li>That&#x2019;s the first market, but it&#x2019;s slowed because of how hard decks are to build</li>
  <li>We&#x2019;re going to make decks bigger, better, and easier, which means more decks</li>
</ul>
<div style="margin-top:auto;text-align:right;font-size:0.75rem;color:#999;">9</div>
</div>

---
layout: default
---

<!-- Slide 9: Team -->
<div style="padding:3rem 4rem;height:100%;display:flex;flex-direction:column;">
<h2 style="font-family:'Playfair Display',serif;font-size:2.8rem;font-weight:700;color:#000;margin:0 0 1.5rem;text-align:center;">Team</h2>
<div style="display:grid;grid-template-columns:repeat(3,1fr);gap:2rem;flex:1;align-items:center;">
  <div style="text-align:center;">
    <div style="width:160px;height:200px;background:#eee;margin:0 auto 0.75rem;display:flex;align-items:center;justify-content:center;color:#999;font-size:0.8rem;">[Photo]</div>
    <div style="font-size:1rem;color:#000;line-height:1.5;text-align:center;">CTO with<br/>lots of<br/>experience</div>
  </div>
  <div style="text-align:center;">
    <div style="width:160px;height:200px;background:#eee;margin:0 auto 0.75rem;display:flex;align-items:center;justify-content:center;color:#999;font-size:0.8rem;">[Photo]</div>
    <div style="font-size:1rem;color:#000;line-height:1.5;text-align:center;">CEO who<br/>knows how<br/>to pitch</div>
  </div>
  <div style="text-align:center;">
    <div style="width:160px;height:200px;background:#eee;margin:0 auto 0.75rem;display:flex;align-items:center;justify-content:center;color:#999;font-size:0.8rem;">[Photo]</div>
    <div style="font-size:1rem;color:#000;line-height:1.5;text-align:center;">VP eng<br/>who<br/>does not<br/>sleep</div>
  </div>
</div>
<div style="margin-top:auto;text-align:right;font-size:0.75rem;color:#999;">10</div>
</div>

---
layout: default
---

<!-- Slide 10: The Ask -->
<div style="padding:3rem 4rem;height:100%;display:flex;flex-direction:column;">
<h2 style="font-family:'Playfair Display',serif;font-size:2.8rem;font-weight:700;color:#000;margin:0 0 1.5rem;">What we need</h2>
<ul style="font-size:1.15rem;color:#000;line-height:2.2;list-style:disc;padding-left:2rem;">
  <li>$1.5m</li>
  <li>This is for engineers and marketing</li>
  <li>With this money, we&#x2019;ll hit all the milestones for our next round within 2 days</li>
  <li>Thanks</li>
</ul>
<div style="margin-top:auto;text-align:right;font-size:0.75rem;color:#999;">11</div>
</div>
```

After generating, tell the user: "I've scaffolded a 10-slide deck that matches the YC seed deck template by Aaron Harris. The content uses YC's placeholder text. What would you like to replace first?"

---

## YC Seed Deck Template (Aaron Harris)

Source: YC Startup Library. Original template: https://docs.google.com/presentation/d/17nFIwCyf2Kz-Ao5HGnmvNZ74L8eSKA2C2Qdaoe-47OM/edit

This is the canonical slide order. Every deck should follow this sequence unless the user explicitly deviates.

### Slide count rule

The title slide is the **only** section limited to exactly 1 slide. All other sections can be 1-3 slides, but aim for n=1 per section. "This is a seed deck, remember."

### Core slides

| #  | Slide | What to show | Aaron Harris guidance |
|----|-------|-------------|----------------------|
| 1  | **Title** | Company name + one-line description of what you do | "This is the only place in the deck where you can only have 1 slide." |
| 2  | **Problem** | Clearly state the problem. Bullet the real-world impact. | "Particulars of how this problem impacts real world people/businesses are valuable." |
| 3  | **Solution** | What you do about it. Concrete benefits. | "Explain what you do very clearly, in as few words as possible. Describe the concrete benefit(s) you provide." |
| 4  | **Traction** | Growth chart (up-and-to-the-right) + key stats next to it. | "Show off your traction (if you have it). Make the numbers clear and meaningful. It's unlikely your curve will be this smooth. That's ok." Add context next to the chart if you have great stats. |
| 5  | **More Traction** (optional) | Additional metrics, revenue chart, second proof point. | "Got more metrics? Awesome! Add them!" Revenue is ideal here. This slide is optional if slide 4 covers it. |
| 6  | **Insight / Why It Works** | What makes you special. Your unique insight. Why this works. | "Tell the investor what makes you so special, what makes this work, what your insights are. This might take more than one slide." |
| 7  | **Business Model** | How you make money. Pricing, revenue, unit economics. | "Business model is important. You probably don't know all the details yet, but you should know a lot of them. Lay it out. If you need more space to dig into something complicated, add slides." |
| 8  | **Market / Future Growth** | Market size, growth trajectory, why it gets bigger. | "What's the market here? Is it going to be big? Will you make it big? How much money are you going to make off this thing? Convince the investor that they're going to make lots of money with you." |
| 9  | **Team** | Founders: photos, names, roles, why suited to this problem. | "Team! So important at seed. Talk about what makes your team particularly well suited to the problem. This should be about founders. Nobody cares about your advisors." |
| 10 | **The Ask** | Amount raising, use of funds, milestones it unlocks. | "Tell the investor how much money you need, and what it gets you. If you can lay out where you'll be inside of a year, which should make you Series A ready, that's powerful." |

### Optional slides (add as needed for fund decks or specific contexts)

| Slide | When to include |
|-------|----------------|
| **Disclosures** | Required for regulated fundraises (fund decks, SEC compliance) |
| **Financials / Projections** | If you have revenue — 3-5yr outlook |
| **Testimonials** | Strong founder/customer quotes that reinforce traction |
| **Portfolio / Track Record** | For fund decks — past performance, notable exits |
| **Network / Access** | For fund decks — sourcing advantage, relationships |
| **Appendix** | Supplementary detail investors may request |

### YC formatting principles

- **~10 slides** for seed — tight and focused. Can extend to 15-20 with optional slides.
- **One idea per slide** — if a slide makes two points, split it
- **Intentionally simple design** — content > aesthetics. Clean, legible, no clutter.
- **Designed for async** — deck must stand alone without a presenter
- **Charts > text** — show traction visually. "Add context next to the chart."
- **Founders only on Team** — "Nobody cares about your advisors."
- **Series A ready** — the ask should show milestones that position you for the next round

### Auditing an existing deck against YC format

When asked to review or restructure a deck:
1. Read all of `slides.md` and build the slide map
2. Map each existing slide to the 10-slide YC structure above
3. Identify: missing slides, out-of-order slides, slides that combine multiple ideas
4. Report gaps and propose a reordering plan
5. Only restructure after user approval

---

## Slidev Architecture

| File | Purpose |
|------|---------|
| `slides.md` | All slide content — single markdown file, slides separated by `---` |
| `style.css` | Custom CSS — design system classes and CSS variables |
| `global-top.vue` | Global top component (e.g., dark mode observer) |
| `global-bottom.vue` | Global bottom component |
| `components/` | Custom Vue components |
| `public/` | Static assets — images, logos. Referenced as `/filename.jpg` |
| `deploy.sh` | Build + deploy script |
| `CLAUDE.md` | Project context for Claude Code |

## Slide Location

`slides.md` is a single file. Slides are separated by `---` on its own line. The first `---` block is YAML frontmatter, not a slide separator.

**To find slide N:** Count `---` separators from the top. Each pair of `---` lines (with `layout:` between them) marks the start of a new slide.

**Build a slide map on first use:** Read the file and create a table mapping slide numbers to line numbers and section eyebrows. Line numbers drift as content changes — always verify before editing.

**IMPORTANT:** Always `Read` the target area before editing.

---

## Edit Patterns

### Updating a stat value
```html
<div class="stat" style="font-size:2.8rem;">42</div>
```

### Updating a stat label
```html
<div class="stat-label">Total Count</div>
```

### Updating a card
```html
<div class="card" style="padding:1.1rem 1.25rem;">
  <div class="stat" style="font-size:2.8rem;">$100M</div>
  <div class="stat-label">Total Revenue</div>
</div>
```

### Updating eyebrow / section label
```html
<div class="eyebrow">Section Name</div>
```

### Updating display heading
```html
<h2 class="display-heading" style="font-size:2.4rem;color:var(--heading-color);margin:0 0 0.25rem;">
  Your headline here.
</h2>
```

### Updating footnote / disclosure text
```html
<p style="font-size:0.28rem;color:#6B7280;line-height:1.4;margin-top:0.3rem;">
  Disclosure text here...
</p>
```

### Adding a new slide
Insert between two `---` blocks. Every slide needs:
1. Frontmatter: `layout: default` (or `cover` for dark slides)
2. Outer wrapper: `<div style="padding:2rem 3.5rem;height:100%;display:flex;flex-direction:column;justify-content:flex-start;">`
3. Section label / eyebrow
4. Display heading
5. Content area
6. Footer
7. Closing `</div>`

**Standard footer (light):**
```html
<div class="footer">
  <span><img src="/icon.png" alt="" style="height:14px;vertical-align:middle;opacity:0.5;" /></span>
  <span>Confidential</span>
</div>
```

**Standard footer (dark/cover):**
```html
<div class="footer-dark">
  <span>Location Info</span>
  <span>Confidential</span>
</div>
```

### Traction slide (the most important slide)
The traction slide should feature a prominent growth chart. Pattern:
```html
<div class="eyebrow">Traction</div>
<h2 class="display-heading">Headline emphasizing growth.</h2>

<!-- Growth chart: SVG or image -->
<div style="...">
  <svg viewBox="..." width="100%"><!-- up-and-to-the-right line/bar chart --></svg>
</div>

<!-- Key metrics row -->
<div style="display:grid;grid-template-columns:repeat(3,1fr);gap:1rem;">
  <div class="card">
    <div class="stat">$1.2M</div>
    <div class="stat-label">ARR</div>
  </div>
  <div class="card">
    <div class="stat">25%</div>
    <div class="stat-label">MoM Growth</div>
  </div>
  <div class="card">
    <div class="stat">500+</div>
    <div class="stat-label">Customers</div>
  </div>
</div>
```

### Competition slide (2x2 matrix pattern)
```html
<div class="eyebrow">Competition</div>
<h2 class="display-heading">Where we sit.</h2>

<div style="display:grid;grid-template-columns:1fr 1fr;grid-template-rows:1fr 1fr;gap:0;width:80%;aspect-ratio:1;border:1px solid var(--border);border-radius:8px;overflow:hidden;">
  <div style="padding:1rem;border-right:1px solid var(--border);border-bottom:1px solid var(--border);">
    <div style="font-size:0.7rem;color:var(--text-secondary);">Quadrant label</div>
    <div style="font-weight:600;">Competitor A</div>
  </div>
  <div style="padding:1rem;border-bottom:1px solid var(--border);background:var(--highlight-bg);">
    <div style="font-size:0.7rem;color:var(--accent);">Your quadrant</div>
    <div style="font-weight:700;color:var(--accent);">Your Company</div>
  </div>
  <!-- bottom two quadrants -->
</div>
<!-- Axis labels positioned outside the grid -->
```

### Team slide (photo grid pattern)
```html
<div class="eyebrow">Team</div>
<h2 class="display-heading">The team.</h2>

<div style="display:grid;grid-template-columns:repeat(3,1fr);gap:1.5rem;">
  <div style="text-align:center;">
    <img src="/person.jpg" style="width:64px;height:64px;border-radius:50%;object-fit:cover;" />
    <div style="font-weight:600;font-size:0.85rem;margin-top:0.5rem;">Name</div>
    <div style="font-size:0.7rem;color:var(--text-secondary);">Role</div>
    <div style="font-size:0.65rem;color:var(--text-secondary);margin-top:0.2rem;">Background · School</div>
  </div>
  <!-- more team members -->
</div>
```

### The Ask slide
```html
<div class="eyebrow">The Ask</div>
<h2 class="display-heading">Raising $X to reach Y.</h2>

<div style="display:grid;grid-template-columns:1fr 1fr;gap:2rem;">
  <!-- Left: terms table -->
  <div>
    <div style="display:flex;justify-content:space-between;padding:0.5rem 0;border-bottom:1px solid var(--border);">
      <span style="font-size:0.7rem;color:var(--text-secondary);text-transform:uppercase;">Raising</span>
      <span style="font-weight:700;">$X</span>
    </div>
    <div style="display:flex;justify-content:space-between;padding:0.5rem 0;border-bottom:1px solid var(--border);">
      <span style="font-size:0.7rem;color:var(--text-secondary);text-transform:uppercase;">Instrument</span>
      <span style="font-weight:600;">SAFE / Priced</span>
    </div>
    <!-- more rows -->
  </div>

  <!-- Right: use of funds or milestone card -->
  <div class="card" style="padding:1.25rem;">
    <div style="font-size:0.7rem;text-transform:uppercase;color:var(--text-secondary);margin-bottom:0.5rem;">Use of Funds</div>
    <ul style="font-size:0.8rem;line-height:1.8;padding-left:1rem;">
      <li>Engineering — hire X</li>
      <li>Go-to-market — launch Y</li>
      <li>Reach Z milestone</li>
    </ul>
  </div>
</div>
```

### Person card
```html
<div class="card" style="display:flex;gap:0.75rem;align-items:flex-start;padding:0.65rem 0.9rem;">
  <div style="width:38px;height:38px;min-width:38px;border-radius:50%;overflow:hidden;flex-shrink:0;">
    <img src="/person-name.jpg" style="width:100%;height:100%;object-fit:cover;object-position:center top;" />
  </div>
  <div>
    <div style="font-weight:600;font-size:0.82rem;margin-bottom:0.05rem;">Person Name</div>
    <div style="font-size:0.62rem;color:var(--accent);margin-bottom:0.2rem;">Title, Company</div>
    <div style="font-size:0.7rem;color:var(--text-secondary);line-height:1.4;">Description text.</div>
  </div>
</div>
```

### Testimonial card
```html
<div style="background:var(--card-bg);border-radius:8px;padding:0.75rem 0.8rem;display:flex;flex-direction:column;align-items:center;text-align:center;overflow:hidden;">
  <img src="/person.jpg" alt="Name" style="width:42px;height:42px;border-radius:50%;object-fit:cover;margin-bottom:0.3rem;border:2px solid var(--highlight);flex-shrink:0;" />
  <div style="font-weight:700;font-size:0.72rem;color:var(--heading-color);margin-bottom:0.04rem;">Name</div>
  <div style="font-size:0.55rem;color:var(--text-secondary);margin-bottom:0.35rem;">Title, Company</div>
  <div style="border-left:3px solid var(--accent);padding-left:0.55rem;text-align:left;font-size:0.55rem;color:var(--text-secondary);line-height:1.5;font-style:italic;">"Quote text here."</div>
</div>
```

---

## HTML Entity Handling

Slidev renders HTML directly. Use HTML entities in `<div>` and `<span>` blocks:

| Character | Entity | Use case |
|-----------|--------|----------|
| Smart quotes | `&#x201C;` `&#x201D;` `&#x2018;` `&#x2019;` | Disclaimers, testimonials |
| Em dash | `&#x2014;` | Prose |
| Multiplication | `&#xD7;` | Multiplier values (e.g., `4.5×`) |
| Arrow right | `&#x2192;` | Flow indicators |
| Middle dot | `&#183;` | Separators |
| Non-breaking space | `&nbsp;` | Prevent line breaks |
| Up-right arrow | `&#8599;` | Growth indicators |

**Rule:** In HTML context, use entities. In pure markdown, use UTF-8 directly.

## Cross-References

When adding or removing slides:
1. Search for hardcoded page references: `grep -n "slide \d\|page \d\|p\.\d" slides.md`
2. Update all affected references

## Design System Reference

Every Slidev deck has a custom CSS design system in `style.css`. On first use, **read `style.css`** and catalog:

1. **CSS custom properties** (variables) — colors, fonts, spacing
2. **CSS classes** — component classes for cards, stats, headings, footers
3. **Layout patterns** — common grid/flex patterns used across slides

Use these classes and variables consistently. Never hardcode values that exist as variables.

### Common inline patterns
- Slide padding: `padding:2rem 3.5rem` or `padding:3rem 3.5rem`
- Flex column: `height:100%;display:flex;flex-direction:column;justify-content:flex-start;`
- Grid: `display:grid;grid-template-columns:repeat(3,1fr);gap:1rem;`
- Highlight border: `border:1.5px solid rgba(74,222,128,0.6);background:rgba(74,222,128,0.08);`

---

## Ship Workflow

When the user says "ship", "deploy", "push it live", or similar:

### Step 1: Pre-flight
```bash
npx slidev build
```
Fix any build errors before proceeding (unclosed tags, missing images, bad YAML, Vue errors).

### Step 2: Commit
```bash
git add slides.md style.css  # only changed files
git commit -m "descriptive message"
```

### Step 3: Push
```bash
git push origin main
```

### Step 4: Deploy to Vercel
Auto-detect:
1. `deploy.sh` exists → `./deploy.sh`
2. `vercel.json` exists → `npx vercel deploy --prod`
3. Git-triggered auto-deploy → check `npx vercel ls --prod 2>/dev/null | head -5`
4. None found → ask the user

### Step 5: Verify
```bash
# Check status
npx vercel ls --prod 2>/dev/null | head -3

# Check live URL
curl -s -o /dev/null -w "%{http_code}" <PRODUCTION_URL>
```
Report: deploy status, live URL, commit hash.

### Step 6: Post-deploy (optional)
If asked to verify content: fetch the live page and check key content appears.

### Rollback
```bash
git revert HEAD
git push origin main
# re-deploy
```
**Always ask the user before reverting.**

### Quick ship (edit + deploy in one flow)
1. Make the edit
2. `npx slidev build` to verify
3. Commit + push + deploy
4. Verify
5. Report: what changed, commit hash, live URL

---

## Key Facts Table

On first use, scan `slides.md` and build a reference table of key facts (names, numbers, dates). **Always verify against actual slide content before changing values** — slides are the source of truth.

## Completion Protocol

End every task with one of:
- **DONE** — changes applied successfully
- **DONE_WITH_CONCERNS** — changes applied but flagged an issue
- **BLOCKED** — cannot proceed, explain why
- **NEEDS_CONTEXT** — need more information from user
