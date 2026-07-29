---
name: competitor-research
description: Use when researching competitor ads and BD market activity for toy products — Meta Ad Library, BD Facebook/Instagram seller pages, what's being actively promoted, at what price, and with what content angles. Run as the Ad Spy subagent in the parallel product research pipeline.
---

# Competitor Research — Ad Spy Subagent

This skill is executed by the **Ad Spy subagent** in the parallel product
research pipeline. It runs concurrently with the Source Scout and Trend Analyst
subagents. Do not wait for them — research your signals independently and
report back to the master agent.

**REQUIRED BACKGROUND:** Read `cognitive-dev-business-mission` first. Your job
is to find what's already being sold to BD parents, what's working in ads, and
where the gaps are — not to evaluate sourcing or trend signals.

---

## Subagent role in the pipeline

```
Master Agent
  ├── Source Scout         → finds products on sourcing platforms (running in parallel)
  ├── Trend Analyst        → finds viral + trend signals (running in parallel)
  └── [YOU] Ad Spy         → finds competitor ads + BD market activity
```

Your output surfaces: (1) what BD sellers are actively spending money to promote,
(2) what content angles work in this market, and (3) which products are already
saturated vs. which have room to enter.

---

## Primary source: Meta Ad Library

The Meta Ad Library is your most important tool. It shows **active paid ads**
— if a BD seller is spending money to promote a product, that's the strongest
possible commercial validation signal.

### How to search the Meta Ad Library

URL: `https://www.facebook.com/ads/library/`

1. Set **Country = Bangladesh**
2. Set **Category = All Ads**
3. Search product-specific terms: "wooden puzzle", "building blocks", "STEM toy", "montessori toy", "brain toy", "logical toy", etc.
4. Also search generic toy-seller terms: "toys BD", "শিশু খেলনা", "বাচ্চাদের খেলনা"

### What to capture per ad found

| Field | Notes |
|---|---|
| **Advertiser name** | Page name running the ad |
| **Product being promoted** | Specific product name/type if visible |
| **Ad start date** | How long has this ad been running? Longer = working |
| **Ad format** | Image / video / carousel — video ads are high signal for visual platforms |
| **Price shown in ad** | BD retail price if listed |
| **CTA / offer** | "Buy now", "Message to order", discount, bundle |
| **Audience signal** | Any visible targeting (kids' age, parenting interest) |
| **Creative angle** | Child playing with it? Parent testimonial? Before/after? Demo? |
| **Mission alignment** | Is the ad angle cognitive-development / screen-time? Or just "fun"? |

---

## Secondary source: BD F-commerce pages

Search Facebook and Instagram for active BD toy sellers:

Search terms: "wooden toys Bangladesh", "montessori toys BD", "educational toys Dhaka", "শিশু শিক্ষামূলক খেলনা"

For each active seller page found:

| Field | Notes |
|---|---|
| **Page name + URL** | |
| **Follower count** | |
| **Top performing posts** | Highest likes/shares/comments in last 60 days |
| **Products actively promoted** | What are they pushing right now? |
| **Price range** | What do they charge in BDT? |
| **Content format** | Video demos / unboxings / testimonials / product photos |
| **Engagement rate** | (likes + comments) / reach — a rough quality signal |
| **Positioning message** | Do they use "cognitive", "screen-time alternative", or just "fun toy"? |

---

## What to identify

### 1. Saturated products
Products where 3+ sellers are running active Meta ads AND pricing is being
competed down. Entering here requires a differentiation angle, not just listing.

### 2. Underserved products
Products with clear demand signals (TikTok momentum, AliExpress orders) but
**no or few active Meta ads** in Bangladesh. This is the opportunity gap.

### 3. Winning ad angles
Which creative formats and messaging angles are getting the most engagement
in BD toy ads right now? This tells you how to market, not just what to sell.

### 4. Pricing intel
What BD retail price range are competitors charging for similar products?
Cross-reference with the $5 import cost ceiling to validate margin math.

---

## Output format (return to master agent)

```
AD SPY RESULTS
Run timestamp: [timestamp]
Meta ads reviewed: X | Seller pages reviewed: Y

ACTIVE ADS TABLE
Advertiser | Product | Running since | Format | BD Price | Creative angle | Mission flag

SELLER PAGES TABLE
Page | Followers | Top product | BDT range | Best content format | Positioning

SYNTHESIS (3 bullets max):
- Saturated: [products already crowded]
- Opportunity gaps: [products with demand but thin competition in BD ads]
- Winning ad angle: [what format/message is working in this market right now]
```

---

## Common mistakes to avoid

- **Don't skip Meta Ad Library** — organic posts lie; paid ads reveal where
  sellers are actually putting money, which is the real demand signal
- **Don't confuse follower count with ad effectiveness** — a page with 10K
  followers running video ads for 3 months straight is more informative than
  a 200K follower page that never advertises
- **Don't treat any product as "uncontested" without checking** — search
  multiple keyword variations before concluding there's no competition
- **Don't ignore the content angle** — the creative format (video demo vs.
  unboxing vs. parent testimonial) is as valuable to the business as the product
  itself, because these channels are visual-first
