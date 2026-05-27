# proposal-skills

Reusable proposal-generation skill bundles for AI agents (Claude Code / Anthropic skill format).

Originally created by [TheZaraAI](https://github.com/TheZaraAI).

---

## Skills

| Skill | Folder | What it does |
|-------|--------|--------------|
| `animated-proposal` | [`skills/animated-proposal/`](skills/animated-proposal/) | Fills a GSAP-animated HTML proposal template with prospect-specific content (variables, scope items, timeline, retainer, terms, animated SVG icons) and deploys to Vercel. |

---

## Quickstart — Use This for Your Own Automation

1. **Clone or copy** the `skills/animated-proposal/` folder into your agent's skill directory.
2. **Point your agent** at `SKILL.md`. The agent reads the front-matter and instructions, then uses the bundled `animated-proposal-template.html` as its output target.
3. **Give the agent context** about your prospect (discovery call transcript, job description, engineering plan, pricing). It will map everything into the 30+ template variables and generate a ready-to-deploy HTML file.
4. **Deploy** the filled HTML to Vercel (one command, shown below).

```bash
# After the agent fills the template:
cd .tmp/proposal-sites/{client-slug}/ && npx vercel --prod --yes --name proposal-{client-slug}
```

The result is a live proposal website at `proposal-{client-slug}.vercel.app`.

---

## Skill folder contents

```
skills/animated-proposal/
  SKILL.md                          # Agent instructions (front-matter + steps)
  animated-proposal-template.html  # GSAP-animated single-file proposal template
```

### Prerequisites

| Dependency | Notes |
|-----------|-------|
| Claude Code or any Anthropic agent that supports the skill format | Reads `SKILL.md` automatically |
| Node.js 18+ | For Vercel CLI |
| Vercel CLI | `npx vercel` (auto-downloads, no global install needed) |
| Gemini API key (`GEMINI_API_KEY` in `.env`) | Only needed for custom animated SVG icon generation (Step 3 of SKILL.md) |

---

## Template variables

All variables use double-curly-brace syntax: `{{VARIABLE_NAME}}`.

### Identity & metadata

| Variable | What to fill in |
|----------|----------------|
| `{{FIRST_NAME}}` | Prospect's first name (shown on animated splash screen) |
| `{{CLIENT_NAME}}` | Prospect's full name |
| `{{CLIENT_COMPANY}}` | Prospect's company name (also used as `{{COMPANY_NAME}}` in the template) |
| `{{COMPANY_NAME}}` | Prospect's company name (used in headers, terms, and footer) |
| `{{PROJECT_TITLE}}` | Short descriptive title for this project (e.g. "AI Lead Qualification System") |
| `{{DATE}}` | Proposal date (e.g. "May 27, 2026") |

### Provider / agency info

| Variable | What to fill in |
|----------|----------------|
| `{{PROVIDER_NAME}}` | Your full name (the person sending the proposal) |
| `{{AGENCY_NAME}}` | Your agency or company name |
| `{{PROVIDER_SIGNATURE_NAME}}` | Your name as it appears pre-signed in the provider signature block |
| `{{SIGNED_DATE}}` | Date you pre-signed (e.g. "May 27, 2026") |

### Intro letter

| Variable | What to fill in |
|----------|----------------|
| `{{INTRO_PARAGRAPH}}` | Personal 2-3 sentence intro referencing something specific from the discovery call. Casual tone, not corporate. |

### Challenge section (3 problems)

| Variable | What to fill in |
|----------|----------------|
| `{{CHALLENGE_INTRO}}` | One sentence framing their pain — lead with the dollar/time impact |
| `{{PROBLEM_1_TITLE}}` | Short pain point title, 3-5 words |
| `{{PROBLEM_1_DESC}}` | Pain point description with specific numbers from their situation |
| `{{PROBLEM_2_TITLE}}` | Short pain point title, 3-5 words |
| `{{PROBLEM_2_DESC}}` | Pain point description |
| `{{PROBLEM_3_TITLE}}` | Short pain point title, 3-5 words |
| `{{PROBLEM_3_DESC}}` | Pain point description |

### Solution section (3 solutions)

| Variable | What to fill in |
|----------|----------------|
| `{{SOLUTION_INTRO}}` | One sentence connecting your solution to their specific problem |
| `{{SOLUTION_1_TITLE}}` | Short solution title that mirrors Problem 1 |
| `{{SOLUTION_1_DESC}}` | What you build and why it fixes that pain |
| `{{SOLUTION_2_TITLE}}` | Short solution title |
| `{{SOLUTION_2_DESC}}` | Description |
| `{{SOLUTION_3_TITLE}}` | Short solution title |
| `{{SOLUTION_3_DESC}}` | Description |

### Scope section

| Variable | What to fill in |
|----------|----------------|
| `{{SCOPE_PHASE_NAME}}` | Name of the project phase (e.g. "Phase 1: Build") |
| `{{SCOPE_SUBTITLE}}` | One-line description of this phase |

Also replace the `scopeItems` JS array (~line 875) with one `{ title, desc }` object per deliverable (8-16 items typical), and the `scopeIcons` array with matching Lucide-style SVG paths.

### Timeline

Replace the `.timeline-node` HTML blocks with your actual phases. Each node needs a label, day count, and description. The scope counter text ("0 of 16 Delivered") auto-updates via JS — update the initial count to match your actual `scopeItems` length.

### Investment

| Variable | What to fill in |
|----------|----------------|
| `{{TOTAL_PRICE}}` | Total investment formatted (e.g. "$5,425") |
| `{{MILESTONE_AMOUNT}}` | 50% milestone amount (e.g. "$2,712") |
| `{{RETAINER_PRICE}}` | Monthly retainer price (e.g. "$750") |
| `{{TOTAL_DAYS}}` | Total project timeline in business days |
| `{{DEPOSIT_PERCENT}}` | Deposit percentage shown on CTA (e.g. "50") |

### Guarantee & terms

| Variable | What to fill in |
|----------|----------------|
| `{{GUARANTEE_TEXT}}` | Full guarantee paragraph — gets typewriter-animated |
| `{{PHASE_2_TEASER}}` | One sentence hinting at the natural next phase after delivery |
| `{{CLIENT_OBLIGATION_1}}` through `{{CLIENT_OBLIGATION_3}}` | Specific access/materials the client must provide (expand the list as needed) |

### Payment & integrations

| Variable | What to fill in |
|----------|----------------|
| `{{STRIPE_LINK}}` | Stripe payment link URL, or leave as `STRIPE_LINK_REQUIRED` to make the CTA inert |
| `{{SIGNED_PDF_UPLOAD_URL}}` | Your endpoint for receiving signed PDFs after client signs (optional — leave empty or remove the check if unused) |

---

## How to fill the template

The agent (guided by `SKILL.md`) handles this automatically. If you want to fill it manually:

1. Copy `animated-proposal-template.html` to your output folder as `index.html`.
2. Find-and-replace all `{{VARIABLES}}` with real values.
3. Replace the `scopeItems` JS array with your deliverables.
4. Replace the `scopeIcons` array with matching SVG path strings.
5. Replace the `.timeline-node` HTML blocks with your project phases.
6. Update the retainer bullets to match your ongoing services (keep the `--ping-delay` pattern).
7. Update the Client Obligations list (Clause 03) with the specific access this client must provide.
8. (Optional) Set `WEBHOOK_URL` in the script to your countersign endpoint.

---

## Deploy to Vercel

```bash
cd path/to/output/index.html/folder
npx vercel --prod --yes --name proposal-your-client-slug
```

No configuration file needed — it's a single static HTML file.

---

## Signature webhook (optional)

The template includes a two-party signature flow. Without a webhook, signing works locally: the client signs, downloads the PDF, and is redirected to Stripe. With a webhook (e.g. deployed via Modal), you get a full countersign flow:

1. Client signs → webhook stores signature → sends provider a counter-sign link.
2. Provider signs at `?role=provider&id=xxx` → both parties receive a signed PDF via email.

Setup: deploy `modal_proposal_signature.py` (not included here — implement your own endpoint), then set `WEBHOOK_URL` in the HTML script block.

---

## License

MIT — see [LICENSE](LICENSE).
