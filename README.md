# proposal-skills

Proposal-making skill bundles for AI agents (Claude / Anthropic skill format).

## Skills

| Skill | Folder | What it does |
|-------|--------|--------------|
| `animated-proposal` | [`skills/animated-proposal/`](skills/animated-proposal/) | Fills a GSAP-animated HTML proposal template with prospect-specific content (30 variables, scope items, timeline, retainer bullets, terms, animated SVG icons) and deploys to Vercel. |

## Using a skill

Each skill folder contains:

- `SKILL.md` — front-matter + instructions the agent follows
- Any supporting assets (HTML template, JSON, etc.)

To load a skill into Claude Code or an Anthropic agent, point the agent at the skill folder. The agent reads `SKILL.md` and uses the listed `allowed-tools` plus any bundled assets.

## Adding a new skill

1. Create `skills/<skill-name>/`
2. Add a `SKILL.md` with YAML frontmatter (`name`, `description`, `allowed-tools`)
3. Drop any supporting templates / assets alongside it
4. Add a row to the table above
