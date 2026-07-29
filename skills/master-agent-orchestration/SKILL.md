---
name: master-agent-orchestration
description: Use when running the Sfurti parallel product research pipeline — dispatches Source Scout, Trend Analyst, and Ad Spy subagents concurrently, waits for all results, then dispatches the Scorer subagent, and presents the final shortlist. Read this before running any multi-agent product research workflow.
---

# Master Agent Orchestration

This skill defines how the **master agent** coordinates the parallel product
research pipeline for the Sfurti business. The master agent does not do the
research itself — it dispatches, coordinates, and synthesises.

---

## Pipeline architecture

```
                    ┌─────────────────────────────┐
                    │       MASTER AGENT           │
                    │  (reads mission + settings)  │
                    └────────────┬────────────────┘
                                 │ dispatches concurrently
               ┌─────────────────┼─────────────────┐
               ▼                 ▼                  ▼
     ┌──────────────┐  ┌──────────────┐  ┌──────────────┐
     │ SOURCE SCOUT │  │TREND ANALYST │  │   AD SPY     │
     │  (Subagent A)│  │  (Subagent B)│  │  (Subagent C)│
     │              │  │              │  │              │
     │ AliExpress   │  │ TikTok viral │  │ Meta Ad Lib  │
     │ Alibaba      │  │ Google Trends│  │ BD seller    │
     │ 1688         │  │ FB viral     │  │ pages        │
     │ DHgate       │  │ AliEx orders │  │              │
     └──────┬───────┘  └──────┬───────┘  └──────┬───────┘
            │                 │                  │
            └────────────┬────┘──────────────────┘
                         │ master agent merges results
                         ▼
               ┌──────────────────┐
               │     SCORER       │
               │  (Subagent D)    │
               │                  │
               │ Cross-refs all 3 │
               │ Scores 4 dims    │
               │ Applies mission  │
               │ Ranks shortlist  │
               └────────┬─────────┘
                         │
                         ▼
               ┌──────────────────┐
               │  FINAL OUTPUT    │
               │  Ranked table    │
               │  Top 3 briefs    │
               │  Off-mission log │
               │  Next steps      │
               └──────────────────┘
```

---

## Master agent responsibilities

### Before dispatching subagents

1. **Read the mission skill:** `/home/polymath/sfurti/skills/sfurti-business-mission/SKILL.md`
2. **Read settings:** `/home/polymath/sfurti/settings.toml`
3. **Determine mode:**
   - **Autonomous mode** (user said "find me winning toys" or similar) → use seed keywords from settings.toml
   - **Guided mode** (user gave a niche, age group, or theme) → pass that as the search directive to subagents
4. **Compose the subagent briefs** — each subagent needs: the search directive, the settings constraints, and its specific skill file path

### Dispatching subagents

Dispatch **Source Scout, Trend Analyst, and Ad Spy simultaneously** (do not
wait for one before starting the next). Pass each subagent:
- The search directive (keyword or guided theme)
- The relevant skill file path to read
- The settings file path
- Their specific output format requirements

### After receiving subagent results

Wait until **all three subagents** have returned before proceeding.

Then compose the Scorer brief:
- Merge the three result tables into one input
- Match products across sources (Source Scout product ↔ Trend Analyst signal ↔ Ad Spy ad presence)
- Note the confidence tier for each product (all-three vs. two vs. one source)
- Pass the merged data to the Scorer subagent

### After Scorer returns

Present the final output to the user in this sequence:
1. Ranked shortlist table (scores first, clean and scannable)
2. Top 3 expanded briefs (margin math, risk, content angle)
3. Off-mission but commercially strong products (separate, clearly labelled)
4. Next steps (2–3 specific actions)

---

## Subagent brief templates

### Source Scout brief
```
You are the Source Scout subagent for the Sfurti product research pipeline.

READ FIRST:
- /home/polymath/sfurti/skills/sfurti-business-mission/SKILL.md
- /home/polymath/sfurti/skills/product-finding/SKILL.md
- /home/polymath/sfurti/settings.toml

SEARCH DIRECTIVE: [autonomous: use settings.toml seed_keywords | guided: {user-specified theme/niche/age}]

YOUR TASK:
1. Search AliExpress, Alibaba, 1688, and DHgate for problem-solving toy products
   matching the directive. Apply the hard filters in product-finding/SKILL.md.
2. MANDATORY URL VERIFICATION: For every candidate that passes the hard filters,
   visit its URL and confirm the page is live (HTTP 200, product page loads, not
   a 404/error/redirect to homepage). Any URL that fails verification → discard
   the product. Record verification status (✅ Verified / ⚠️ Stock risk) for
   every product you keep.
3. Return results in the Source Scout output format defined in the skill,
   including the URL verification status column and the count of products
   discarded due to invalid URLs.

Do NOT score products. Do NOT wait for other subagents.
Hard limit: up to {raw_candidate_limit from settings.toml} verified candidates.
```

### Trend Analyst brief
```
You are the Trend Analyst subagent for the Sfurti product research pipeline.

READ FIRST:
- /home/polymath/sfurti/skills/sfurti-business-mission/SKILL.md
- /home/polymath/sfurti/skills/trend-forecasting/SKILL.md
- /home/polymath/sfurti/settings.toml

SEARCH DIRECTIVE: [same as Source Scout]

YOUR TASK: Research TikTok viral signals, Google Trends trajectory, Facebook
viral posts, and AliExpress order velocity for toy products matching the
directive. Return your results in the Trend Analyst output format from the
skill. Do NOT score products. Do NOT wait for other subagents.
```

### Ad Spy brief
```
You are the Ad Spy subagent for the Sfurti product research pipeline.

READ FIRST:
- /home/polymath/sfurti/skills/sfurti-business-mission/SKILL.md
- /home/polymath/sfurti/skills/competitor-research/SKILL.md
- /home/polymath/sfurti/settings.toml

SEARCH DIRECTIVE: [same as Source Scout]

YOUR TASK: Search the Meta Ad Library (Bangladesh) and active BD F-commerce
pages. Identify what's being actively promoted, at what price, and with what
content angles. Return your results in the Ad Spy output format from the skill.
Do NOT score products. Do NOT wait for other subagents.
```

### Scorer brief
```
You are the Scorer subagent for the Sfurti product research pipeline.

READ FIRST:
- /home/polymath/sfurti/skills/sfurti-business-mission/SKILL.md
- /home/polymath/sfurti/skills/product-scoring/SKILL.md
- /home/polymath/sfurti/settings.toml

INPUT DATA:
[Master agent inserts merged results from Source Scout + Trend Analyst + Ad Spy here]

YOUR TASK: Match products across all three data sources. Score each on the 4
commercial dimensions (Margin 30%, Viral 30%, Shipping 20%, Visual 20%).
Apply the mission flag overlay. Rank by total score. Return the shortlist in
the Scorer output format from the skill.
```

---

## Guardrails for the master agent

- **Never present partial results** — wait for all three parallel subagents before dispatching the Scorer
- **Never let a subagent score** — Source Scout, Trend Analyst, and Ad Spy collect data only; the Scorer evaluates
- **Never hide off-mission products** — present them in their own section so the user can make an informed override
- **Never include an unverified URL** — every product in the final output must carry a `✅ Verified` status from Source Scout; any product without this status is excluded before scoring
- **Never accept a 404 or error URL** — if the Scorer receives a product with a broken URL, reject it and log it separately; do not score it
- **Never let "no URL" slip through as an oversight** — a product with no sourcing URL is not a product; it is an unverified claim

---

## Handling timeouts / partial results

If one subagent fails to return:
- Wait up to a reasonable timeout (the master agent's judgment)
- If still no result: proceed with available data, note the gap in the output ("Trend signals unavailable for this run — scoring Viral dimension conservatively")
- Never present a complete-seeming shortlist if a key data source is missing without disclosing the gap
