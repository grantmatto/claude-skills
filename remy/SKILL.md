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

MSFM extends standard Markdown with annotations that attach precision to prose.
**Dual-layer structure:**
- **YAML frontmatter** — machine-readable fields (name, inputs, outputs, constraints)
- **Markdown body** — human-readable prose with inline annotations

This makes specs "a shared medium. Something both humans and agents can read, write,
and reason about. Something that captures intent precisely enough to generate an
implementation, and captures the implementation clearly enough to verify the intent."

The format aligns with the broader ecosystem: Claude Code Sub Agents, GitHub Copilot
Custom Agents all use markdown-based formats for agent definitions. MSFM is the Remy
flavour of this emerging standard.

### Application Contracts

From an MSFM spec, Remy compiles an **application contract** — a single authoritative
definition of the entire system: every method, every data model, every access rule.

### Projections

The contract then generates multiple **projections** — different interface renderings
of the same underlying logic:

> "The same application contract can be rendered as a web dashboard. As a REST API.
> As an AI agent that takes actions on behalf of a user. As a voice interface.
> Same methods, same data, same rules. We call these projections. Because the logic
> lives in the contract (not in any individual projection), the projection can't break it."

This means:
- Web dashboard and REST API cannot diverge — both derive from the same contract
- Add new interfaces without rebuilding the core
- When the spec changes, ALL projections regenerate automatically
- Prevents specification-code drift (the core failure mode of low-code platforms)

From one MSFM spec, Remy can generate:
- MindStudio AI agents/workflows
- Web dashboards
- REST APIs
- Voice interfaces
- Mobile apps

If AI models improve, Remy regenerates better code from the same spec automatically.

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

## Prompt vs Specification

> *"A prompt tells the AI what to do in a specific moment. A specification tells the AI what you mean in every situation it might encounter."*

**Example:**
- ❌ Prompt: "Summarize this customer email and suggest a response"
- ✅ Spec: decision trees for different scenarios, tone requirements, length limits, escalation rules

A spec handles every case. A prompt handles one case.

Remy is positioned as a **collaborator, not autocomplete:**
> *"Code-level AI tools make you a faster programmer. Remy changes what programming means."*

Because the spec is source of truth (not the code), Remy can make sweeping changes without fragility:
rename a concept across the entire app → update spec → everything regenerates to match.

**The alignment loss problem** (why specs matter more than prompts):
Research on 600 rejected PRs found alignment loss during execution — not poor task descriptions —
as the primary driver of agentic workflow failures. Agents receive accurate specs but produce code
that fails because the spec doesn't update to reflect intermediate decisions. Remy's spec-as-truth
model addresses this by keeping spec and implementation always in sync.

---

## The Core Principle

> *"An AI cannot fix a flawed or ambiguous requirement; it can only instantiate that flaw with frightening efficiency."*
> — StartupHub.ai, January 2026

Remy shifts the bottleneck from coding to specification. The quality of the spec is
the single most critical factor in what gets built. This is not optional precision —
it's the entire discipline.

**The paradigm shift:**
- Old: execution efficiency (how fast can we write code?)
- New: definition precision (how clearly can we specify intent?)

The spec must be treated as software itself — tested, validated, version-controlled —
before a single line of code is generated. Loose natural language → ambiguous output.
Structured, precise specs → reliable, reproducible delivery.

## The 5 Dimensions of Specification Precision

Writing specs is the core skill. Each spec must address all five dimensions:

1. **Scope** — what the app does AND doesn't do (explicit exclusions matter)
2. **Format** — input/output data structure (types, shapes, constraints)
3. **Constraints** — rules, limits, edge cases, validation, guardrails
4. **Context** — who uses it, when, why, what they already know
5. **Success criteria** — how you know it worked correctly

Remy can only build what the spec captures. Vague specs → vague apps.

**The Kiro (Amazon) philosophy:**
Investment in the front-end requirements process yields exponential returns in the
back-end coding phase. The spec is a high-leverage artifact — get it right first.

---

## MindStudio Infrastructure (Zero-Config)

Remy builds on MindStudio's production infrastructure. All of this is available
as zero-configuration SDK calls from within any Remy-built app:

- **200+ AI models** (Claude, GPT-4o, Gemini, Llama, etc.)
- **Managed databases** with automatic schema migrations
- **Role-based authentication** built in
- **1,000+ third-party integrations** (Zapier, Stripe, Slack, etc.)

No setup, no API keys to manage, no infrastructure to provision.

---

## Agent Collaboration Model

Remy supports two orchestration modes:
1. **Structured visual workflows** — define exact steps (MindStudio block canvas)
2. **LLM-driven reasoning** — for steps requiring judgment

Multi-agent: one agent can call another, enabling specialisation without losing
visibility into what's happening.

---

## Known Limitations (Alpha)

- **Alpha stage** — limited public access, no public pricing as of April 2026
- Complex logic may need manual refinement in MindStudio canvas
- Review all generated block configs before running
- Test with simple inputs first
- Run Code blocks may need configuration object setup (see mindstudio-expert skill)
- **Unanswered questions**: spec completeness requirements for complex apps, debugging
  when code is abstracted, customisation limits, platform lock-in risk
- Significant marketing presence but limited technical documentation publicly available

---

## Connection to Matto's Stack

Remy-built agents deploy as MindStudio agents — same platform as:
- AIRE CMA Builder (agent ID: 92230109-0f67-4c4f-bd1d-a97a0316a273)
- AI Property Market Report (agent ID: fceea54f-94e5-4911-984d-326acfde6aae)

Built agents can be embedded into airealestate.tech the same way.

---

## Pricing

**MindStudio platform pricing (as of April 2026):**

| Tier | Cost | Includes |
|------|------|---------|
| Free | $0/month | 1 agent, 1,000 runs/month, 200+ models, BYOK |
| Individual | $20/month ($16 annual) | Unlimited agents, unlimited runs, workshops, certifications |
| Business | Custom | Team collaboration, SSO, audit logs, self-hosting, custom domains |

**Remy-specific pricing: unknown.** No public pricing as of April 2026.

**AI inference costs:** MindStudio charges exactly what model providers charge — no markup.
Example: ~$0.011 for a single workflow run.

**Economic case (MindStudio's own claims):**
- Custom AI agent development: $75K-$500K, takes months
- No-code platform alternative: $187K average annual savings
- Note: these figures are for AI agent development, not full Remy app generation

**Watch out for hidden costs:**
- Ongoing AI inference as specs recompile with newer models (automatic improvement ≠ free)
- Infrastructure/hosting (unclear if self-deployable or MindStudio-hosted only)
- Scaling with traffic and data volume
- Platform lock-in: if specs can't be compiled by alternative tools, migration is hard

---

## Competitive Context

| Tool | Operates at | Primary artifact |
|------|------------|-----------------|
| GitHub Copilot | Code level | Code |
| Cursor | Code level | Code |
| Claude Code (Harry) | Code level | Code |
| **Remy** | **Spec level** | **Specification** |

Remy doesn't accelerate coding — it replaces code as the primary artifact.
When AI models improve, Remy apps improve automatically because the spec is stable
and the implementation layer regenerates beneath it.

---

## Tips

- Remy is best for greenfield — start fresh, describe the full thing
- For fixes to existing agents, use Harry (Claude Code) + MindStudio canvas directly
- Save Remy's output as an MSFM spec file — it's your portable spec, not just a prompt
- If Remy gets confused, start a new chat with a cleaner, more precise description
- Ask Remy to use Haiku for cheap/simple steps and Sonnet for analysis
- Version-control your MSFM specs in Git — they're the source of truth
- When spec changes, expect full regeneration — don't manually patch generated code
- Security note: ~45% of AI-generated code has security flaws (Veracode 2025) — review generated code, don't assume it's production-safe
