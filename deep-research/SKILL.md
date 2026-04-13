---
name: deep-research
description: >
  Comprehensive deep research agent that produces long-form, publication-quality
  research reports on any topic. Modelled after MindStudio's Deep Research Agent.
  Use when: (1) user says "deep research", "research report", "write a report on",
  "comprehensive analysis of", "deep dive into", "technology research",
  "market research report", "investigate and report on", "/deep-research",
  (2) user needs a thorough, multi-source analysis of a technology, product,
  market, trend, competitor, or concept, (3) user wants a structured report with
  citations, executive summary, and actionable takeaways.
  NOT for: quick lookups, simple questions, or tasks where a paragraph answer suffices.
  This skill produces 3000-8000 word reports with full source attribution.
version: 1.0.0
category: research
tags: [deep-research, report, analysis, technology, market, competitor, citations]
recommended_skills: [competitive-analysis, x-research, content-marketing]
platforms:
  - claude-code
sources:
  - url: https://app.mindstudio.ai/share/public/asset/4GlmTacwEWIqlDIyCRIpFy
    accessed: 2026-04-14
    description: MindStudio Deep Research Agent output - reference for report structure
---

# Deep Research Agent

Produce comprehensive, publication-quality research reports with real sources,
inline citations, structured sections, and actionable takeaways. Reports follow
the structure of professional analyst reports.

## Workflow

Execute these phases sequentially. Each phase builds on the previous.

### Phase 1: Scope & Plan

1. Confirm the research topic with the user
2. Identify 3-5 research angles/dimensions to explore
3. Generate 6-10 diverse search queries covering:
   - The core topic (what it is, how it works)
   - Market context and competitors
   - Technical architecture/details
   - User/community sentiment and reception
   - Pricing, economics, practical considerations
   - Criticisms, limitations, risks
   - Future implications and trends

### Phase 2: Research & Gather

Use MindStudio tools via MCP for broad coverage. Run searches in parallel where possible.

**Primary search tools (use all that apply):**

```
mcp__mindstudio__searchGoogle       - Main web search (use 6-10 queries)
mcp__mindstudio__searchPerplexity   - AI-powered search for synthesis
mcp__mindstudio__searchGoogleNews   - Recent news coverage
mcp__mindstudio__searchXPosts       - Community/developer sentiment (if relevant)
```

**For each search:**
- Use `exportType: "json"` and `numResults: 10-15`
- Use `countryCode` appropriate to the topic
- Use `dateRange: "year"` for recency unless historical context needed

**Deep-dive on key sources:**

```
mcp__mindstudio__scrapeUrl          - Scrape 5-10 most important URLs from search results
```

- Scrape official documentation, blog posts, key articles
- Use `pageOptions.onlyMainContent: true` for cleaner extraction
- Use `pageOptions.waitFor: 3000` for JS-heavy pages

**Gather at least:**
- 15-25 unique sources
- Mix of official docs, independent analysis, community discussion, news
- Both supportive and critical perspectives

### Phase 3: Analyse & Structure

Organise findings into this report structure:

```
1. Cover Image (AI-generated via mcp__mindstudio__generateImage)
2. Title & Category Label
3. Executive Summary (2-3 paragraph abstract)
4. The Takeaway (8-10 numbered key findings)
5. Table of Contents
6. Report Date & Reading Time
7. Main Body (5-8 major sections, each with):
   - Section header
   - "Key Points" summary box
   - 2-4 subsections with analysis
   - Inline citations [N] linking to Sources
8. Conclusion (3-4 paragraphs)
9. Sources (full bibliography)
```

### Phase 4: Generate Report

**Cover image:**
```
mcp__mindstudio__generateImage
- Professional, abstract/conceptual art related to the topic
- Style: modern, clean, technology-oriented
- Do NOT include text in the image
```

**Report generation:**
Use `mcp__mindstudio__generateText` with a large-context model for the full report.

Model recommendation:
```json
{
  "modelOverride": {
    "model": "claude-4-5-sonnet",
    "temperature": 0.4,
    "maxResponseTokens": 16384
  }
}
```

If the report is too long for a single generation, split into sections and generate
each separately, then combine.

### Phase 5: Output & Deliver

**Default output: HTML file**

Save the report as a self-contained HTML file at:
```
~/research-reports/{topic-slug}-{YYYY-MM-DD}.html
```

The HTML should be:
- Self-contained (inline CSS, no external dependencies)
- Print-friendly with `@media print` styles
- Responsive for mobile viewing
- Professional typography (system fonts, proper line-height)
- Styled with the user's brand colours if available from their profile

**Alternative outputs (if requested):**
- Markdown file
- PDF (via browser print)

## Report Quality Standards

### Citations
- Every factual claim must have an inline citation `[N]`
- Citations link to the Sources section at the bottom
- Each source entry includes: quote, source name, URL, date, source type
- Minimum 15 unique sources per report

### Tone
- Professional analyst tone - objective, evidence-based
- Acknowledge uncertainty and information gaps explicitly
- Present multiple perspectives, not just the positive narrative
- Include a "Critical Analysis" section with limitations and risks

### Structure
- Each major section starts with a "Key Points" box (3-5 bullet summary)
- Use subheadings liberally for scannability
- Include estimated reading time based on word count (250 words/minute)
- Date the report prominently

### Honesty
- If information is unavailable, say so explicitly
- Distinguish between confirmed facts and claims/marketing
- Note when sources are limited or potentially biased
- Flag Alpha/Beta products as such

## HTML Report Template

Use this base structure for the HTML output:

```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1">
<title>{Report Title}</title>
<style>
  * { margin:0; padding:0; box-sizing:border-box; }
  body { font-family:Georgia,'Times New Roman',serif; color:#1a1a1a; background:#fff; line-height:1.8; }
  .container { max-width:800px; margin:0 auto; padding:0 24px; }
  .cover { width:100%; max-height:400px; object-fit:cover; border-radius:0 0 12px 12px; }
  .category { font-size:12px; text-transform:uppercase; letter-spacing:0.15em; color:#3A75C4; font-weight:700; margin:32px 0 8px; }
  h1 { font-size:32px; font-weight:700; line-height:1.2; margin-bottom:16px; color:#0F172A; }
  .meta { font-size:14px; color:#64748b; margin-bottom:24px; }
  .summary { font-size:16px; color:#374151; line-height:1.9; margin-bottom:32px; border-left:4px solid #3A75C4; padding:16px 20px; background:#f8fafc; border-radius:0 8px 8px 0; }
  .takeaway { background:#f8fafc; border-radius:12px; padding:24px 28px; margin-bottom:32px; }
  .takeaway h2 { font-size:18px; margin-bottom:16px; color:#0F172A; }
  .takeaway ol { padding-left:20px; }
  .takeaway li { margin-bottom:10px; font-size:14px; line-height:1.7; color:#374151; }
  .toc { margin-bottom:32px; }
  .toc a { color:#3A75C4; text-decoration:none; font-size:14px; display:block; padding:4px 0; }
  .toc a:hover { text-decoration:underline; }
  h2 { font-size:24px; font-weight:700; color:#0F172A; margin:40px 0 16px; padding-top:16px; border-top:1px solid #e5e7eb; }
  h3 { font-size:18px; font-weight:700; color:#1e293b; margin:24px 0 12px; }
  .key-points { background:#eff6ff; border-radius:8px; padding:16px 20px; margin-bottom:20px; }
  .key-points p.label { font-size:11px; text-transform:uppercase; letter-spacing:0.1em; color:#3A75C4; font-weight:700; margin-bottom:8px; }
  .key-points ul { padding-left:18px; }
  .key-points li { font-size:13px; line-height:1.6; color:#374151; margin-bottom:4px; }
  p { font-size:16px; margin-bottom:16px; color:#374151; }
  .citation { color:#3A75C4; font-size:12px; vertical-align:super; text-decoration:none; font-weight:600; }
  .citation:hover { text-decoration:underline; }
  .sources { margin-top:40px; padding-top:24px; border-top:2px solid #e5e7eb; }
  .sources h2 { border:none; padding-top:0; }
  .source-entry { padding:12px 0; border-bottom:1px solid #f1f5f9; font-size:13px; }
  .source-num { color:#3A75C4; font-weight:700; }
  .source-quote { font-style:italic; color:#64748b; margin:4px 0; }
  .source-meta { color:#94a3b8; font-size:12px; }
  .source-meta a { color:#3A75C4; text-decoration:none; }
  .footer { text-align:center; padding:32px 0; font-size:12px; color:#94a3b8; }
  img.figure { width:100%; border-radius:8px; margin:16px 0; }
  blockquote { border-left:3px solid #3A75C4; padding:8px 16px; margin:16px 0; font-style:italic; color:#475569; }
  @media print {
    body { font-size:11pt; }
    .container { max-width:100%; }
    h2 { page-break-before:auto; }
    .key-points, .takeaway, .summary { break-inside:avoid; }
  }
  @media (max-width:600px) {
    h1 { font-size:24px; }
    h2 { font-size:20px; }
    .container { padding:0 16px; }
  }
</style>
</head>
<body>
<img src="{cover_image_url}" alt="" class="cover" />
<div class="container">
  <p class="category">{Category Label}</p>
  <h1>{Report Title}</h1>
  <p class="meta">{Date} &middot; {reading_time} minute read</p>
  <div class="summary">{Executive summary paragraphs}</div>
  <div class="takeaway">
    <h2>The Takeaway</h2>
    <ol>{Numbered key findings}</ol>
  </div>
  <div class="toc">{Table of contents links}</div>

  <!-- Repeat for each section -->
  <h2 id="{section-id}">{Section Title}</h2>
  <div class="key-points">
    <p class="label">Key Points</p>
    <ul>{3-5 bullet summary}</ul>
  </div>
  <h3>{Subsection}</h3>
  <p>{Content with <a class="citation" href="#src-N">[N]</a> inline citations}</p>

  <!-- Sources -->
  <div class="sources">
    <h2>Sources</h2>
    <!-- Repeat for each source -->
    <div class="source-entry" id="src-{N}">
      <span class="source-num">[{N}]</span>
      <p class="source-quote">"{Relevant quote}"</p>
      <p class="source-meta">
        {Source Name} &middot; <a href="{url}">{Article Title}</a> &middot; {Date} &middot; {Source Type}
      </p>
    </div>
  </div>

  <div class="footer">
    This report was generated with AI-assisted research by AIRE &middot; airealestate.tech
  </div>
</div>
</body>
</html>
```

## Cost Awareness

Before running, inform the user of estimated MindStudio costs:
- Google searches: ~6-10 queries x $0.005 = ~$0.03-0.05
- Perplexity search: ~2-3 queries x $0.01 = ~$0.02-0.03
- URL scraping: ~5-10 pages x $0.005 = ~$0.025-0.05
- Image generation: ~1 image x $0.05 = ~$0.05
- Text generation: ~1-2 calls x $0.10 = ~$0.10-0.20
- **Total estimated: $0.25-0.40 per report**

Always call `mcp__mindstudio__estimateActionCost` before expensive operations
and confirm with the user before proceeding.

## Example Usage

User: "Deep research on Cursor IDE"
User: "Write a comprehensive report on AI in Australian real estate"
User: "Research report comparing Next.js vs Remix in 2026"
User: "/deep-research proptech trends Australia"
