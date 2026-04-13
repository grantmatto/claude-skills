---
name: mindstudio-expert
description: >
  Expert knowledge for building, editing, and debugging MindStudio AI agents.
  Trigger when Matto asks about MindStudio, wants to build or fix an agent, add blocks,
  wire up data, generate charts or PDFs, scrape web content, or understand how MindStudio
  works. Covers the full block library, variable syntax, CLI usage, workflow patterns,
  and the internal API. Includes Matto's AI Property Market Report for real estate agents.
---

# MindStudio Expert Skill

## What is MindStudio

Visual AI agent builder at app.mindstudio.ai. Canvas-based workflow editor where each step
is a "block". Data flows between blocks via named variables. Output via Display Content blocks
or exported as PDF/image via Generate Asset blocks.

Agents can be embedded on websites. Agent branding (name, logo, colors, photo) can be
pre-injected per agent via URL params or API so each real estate agent gets a personalised report.

---

## Variable Syntax

| Syntax | Use |
|--------|-----|
| {{variableName}} | Simple string variable |
| {{json variableName}} | Render object/array as JSON |
| {{object.property}} | Nested property access |
| {{object.array.[0].field}} | Array index access |
| {{#each items}}...{{/each}} | Loop over array |
| {{#if condition}}...{{/if}} | Conditional |
| {{global.variableName}} | Cross-workflow global |
| {{this}} | Current item in each loop |
| {{this.fieldName}} | Property in current loop item |

---

## Block Library

### User Input
Collects data before workflow starts. Each field = named variable.
Types: Text, Long Text, Number, Select, Multi-select, Date, File Upload, Toggle

### Generate Text
Core AI block. Calls LLM, stores response as variable.
Models: Claude Sonnet/Haiku/Opus, GPT-4o, Gemini, Llama, etc.
System prompt: block-level context (separate from agent system prompt)
Key pattern: end JSON prompts with "Output ONLY valid JSON. No markdown. No code fences."

### Generate Image
Calls image generation model (DALL-E 3, Stable Diffusion, etc.)
Output: URL stored in variable (e.g. heroImageUrl)
Never hardcode property type or location - always use {{propertyType}} and {{suburb}}

### Generate Chart (Chart.js / QuickChart)
Generates chart image from Chart.js JSON config. Output: image URL.
CRITICAL: data must come from variables, never hardcoded.
Use {{brandColorPrimary}} and {{brandColorSecondary}} for agent brand colors.
Chart types: bar, line, pie, doughnut, radar

### Generate Asset (HTML/PDF)
Renders HTML template using Handlebars syntax. Output: PDF, PNG, or webpage URL.
Full CSS supported. Use CSS custom properties for brand colors.

### Scrape URL
Fetches content from a web page. Input: URL. Output: extracted text.
Use for: REA/Domain listings, suburb stats, property data.

### searchGoogle (native block)
Google search returning title, URL, snippet array.
Use for: finding comparable sales listing URLs on REA/Domain.

### searchGoogleImages (native block)
Google Images search returning array of {url, title, source}.
Use for: fetching property photos for comparable sales.
Pattern: "{{address}} {{suburb}} NSW property house photo"
Take first result, extract URL with Run Code block.

### screenshotUrl
Screenshot any URL, returns image. Use for: REA/Domain listing pages.

### Display Content
Shows content to user mid-workflow. Supports Markdown and HTML.
Use for: status updates, loading messages.

### Logic
Conditional branching based on variable values.
Operators: equals, not equals, contains, greater than, less than, is empty.

### Jump
Unconditional redirect to another block.

### Menu
Presents options to user, routes based on selection.

### Run Workflow
Calls another workflow (sub-agent pattern). Pass variables in and out.

### Checkpoint
Saves workflow state. User can return to this point.

### Text to Speech
Text to audio via ElevenLabs. Output: audio URL.

### Generate Video
HeyGen-style video with avatar + audio.
Use for: Matto video avatar generation in social pipeline.

### Run Code (JavaScript)
Executes JavaScript in workflow.
Use for: JSON.parse(), array manipulation, string formatting, math.
Example: const d = JSON.parse(inputs.marketDataJson); return { median: d.medianPrice };

CRITICAL LIMITATION: Run Code output variables are NOT automatically available as workflow
variables unless you configure the "configuration object" in the block UI. If outputs show
as "undefined" in downstream blocks, the fix is to use Generate Text blocks instead.

---

## MindStudio CLI

Installed: ~/.mindstudio/bin/mindstudio.exe
Config: ~/.mindstudio/config.json

Commands:
  rtk ~/.mindstudio/bin/mindstudio.exe whoami
  rtk ~/.mindstudio/bin/mindstudio.exe list-actions --summary
  rtk ~/.mindstudio/bin/mindstudio.exe info <actionName>
  rtk ~/.mindstudio/bin/mindstudio.exe run-agent <appId>
  rtk ~/.mindstudio/bin/mindstudio.exe searchGoogle --query "..."
  rtk ~/.mindstudio/bin/mindstudio.exe scrapeUrl --url "..."
  rtk ~/.mindstudio/bin/mindstudio.exe generateChart --config '{...}'

---

## Internal API

Load workflow:
  GET https://v1.mindstudio-api.com/v1/apps/create/{appId}/load
  Authorization: Bearer {cognitoJwt}

Edit workflow:
  POST https://v1.mindstudio-api.com/v1/apps/create/{appId}/edit-delta
  Authorization: Bearer {cognitoJwt}
  Body: {"operations": [...]}

Auth: Cognito JWT from browser cookie CognitoIdentityServiceProvider.*.accessToken

---

## Matto AI Property Market Report Agent

Agent ID: fceea54f-94e5-4911-984d-326acfde6aae
URL: https://app.mindstudio.ai/agents/fceea54f-94e5-4911-984d-326acfde6aae/edit
Workflow ID: 80799f00-f412-4ada-9841-d07c2501620e

### Agent Purpose
Used by real estate agents on Matto website. Agents enter property details + estimated price.
AI automatically collects comparable sales, fetches a photo for each comp, and generates
a branded PDF report using each agent's own colors, logo, name, and photo.

### Agent Branding (injected from website per agent)
agentName, agencyName, agentPhone, agentEmail, agentTitle, agentPhotoUrl, logoUrl
brandColorPrimary (hex e.g. #00A651), brandColorSecondary (hex e.g. #FFD700)

### Recommended Workflow Design

Step 1: User Input
  - propertyAddress, suburb, postcode, propertyType
  - bedrooms, bathrooms, carSpaces, landSize, saleMethod
  - estimatedPrice (agent enters this)
  - [branding vars injected from website: agentName, logoUrl, brandColorPrimary, etc.]

Step 2: Generate Text -> marketDataJson
  Analyse suburb market, output JSON with monthLabels (6 items), medianPrices, soldVolumes,
  daysOnMarket (all same-length arrays), currentMedian, yoyChange, stockLevels, activeBuyers, insight

Step 3: Generate Text -> comparableSalesJson
  Find 4-5 comparable sales near estimatedPrice, output JSON array with:
  address, soldPrice, soldDate, bedrooms, bathrooms, carSpaces, landSize,
  daysOnMarket, distance, relevanceNote

Step 4: searchGoogleImages x 4-5 (one per comparable)
  Query per comp: "{{comp.address}} {{suburb}} NSW property house photo"
  Store as comp1Images, comp2Images, comp3Images, comp4Images

Step 5: Run Code -> extract photo URLs
  return {
    comp1PhotoUrl: inputs.comp1Images[0]?.url || '',
    comp2PhotoUrl: inputs.comp2Images[0]?.url || '',
    comp3PhotoUrl: inputs.comp3Images[0]?.url || '',
    comp4PhotoUrl: inputs.comp4Images[0]?.url || ''
  }

Step 6: Generate Image -> heroImageUrl
  "{{propertyType}} exterior in {{suburb}}, Northern Beaches Sydney. Natural light. No text."

Step 7: Generate Chart x3
  soldVolumeChartUrl, medianPriceChartUrl, daysOnMarketChartUrl
  (all using {{json marketDataJson.field}} and {{brandColorPrimary}})

Step 8: Generate Text -> marketNarrativeText (3 paragraphs, human-readable)

Step 9: Generate Asset -> PDF report
  Full branded report with charts, comp cards (photo+details), agent branding

### Key Block IDs (existing, need fixes)
4137ba51 - Generate Text -> marketDataJson (OK)
5a607c66 - Generate Image -> heroImageUrl (FIX: hardcoded "beachside house")
b903692a - Generate Chart -> soldVolumeChartUrl (FIX: hardcoded data)
5c77ddfb - Generate Chart -> medianPriceChartUrl (FIX: hardcoded data)
4901402a - Generate Chart -> daysOnMarketChartUrl (FIX: hardcoded data)
ed3061cf - Generate Asset PDF (FIX: add missing branding variables)

---

## Comparable Sales Card Pattern (PDF)

Each comp card: photo left, details right, side by side.

{{#each comparableSales}}
<div class="comp-card" style="display:flex; gap:16px; margin-bottom:24px; border:1px solid #eee; border-radius:8px; overflow:hidden;">
  <div style="width:200px; flex-shrink:0;">
    <img src="{{this.photoUrl}}" onerror="this.src='{{heroImageUrl}}'"
      style="width:100%; height:150px; object-fit:cover;" alt="{{this.address}}" />
  </div>
  <div style="padding:12px; flex:1;">
    <h3 style="margin:0 0 8px; font-size:14px;">{{this.address}}</h3>
    <p style="font-size:18px; font-weight:bold; color:var(--brand-primary); margin:0 0 8px;">
      ${{this.soldPrice}}
    </p>
    <p style="font-size:12px; color:#666; margin:0 0 4px;">
      {{this.bedrooms}} bed / {{this.bathrooms}} bath / {{this.carSpaces}} car
    </p>
    <p style="font-size:12px; color:#666; margin:0 0 4px;">
      Land: {{this.landSize}}sqm | Sold: {{this.soldDate}} | {{this.daysOnMarket}} days
    </p>
    <p style="font-size:11px; color:#888; margin:0; font-style:italic;">
      {{this.relevanceNote}}
    </p>
  </div>
</div>
{{/each}}

---

## Chart Config Patterns (dynamic, branded)

Median Price (line):
  {"type":"line","data":{"labels":{{json marketDataJson.monthLabels}},"datasets":[{"label":"Median Price","data":{{json marketDataJson.medianPrices}},"borderColor":"{{brandColorPrimary}}","fill":true,"tension":0.4}]}}

Sales Volume (bar):
  {"type":"bar","data":{"labels":{{json marketDataJson.monthLabels}},"datasets":[{"label":"Sales Volume","data":{{json marketDataJson.soldVolumes}},"backgroundColor":"{{brandColorPrimary}}"}]}}

Days on Market (bar):
  {"type":"bar","data":{"labels":{{json marketDataJson.monthLabels}},"datasets":[{"label":"Days on Market","data":{{json marketDataJson.daysOnMarket}},"backgroundColor":"{{brandColorSecondary}}"}]}}

---

## Common Patterns

JSON Data -> Charts:
  1. Generate Text -> JSON with same-length arrays
  2. Generate Chart x N -> {{json variable.field}} references

Google Images -> Property Photos:
  1. searchGoogleImages -> comp1Images (array with url field)
  2. Run Code -> return { comp1PhotoUrl: inputs.comp1Images[0]?.url || '' }
  3. Comp card template uses {{this.photoUrl}} with onerror fallback to heroImageUrl

Dynamic Branded PDF:
  1. Inject brand vars from website (agentName, logoUrl, brandColorPrimary...)
  2. Generate Text -> market JSON + comparable sales JSON
  3. searchGoogleImages x N -> comp photos
  4. Run Code -> extract photo URLs, merge into comparableSales array
  5. Generate Chart x 3 -> chart images using brand colors
  6. Generate Asset HTML -> PDF with CSS custom properties for brand colors

---

## LFS Variables (Large File Storage)

searchGoogleImages and similar blocks store results as LFS variables.
LFS variables CANNOT be accessed via {{comp1Images.[0].url}} in Generate Asset templates.

CORRECT PATTERN - extract via Generate Text (Haiku):
  Block: getComp1Photo (Generate Text, Haiku, max 256 tokens)
  Prompt: "From this JSON data, extract and return the URL of the first image result.
           Return ONLY the URL with no other text or explanation.
           {{comp1Images}}"
  Output variable: comp1PhotoUrl
  Cost: ~$0.003 per extraction (10x cheaper than Sonnet)

This works because Generate Text blocks CAN access LFS variable content inline.
The output (a plain URL string) is then usable anywhere in the workflow.

---

## Domain.com.au Scrape Pattern

URL: https://www.domain.com.au/sold-listings/{{suburb}}-{{state}}-{{postcode}}/?property-type={{domainPropertyType}}

Despite Domain using JS rendering, the static HTML contains enough structured data
(addresses, bedrooms, bathrooms, land size, sale dates) for AI analysis.
Prices are often withheld on recent sales - this is expected, not a bug.

Property type mapping (use Haiku block before scrapeUrl):
  Prompt: "Convert this property type to Domain.com.au URL format.
           Property type: {{propertyType}}
           Return ONLY: house / unit+apartment / townhouse / acreage-semi-rural / land"
  Output variable: domainPropertyType

REA URL issue: realestate.com.au URLs with commas (e.g. in-Collaroy,+NSW+2097)
get split by MindStudio's scrapeUrl block. Use Domain instead.

---

## JSON Output Reliability

MindStudio auto-parses Generate Text output as JSON object if prompt ends with:
  "Output ONLY valid JSON. No markdown fences. No code fences. No explanation."

If model returns non-standard JSON, MindStudio attempts autohealing - unreliable.
Watch debugger for: "Model did not return valid JSON, attempting to autoheal"
Fix: tighten the prompt, reduce temperature, use Claude Sonnet not Haiku for complex JSON.

Access parsed JSON fields: {{comparablesJson.comparableProperties.[0].address}}
Note: array syntax requires dot before bracket: .[0] not [0]

---

## Null-Safe Template Rendering

Wrap ENTIRE container divs in {{#if}}, not just values:

WRONG:
  <div class="price-box">
    <label>LOW PRICE</label>
    <p>{{#if comparablesJson.priceRangeLow}}{{comparablesJson.priceRangeLow}}{{/if}}</p>
  </div>
  (renders empty box with orphaned label)

CORRECT:
  {{#if comparablesJson.priceRangeLow}}
  <div class="price-box">
    <label>LOW PRICE</label>
    <p>{{comparablesJson.priceRangeLow}}</p>
  </div>
  {{/if}}

---

## AIRE CMA Builder - Current Working Config (April 2026)

Agent ID: 92230109-0f67-4c4f-bd1d-a97a0316a273

Workflow:
1. User Input: propertyAddress, suburb, state, postcode, propertyType, bedrooms, bathrooms, carSpaces, landSize, estimatedPrice
2. searchGoogle: suburbSearch (still in flow but removed from main prompt)
3. Generate Text (Haiku): domainPropertyType (maps propertyType to Domain URL slug)
4. Scrape URL: recentSales (https://www.domain.com.au/sold-listings/{{suburb}}-{{state}}-{{postcode}}/?property-type={{domainPropertyType}})
5. Generate Text (Claude 4.5 Sonnet, temp 0.3): comparablesJson (6 comps, null prices when withheld)
6. searchGoogleImages x 6: comp1Images through comp6Images (LFS)
7. Generate Text (Haiku) x 6: comp1PhotoUrl through comp6PhotoUrl (URL extraction from LFS)
8. Generate Asset: reportPdf (HTML/Handlebars, PDF output)
9. Display Content: download link

Cost: ~$0.097/run (floor with Domain scrape architecture)
  Sonnet (comparablesJson): ~$0.074 - 17,626 prompt tokens (Domain HTML is large)
  Haiku (7 blocks total): ~$0.022
  scrapeUrl: $0.0005

Key prompt rules that prevent fabrication:
  CRITICAL: If soldPrice is "Price Withheld" for all comparables:
  - Set medianCompPrice, priceRangeLow, priceRangeHigh to null
  - Set recommendedValue based on agent estimate only
  - Do NOT invent price figures

---

## Tips

- Name variables clearly: marketDataJson not data, comp1PhotoUrl not img
- Test via CLI: mindstudio.exe searchGoogleImages --query "10 Collaroy St house photo"
- JSON prompts: end with "Output ONLY valid JSON. No markdown. No code fences. No explanation."
- CSS brand colors: :root { --brand-primary: {{brandColorPrimary}}; }
- Image fallbacks: onerror="this.src='{{heroImageUrl}}'" on all comp photo imgs
- Space out multiple search blocks with Checkpoint blocks (rate limits)
- LJ Hooker defaults: brandColorPrimary=#00A651, brandColorSecondary=#FFD700
- Haiku for simple extractions (photo URLs, type mappings): ~$0.003/call, 10x cheaper than Sonnet
- Sonnet for complex analysis (comparable sales JSON): ~$0.074/call with large scrape data
- Domain scrape HTML is large (~13,000 tokens) - this is the cost floor with this architecture
- getCompPhoto blocks: set max response to 256 tokens (not 64,000) to avoid waste
- State inputs: use Select dropdown (NSW, VIC, QLD...) to prevent typo issues like "NSw"
- soldPrice as string: if returning "Price Withheld", don't prefix $ in template
- soldPrice as integer: prefix $ in template (${{this.soldPrice}})
- JSON example in prompt: include full example object so model knows exact field format required
