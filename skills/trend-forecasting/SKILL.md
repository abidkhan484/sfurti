---
name: trend-forecasting
description: Use when researching viral and trending signals for toy products targeting Bangladesh — TikTok viral content, Facebook/Instagram ad momentum, Google Trends trajectory, and AliExpress order-volume spikes. Run as the Trend Analyst subagent in the parallel product research pipeline.
---

# Trend Forecasting — Trend Analyst Subagent

This skill is executed by the **Trend Analyst subagent** in the parallel product
research pipeline. It runs concurrently with the Source Scout and Ad Spy
subagents. Do not wait for them — research your signals independently and
report back to the master agent.

**REQUIRED BACKGROUND:** Read `cognitive-dev-business-mission` first. Trends that
drift away from "effortful, hands-on cognition" are still worth flagging as
commercial signals — but label them clearly so the Scorer can apply the mission
discount appropriately.

---

## Subagent role in the pipeline

```
Master Agent
  ├── Source Scout         → finds products on sourcing platforms (running in parallel)
  ├── [YOU] Trend Analyst  → finds viral + trend signals
  └── Ad Spy               → finds competitor ads (running in parallel)
```

Your output is merged with Source Scout's product list and Ad Spy's competitor
data by the master agent before scoring. Your job is to assess **viral momentum
and search-volume trajectory** — not to find products.

---

## Signal sources — search all of these

### Tier 1 — Highest weight (viral / real-time)

| Source | What to look for |
|---|---|
| **TikTok search** | Search `#toytok`, `#toysoftiktok`, `#kidstoys`, product-specific hashtags; look for posts >100K views in last 30 days; note engagement rate (likes+comments / views) |
| **Facebook / Instagram** | Viral toy product posts (not ads) shared in BD parenting groups; note share counts and comment sentiment |
| **AliExpress order velocity** | Products with >500 orders on a single listing AND a rapid review accumulation (check "sort by newest" reviews) — spikes indicate going viral |

### Tier 2 — Medium weight (sustained trend)

| Source | What to look for |
|---|---|
| **Google Trends** | 12-month trajectory for product-specific terms; filter to Bangladesh first, then India+Pakistan as BD proxy; look for upward slope, not just high volume |
| **Amazon (US/UK)** | "Best Seller" + "New Release" badges in Toys > Games & Puzzles, STEM; fast review accumulation on new entrants |
| **Pinterest Trends** | Search-volume signals on toy boards; growing saves on puzzle/STEM/wooden toy content |

### Tier 3 — Background context (market readiness)

| Source | What to look for |
|---|---|
| **BD Facebook parenting groups** | Are parents actively asking for or talking about this product type? |
| **BD F-commerce pages** | Are competitors already stocking similar products? High engagement = validated demand |

---

## Per-product signal assessment

For each product keyword/category you research, capture:

| Field | Notes |
|---|---|
| **TikTok momentum** | View count of top 3 posts in last 30 days; hashtag size; engagement rate |
| **FB viral evidence** | Share count on viral posts; comment tone (want-to-buy vs. curiosity) |
| **Google Trends slope** | Flat / rising slowly / rising sharply / already peaked |
| **AliExpress velocity** | Total orders on top listing; review growth rate if visible |
| **BD local signal** | Any direct BD evidence (group posts, competitor listings, news) |
| **Spike vs. shift** | Is this a one-time viral moment or a sustained category shift? |
| **Trend maturity** | Early (few sellers) / growing (many sellers, still rising) / saturated (everyone selling) |

---

## What makes a strong trend signal for this business

**Strong:** TikTok videos showing a child hands-on with the product, high
completion rate (long videos watched fully), comments showing purchase intent
("where can I buy this?"), product appearing in multiple unrelated creators'
feeds within 30 days.

**Weak:** A single viral video with no follow-on content, "cute/novelty" appeal
only with no replay engagement, trend visible only in US/EU markets with no
South/Southeast Asia signal.

---

## Output format (return to master agent)

```
TREND ANALYST RESULTS
Run timestamp: [timestamp]
Products/keywords researched: X

[TABLE]
Product/category | TikTok momentum (H/M/L) | FB viral evidence | Google Trends slope | AliExpress velocity | BD local signal | Spike vs shift | Maturity | Overall trend score (H/M/L)
```

Include a brief note (1–2 sentences) on any product showing **strong** overall
trend signal — what is driving it and whether it looks sustainable.

Flag any strongly trending product that appears **off-mission** — don't hide it,
just label it: "⚠️ trending but off-mission: [reason]". The master agent
and Scorer will handle the tradeoff.

---

## Common mistakes to avoid

- **Don't treat TikTok view count alone as the signal** — engagement rate and
  purchase-intent comments matter more than raw views
- **Don't confuse BD-readiness with global trend strength** — a US-viral toy
  with no BD social signal is a much higher-risk import
- **Don't mark everything as "rising"** — use the full range (flat / rising /
  peaked / declining); a peaked trend is a sourcing trap
- **Don't skip the spike-vs-shift distinction** — a product that went viral for
  one week and then disappeared is fundamentally different from a category in
  sustained growth
