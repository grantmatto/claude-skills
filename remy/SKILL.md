---
name: remy
description: >
  Expert knowledge for using Remy by MindStudio — a chat-based AI app builder
  that creates MindStudio agents from conversational descriptions. Trigger when
  Matto wants to build a new MindStudio app by describing it, use the Remy
  chat builder, write MSFM specs, or generate MindStudio workflows from ideas.
---

# Remy — MindStudio Chat App Builder

## What is Remy

Remy by MindStudio is a "Specification First Development" platform (Alpha stage).
It inverts traditional development: specifications are the source of truth, not code.
Code is regenerable output — you own the spec, Remy generates everything else.

**Chat builder URL:** https://app.mindstudio.ai/share/public/asset/4GlmTacwEWIqlDIyCRIpFy

You interact with Remy like Claude Code — describe what you want to build in
plain language and it builds the MindStudio workflow/app for you.

---

## Core Concept: MSFM

**MSFM = MindStudio-Flavored Markdown** — the spec format Remy uses.

A MSFM spec is a structured markdown document that describes:
- What the app does
- What inputs it takes
- What workflow steps it runs
- What outputs it produces

From one MSFM spec, Remy can generate:
- MindStudio AI agents/workflows
- Web dashboards
- REST APIs
- Mobile apps

If AI models improve, Remy can regenerate better code from the same spec automatically.

---

## How to Use the Remy Chat Builder

### Basic Flow
1. Open the Remy chat builder URL above
2. Describe what you want to build in plain language
3. Remy asks clarifying questions and builds the spec
4. It generates the MindStudio workflow blocks
5. Review, refine, iterate conversationally

### What to Tell Remy
Be specific about:
- **Purpose**: what problem the app solves
- **Inputs**: what information the user provides
- **Steps**: what the workflow should do
- **Output**: what the user receives (PDF, text, data, etc.)
- **Data sources**: what external data is needed (web scraping, Google, etc.)

### Example Prompt Approach
```
Build me a MindStudio agent that:
- Takes a suburb name and property type as input
- Scrapes recent sold listings from domain.com.au
- Uses Claude Sonnet to analyse the data and extract 6 comparable sales
- Fetches a property photo for each comp using Google Images
- Generates a professional PDF report with the comps and a price recommendation
```

### Iteration Tips
- Start broad, refine in follow-up messages
- Ask Remy to explain any block it creates
- Tell it when something isn't right — it adjusts
- Request specific models (Haiku vs Sonnet) for cost optimisation
- Ask it to add error handling or null checks explicitly

---

## Remy vs Manual MindStudio Building

| | Remy | Manual |
|--|------|--------|
| Speed | Fast — describe and generate | Slow — block by block |
| Control | Less — Remy chooses patterns | Full — you control everything |
| Best for | New agents, prototyping | Fixing, fine-tuning, complex logic |
| Learning | Remy's output teaches patterns | Learn by doing |

**Recommended workflow:**
1. Use Remy to build the first version (fast)
2. Export to MindStudio canvas for fine-tuning
3. Use Claude Code (Harry) to debug complex issues

---

## MSFM Spec Writing

When writing specs for Remy, structure them like this:

```markdown
# App Name

## Purpose
One sentence: what this app does and for whom.

## Inputs
- fieldName (Type): description
- suburb (Text): the suburb to analyse
- propertyType (Select: House, Unit, Townhouse): property category

## Workflow
1. Step description
2. Step description
3. Generate PDF with results

## Output
Describe what the user receives.

## Data Sources
- domain.com.au sold listings (scrapeUrl)
- Google Images for property photos
- Claude Sonnet for analysis

## Constraints
- 6 comparable sales maximum
- Within 2km radius
- Sold within last 12 months
```

---

## Known Limitations (Alpha)

- Alpha stage — expect rough edges
- Complex logic may need manual refinement in MindStudio canvas
- Review all generated block configs before running
- Test with simple inputs first
- Run Code blocks may need configuration object setup (see mindstudio-expert skill)

---

## Connection to Matto's Stack

Remy-built agents deploy as MindStudio agents — same platform as:
- AIRE CMA Builder (agent ID: 92230109-0f67-4c4f-bd1d-a97a0316a273)
- AI Property Market Report (agent ID: fceea54f-94e5-4911-984d-326acfde6aae)

Built agents can be embedded into airealestate.tech the same way.

---

## Tips

- Remy is best for greenfield — start fresh, describe the full thing
- For fixes to existing agents, use Harry (Claude Code) + MindStudio canvas directly
- Save Remy's output as an MSFM spec file — it's your portable spec
- If Remy gets confused, start a new chat with a cleaner description
- Ask Remy to use Haiku for cheap/simple steps and Sonnet for analysis
