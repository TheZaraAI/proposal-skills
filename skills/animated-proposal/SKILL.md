---
name: animated-proposal
description: >
  Fill the animated HTML proposal template with prospect-specific content.
  Reads all available context (discovery call transcript, job description,
  engineering plan, proposal JSON) and maps it into the template's 30 variables,
  scope items, timeline, retainer bullets, terms, and SVG icons. Gemini
  generates custom animated SVG icons to match each card's content.

  Use when: "animated proposal", "proposal website", "HTML proposal",
  "animate the proposal", "deploy proposal site", "cinematic proposal",
  "generate proposal website", "animated version of proposal",
  "make the proposal into a website", "proposal to website",
  "fill in the proposal template"
allowed-tools: Read, Write, Edit, Bash, Glob, Grep
---

# Animated Proposal

Fill the GSAP-animated HTML proposal template with prospect-specific content and deploy to Vercel.

**Skill chain:** Context gathering --> content synthesis --> icon generation --> template fill --> deploy.

## Goal

Produce a live proposal website at `proposal-{clientslug}.vercel.app` by reading all available context about the prospect/job and mapping it into the template's content areas. Every word verbatim, zero summarization.

## Context Sources

Read ALL available sources before filling the template. More context = better content mapping.

| Source | Where to Find | What It Provides |
|--------|--------------|-----------------|
| Discovery call transcript | Job folder, `.tmp/`, or user-provided path | Pain points, budget, timeline, client language |
| Job description | Upwork job folder or user-provided | Requirements, scope, client expectations |
| Engineering plan | `engineering_plan.md` from project-spec-thorough | Detailed scope, phases, deliverables, pricing |
| Proposal JSON | `create-proposal` output | Pre-structured problems, solutions, investment |
| Client brief/notes | User-provided | Any additional context |
| Stripe payment link | User-provided | Payment CTA URL |

**Minimum required:** At least ONE source that provides problems, scope, and pricing. Ask user for paths if nothing is obvious in the job folder.

## Process

### Step 1: Gather Context

Read every available source from the table above. Build a mental model of:
- Who the prospect is (name, company, role)
- Their core pain points (becomes problems section)
- What we're building for them (becomes solutions + scope)
- Pricing and timeline (becomes investment + timeline sections)
- What guarantee applies
- What the client needs to provide (becomes terms/obligations)

### Step 2: Map Context to Template Content

Synthesize the gathered context into every editable area of the template. This is the intelligence layer -- not just copying fields, but crafting the right content for each section.

**30 Template Variables:**

| Variable | What to Write |
|----------|--------------|
| `{{FIRST_NAME}}` | Prospect's first name |
| `{{CLIENT_NAME}}` | Prospect's full name |
| `{{COMPANY_NAME}}` | Prospect's company |
| `{{PROJECT_TITLE}}` | Descriptive project title from scope |
| `{{PROVIDER_NAME}}` | Drew Longest |
| `{{AGENCY_NAME}}` | SimpleSystems.ai |
| `{{DATE}}` | Today's date |
| `{{INTRO_PARAGRAPH}}` | Personal intro referencing discovery call specifics. Casual, not corporate. 2-3 sentences max. |
| `{{CHALLENGE_INTRO}}` | One sentence framing their pain. Lead with the dollar impact. |
| `{{PROBLEM_1-3_TITLE}}` | Short pain point titles (3-5 words each) |
| `{{PROBLEM_1-3_DESC}}` | Pain point descriptions with specific numbers from their situation |
| `{{SOLUTION_INTRO}}` | One sentence connecting solution to their specific problem |
| `{{SOLUTION_1-3_TITLE}}` | Short solution titles that mirror the problems |
| `{{SOLUTION_1-3_DESC}}` | What we build and why it fixes their specific pain |
| `{{SCOPE_PHASE_NAME}}` | Phase name from engineering plan |
| `{{SCOPE_SUBTITLE}}` | One-line phase description |
| `{{TOTAL_PRICE}}` | Total investment formatted (e.g., "$5,425") |
| `{{MILESTONE_AMOUNT}}` | 50% milestone amount |
| `{{RETAINER_PRICE}}` | Monthly retainer price |
| `{{TOTAL_DAYS}}` | Timeline in business days |
| `{{GUARANTEE_TEXT}}` | Full guarantee paragraph. Gets typewriter-animated. |
| `{{STRIPE_LINK}}` | Stripe payment link URL, or `STRIPE_LINK_REQUIRED` placeholder |

**Scope Items** (`scopeItems` JS array, ~line 877):
Each item = `{ title: "...", desc: "..." }`. One per deliverable from the engineering plan. Typically 8-16 items.

**Timeline Nodes** (`.timeline-node` HTML blocks, ~line 670):
Each node has a label, day count, and description. Match phases from the engineering plan. Add/remove nodes as needed.

**Retainer Bullets** (`.retainer-bullets` list, ~line 733):
Each `<li>` has `--ping-delay` for sequential animation (0s, 0.4s, 0.8s, 1.2s, 1.6s). Match to ongoing services.

**Terms/Clauses** (clause items, ~line 771):
Update clause text with project-specific details -- especially Client Obligations (Clause 03) which should list exactly what access/data the client needs to provide.

**Phase 2 Teaser** (paragraph after guarantee, ~line 767):
Brief mention of natural next phase. One sentence.

**Scope Counter** (line 652):
Update "0 of 16 Delivered" to match actual scope item count.

**Timeline Intro** (line 664):
Update "One month from kickoff..." to match actual timeline.

### Step 3: Generate SVG Icons

Each card needs an SVG icon that matches its specific content. The template ships with placeholder icons (clock, EKG, stopwatch for problems; checkmark, trend, lightning for solutions; Lucide-style paths for scope). These must be replaced with icons that match the actual content.

**Use Gemini to generate icons.** Send one prompt per icon group:

**Prompt pattern for problem/solution icons:**
```
Generate 3 animated SVG icons for a dark-background proposal website.
Each icon is 24x24 viewBox, stroke-based (no fill), stroke-width 1.5.

Problem icons use stroke="#E54545" (red).
Solution icons use stroke="#34C759" (green).

For each icon, the SVG must be self-contained with CSS animation inside a <style> tag.
Use unique class names per icon (e.g., ic1, ic2, ic3) to avoid conflicts.

Animation rules:
- Use CSS @keyframes inside SVG <style> tags (NOT GSAP)
- Animations run continuously (infinite), subtle and slow
- Icons must ALWAYS be fully visible -- animations are additive effects
- For path-following: use <animateMotion> with <mpath>
- For rotation: transform-origin must be explicit (e.g., 12px 12px)
- NO bouncing/translateY (causes clipping in cards)
- NO animation-play-state: paused (freezes mid-frame)

The 3 problems are:
1. [PROBLEM_1_TITLE] -- [brief description]
2. [PROBLEM_2_TITLE] -- [brief description]
3. [PROBLEM_3_TITLE] -- [brief description]

Generate an icon that visually represents each problem. Return only the SVG markup for each (everything inside the <svg> tag, not the tag itself).
```

Same pattern for solution icons (swap color to green, swap content).

**Scope icons** (`scopeIcons` array, ~line 893):
Lucide-style SVG path strings (no animation, no `<style>` tags). Simpler than problem/solution icons. Gemini can generate these in one batch prompt -- just the `<path>` and `<line>` elements for each scope item title.

**Insert generated icons:**
- Problem icons: replace inline SVG content in the 3 `.challenge-card .block-title` elements (~lines 605, 610, 615)
- Solution icons: replace inline SVG content in the 3 `.solution-card .block-title` elements (~lines 629, 634, 639)
- Scope icons: replace the `scopeIcons` array entries (~line 893)

### Step 4: Fill the Template

1. Copy `animated-proposal-template.html` to `.tmp/proposal-sites/{slug}/index.html`
2. Find-and-replace all 30 `{{VARIABLES}}`
3. Replace the `scopeItems` JS array with actual scope items
4. Replace the `scopeIcons` JS array with generated icon paths
5. Replace/add/remove `.timeline-node` HTML blocks
6. Update retainer bullets (keep `--ping-delay` pattern: 0s, 0.4s, 0.8s, etc.)
7. Update terms clause text
8. Replace problem/solution card SVG icons with generated ones
9. Update scope counter text to match item count
10. Update timeline intro text
11. Set `WEBHOOK_URL` if Modal webhook is deployed

### Step 5: Content Audit

Grep 5+ unique multi-word phrases from the original context sources in the filled HTML. Every piece of proposal content must appear verbatim. If content is missing or reworded, fix it.

### Step 6: Deploy to Vercel

Confirm with user before deploying:

```bash
cd .tmp/proposal-sites/{slug}/ && npx vercel --prod --yes --name proposal-{client_slug}
```

Return the live URL.

## Signature Webhook (Optional)

Template has built-in signature flow. Without webhook: signing works locally (PDF downloads, Stripe redirects). With webhook: full two-party flow (client signs, provider gets emailed counter-sign link, both get signed PDF).

Setup: `modal deploy execution/modal_proposal_signature.py`. Set `WEBHOOK_URL` in HTML.
URL params: `?role=client` (default) or `?role=provider&id=xxx`.

## Content Fidelity Rule (HARD)

Every word of the proposal must appear verbatim. Zero compression, summarization, or rewording. The proposal text is sacred content.

## Edge Cases

- **No Stripe link:** Insert `STRIPE_LINK_REQUIRED` placeholder. CTA button is inert.
- **No engineering plan:** Use job description + transcript to infer scope and timeline. Ask user to confirm.
- **Fewer than 3 problems/solutions:** Template has exactly 3 cards for each. Consolidate or split to fit 3.
- **More than 16 scope items:** Group related deliverables. Template handles any count but 8-16 is ideal.

## Environment

| Dependency | Setup |
|-----------|-------|
| Node.js 18+ | `node --version` |
| Vercel CLI | `npx vercel` (auto-downloads) |
| `GEMINI_API_KEY` | In `.env` (for icon generation only) |

## SVG Icon Rules (HARD -- learned from production bugs)

- NEVER use GSAP on SVG child elements (coordinate system mismatch)
- NEVER use `animation-play-state: paused` (freezes mid-frame)
- Use CSS `@keyframes` inside SVG `<style>` tags
- Use `<animateMotion>` with `<mpath>` for path-following dots
- Icons must ALWAYS be fully visible -- animations are additive
- No bouncing/translateY (causes clipping)
- Unique class names per icon to avoid CSS conflicts
- Clock = slow rotating hand (15s, 75% opacity). EKG = dot follows path. Checkmarks = stroke-draw. Lightning = double-flash. Trend arrows = opacity pulse.

## Template Technical Notes

- **FOUC prevention:** `visibility: hidden` on `.page-content`, init order: (1) visible, (2) `initContentAnimations()`, (3) `opacity: 1`
- **Sticky + GSAP conflict:** `position: sticky` breaks when parent has GSAP `transform`. Remove `gsap-rev` class from sticky elements.
- **PDF generation:** html2canvas + jsPDF with pre-capture flattening (unstick cards, freeze SVGs, await fonts, PNG format)
- **CSS `transparent` keyword** = `rgba(0,0,0,0)` which creates dark gradient bands. Use explicit `rgba(color, 0)`.
