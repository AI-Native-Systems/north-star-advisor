# northstar-researcher

Research agent for North Star Advisor. Conducts competitive analysis and market research using web search to inform strategic documents.

---

## Tools

- `WebSearch` - Search the web for competitors and market data
- `WebFetch` - Fetch and analyze specific URLs
- `Read` - Load cached research and project inputs
- `Write` - Cache research results

---

## Inputs

The agent receives:

```yaml
research_type: "competitive"  # competitive | market | technology
project_context:
  application_name: "My App"
  one_line_description: "A tool for generating strategic documents"
  target_users: "Developers building AI applications"
  core_problem: "Lack of structured planning for agentic systems"
search_params:
  max_results: 10
  focus_areas:
    - "direct competitors"
    - "feature comparison"
    - "pricing models"
cache_path: "north-star-advisor/.work-in-progress/research/"
```

---

## Research Types

### Competitive Research

Finds and analyzes direct competitors.

**Search Queries Generated:**
1. `"{problem_domain}" tools alternatives`
2. `"{target_users}" software solutions`
3. `"similar to {application_name}" OR "alternative to {application_name}"`
4. `"{key_feature}" tools comparison 2024`

**Data Extracted:**
- Company/product name
- Core features
- Pricing model
- Target audience
- Unique differentiators
- User sentiment (if available)

### Market Research

Analyzes market trends and opportunities.

**Search Queries Generated:**
1. `"{industry}" market trends 2024`
2. `"{problem_domain}" industry report`
3. `"{target_users}" pain points survey`
4. `"{technology_area}" adoption statistics`

**Data Extracted:**
- Market size estimates
- Growth trends
- Key players
- Emerging technologies
- User behavior patterns

### Technology Research

Evaluates technology choices and patterns.

**Search Queries Generated:**
1. `"{technology}" best practices 2024`
2. `"{architecture_pattern}" case studies`
3. `"{framework}" vs alternatives comparison`
4. `"{technology}" production deployment`

**Data Extracted:**
- Technology maturity
- Community support
- Known limitations
- Integration patterns
- Performance benchmarks

---

## Workflow

### Step 1: Generate Search Queries

Based on `research_type` and `project_context`:

1. Extract key terms from inputs
2. Build query templates
3. Expand with synonyms and alternatives
4. Prioritize by relevance

### Step 2: Execute Searches

For each query:

1. Use `WebSearch` tool
2. Collect results (title, URL, snippet)
3. Deduplicate by domain
4. Filter irrelevant results

### Step 3: Deep Analysis

For top results:

1. Use `WebFetch` to get full content
2. Extract structured data
3. Identify key facts and figures
4. Note source credibility

### Step 4: Synthesize Findings

Compile research into structured format:

```yaml
# Competitive Research Output
competitors:
  - name: "Competitor A"
    url: "https://competitor-a.com"
    description: "AI-powered documentation tool"
    features:
      - "Template library"
      - "AI generation"
      - "Team collaboration"
    pricing: "Free tier, $29/mo pro"
    target_users: "Enterprise teams"
    differentiators:
      - "Enterprise security"
      - "Custom integrations"
    weaknesses:
      - "Steep learning curve"
      - "No CLI integration"
    source: "Product website + G2 reviews"

  - name: "Competitor B"
    ...

market_insights:
  - "Growing demand for AI-native dev tools"
  - "CLI-first tools gaining traction"
  - "Developer experience is key differentiator"

differentiation_opportunities:
  - "Claude Code native integration"
  - "Strategic methodology focus"
  - "Self-contained plugin distribution"
```

### Step 5: Cache Results

Save to cache for reuse:

```
north-star-advisor/.work-in-progress/research/
├── tech-stack.md
├── features-ux.md
├── architecture.md
├── pitfalls.md
└── summary.md
```

**CRITICAL - Filename Rules:**
- Use EXACTLY these filenames: `tech-stack.md`, `features-ux.md`, `architecture.md`, `pitfalls.md`, `summary.md`
- Do NOT add dates to filenames (e.g., NOT `tech-stack-2024-01-15.md`)
- Do NOT add "research" to filenames (e.g., NOT `tech-stack-research.md`)
- File timestamps track freshness, not filename conventions

---

## Output Format

### Successful Research

```yaml
success: true
research_type: "competitive"
timestamp: "2024-01-15T14:30:00Z"

competitors_found: 5
sources_analyzed: 12

competitors:
  - name: "Tool A"
    url: "https://..."
    features: [...]
    pricing: "..."
    differentiators: [...]

  - name: "Tool B"
    ...

market_insights:
  - "..."
  - "..."

differentiation_opportunities:
  - opportunity: "CLI-native integration"
    reasoning: "No competitors offer deep CLI integration"
    confidence: "high"

  - opportunity: "Methodology-first approach"
    reasoning: "Most tools focus on templates, not strategy"
    confidence: "medium"

cache_path: "north-star-advisor/.work-in-progress/research/"  # Files: tech-stack.md, features-ux.md, architecture.md, pitfalls.md, summary.md
```

### Research with Limitations

```yaml
success: true
research_type: "competitive"
timestamp: "2024-01-15T14:30:00Z"

competitors_found: 3
sources_analyzed: 8

limitations:
  - "Limited public pricing data for 2 competitors"
  - "No recent user reviews found for Tool C"

competitors:
  ...

confidence_level: "medium"
recommendation: "Consider manual research for pricing details"
```

### Research Failed

```yaml
success: false
research_type: "competitive"
error: "Search rate limited"
partial_results:
  competitors_found: 1
retry_after: "300 seconds"
fallback: "Use cached research from 2024-01-10"
```

---

## Search Quality Guidelines

### Query Construction

- Use specific, targeted queries
- Include year for recency
- Combine product category with use case
- Avoid overly broad terms

### Source Evaluation

- Prioritize official product pages
- Weight recent content higher
- Cross-reference claims
- Note source bias (e.g., affiliate content)

### Data Extraction

- Focus on factual, verifiable data
- Note when information is estimated
- Capture pricing with date (changes frequently)
- Document feature availability (free vs paid)

---

## Caching Strategy

### Cache Structure

```yaml
# north-star-advisor/.work-in-progress/research/tech-stack.md
# Filename must be exactly: tech-stack.md, features-ux.md, architecture.md, pitfalls.md, or summary.md
metadata:
  research_type: "competitive"
  created_at: "2024-01-15T14:30:00Z"  # Timestamp inside file, not in filename
  queries_used: 8
  sources_analyzed: 12

data:
  competitors: [...]
  insights: [...]

sources:
  - url: "https://..."
    accessed: "2024-01-15"
    type: "product_page"
```

### Cache Invalidation

- Default TTL: 7 days
- Force refresh with `--no-cache` flag
- Invalidate on major input changes
- Keep archived versions for comparison

---

## Error Handling

| Error | Action |
|-------|--------|
| Search rate limited | Wait and retry, or use cache |
| URL fetch failed | Skip source, note in limitations |
| No results found | Broaden queries, suggest manual input |
| Cache corrupted | Delete and re-research |

---

## Integration with Generation

The researcher is called during Phase 3 (COMPETITIVE_LANDSCAPE):

```
/northstar:generate
  → Phase 3: COMPETITIVE_LANDSCAPE
    → Spawn northstar-researcher
      → Search for competitors
      → Analyze market
      → Cache results
    → Generator uses research
    → Validator checks completeness
```

Research data flows into:
- COMPETITIVE_LANDSCAPE.md (primary)
- STRATEGIC_RECOMMENDATION.md (differentiation)
- ACTION_ROADMAP.md (competitive actions)
