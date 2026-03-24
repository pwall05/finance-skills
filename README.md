# paul-skills

A portable Claude skills library for finance and economics workflows.

## How to use

### Claude Cowork / claude.ai (file upload)
Upload this folder (or individual SKILL.md files) at the start of a session and reference them in your prompt:
> "Use the skill at financial-statement-analysis/SKILL.md to analyze this income statement."

### OpenClaw (system prompt injection)
In OpenClaw's system prompt configuration, add a block like:
```
You have access to the following skills. When the user's request matches a skill's description, read the skill file from GitHub and follow its instructions.

Skills index: https://raw.githubusercontent.com/YOUR_USERNAME/paul-skills/main/SKILLS_INDEX.md
```
OpenClaw can fetch raw GitHub URLs directly.

### Claude Code / Cowork (local mount)
Clone this repo and set the skills path in your Claude config:
```bash
git clone https://github.com/YOUR_USERNAME/paul-skills.git ~/.claude/skills/paul
```
Then reference in prompts or system instructions.

### GitHub (recommended hosting)
1. Create a private GitHub repo called `paul-skills`
2. Push this folder to `main`
3. Use raw.githubusercontent.com URLs in any system prompt

---

## Skills in this library

| Skill | Triggers on | Primary outputs |
|---|---|---|
| `financial-statement-analysis` | IS / BS / CF review, margin analysis, quality of earnings | Excel, Word, PPTX |
| `valuation-ma-modeling` | DCF, comps, M&A, Diligence OS, acquisition modeling | Excel, Word |
| `macro-investment-research` | Market research, investment thesis, sector analysis | Word, PPTX, Notion |

---

## Context about Paul

These skills are tuned for:
- **Industry**: Scrap metal recycling (Saskatchewan & Alberta)
- **Finance stack**: NetSuite (ERP), ROM system (SQL), Excel, Power BI
- **Investment work**: Canadian SMB acquisitions, private equity-style diligence (Diligence OS)
- **Communication style**: Business/finance first — translate between objectives and technical implementation
- **Output preference**: Professional, dense, numbers-forward. Avoid filler prose.
