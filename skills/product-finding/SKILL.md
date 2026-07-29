---
name: product-finding
description: Use when searching for new products to source from abroad and resell in Bangladesh — covers sourcing platforms (Alibaba, AliExpress, 1688, DHgate), what data to collect per product, hard cost filters, and how to hand off results to the scoring subagent.
---

# Product Finding — Source Scout Subagent

This skill is executed by the **Source Scout subagent** in the parallel product
research pipeline. It runs concurrently with the Trend Analyst and Ad Spy
subagents. Do not wait for them — run your sourcing searches independently and
report back your raw candidates to the master agent.

**REQUIRED BACKGROUND:** Read `cognitive-dev-business-mission` first to understand
which product types are on-mission. This skill tells you *where and how* to find them.

---

## Subagent role in the pipeline

```
Master Agent
  ├── [YOU] Source Scout   → finds products on sourcing platforms
  ├── Trend Analyst        → finds viral signals (running in parallel)
  └── Ad Spy               → finds competitor ads (running in parallel)
```

When you finish, return your findings to the master agent in the structured
format at the bottom of this skill. The master agent will merge your data with
Trend Analyst and Ad Spy results before handing off to the Scorer.

---

## Sourcing platforms to search

Search **all four** unless the master agent restricts scope:

| Platform | URL | Focus |
|---|---|---|
| **AliExpress** | aliexpress.com | Retail pricing, review count, order volume, photo quality |
| **Alibaba** | alibaba.com | Wholesale price (MOQ), supplier rating, manufacturing specs |
| **1688** | 1688.com | Factory-direct pricing (cheapest tier), use Google Translate |
| **DHgate** | dhgate.com | Mid-tier wholesale, faster shipping options |

Search each platform using the seed keywords from `settings.toml [product_search].seed_keywords`.
If the master agent provided a guided niche/age/theme, use that instead.

---

## Hard filters — discard before reporting

Discard any product that fails any of these before adding to your results:

| Filter | Rule |
|---|---|
| **Price ceiling** | Unit cost > $5 USD at the lowest wholesale tier → discard |
| **Physical only** | Digital, app, or screen-based product → discard |
| **Luck-based** | Pure dice/card randomness with no skill element → discard |
| **Passive** | Sticker books, coloring books, consumables with no replay → discard |
| **Age band** | No specific age range stated by the seller → flag ⚠️ (don't discard) |

---

## What to capture per candidate

For every product that passes the hard filters, record:

| Field | Required | Notes |
|---|---|---|
| Product name | ✅ | Specific — "7-piece wooden tangram set", not "puzzle" |
| Platform | ✅ | AliExpress / Alibaba / 1688 / DHgate |
| Source URL | ✅ | Direct link to the listing |
| Sourcing price (USD) | ✅ | Unit cost at lowest available quantity; label as "est." if range |
| MOQ | ✅ | Minimum order quantity |
| Order volume signal | ✅ | Number of orders/reviews on listing (proxy for demand) |
| Seller rating | ✅ | Star rating or trust badge |
| Age band | ✅ | From listing; flag ⚠️ if missing |
| Product dimensions / weight | ✅ | For shipping cost estimation downstream |
| Photo / video quality | ✅ | Rate High / Medium / Low — critical for social commerce |
| Mission fit (quick check) | ✅ | One sentence: "builds X through Y" or "borderline because Z" |

---

## Scoring signal to pass forward

For each candidate also note:
- **Bestseller / Choice badge** on AliExpress (strong demand signal)
- **>500 orders** on a single AliExpress listing (high velocity)
- **>4.5 star** seller rating on Alibaba (sourcing reliability)
- Any **video content** available on the listing (valuable for TikTok/Reel repurposing)

These signals feed directly into the Trend Analyst and Scorer's work — surface
them even if you can't evaluate their weight yourself.

---

## Output format (return to master agent)

```
SOURCE SCOUT RESULTS
Run timestamp: [timestamp]
Total found: X | Passed filters: Y | Discarded: Z

[TABLE]
# | Product | Platform | Price USD | MOQ | Orders | Rating | Age | Weight | Photo Quality | Mission (quick) | URL
```

List all passing candidates. Do NOT score them — scoring is the Scorer
subagent's job. Do NOT wait for Trend Analyst or Ad Spy results.

---

## Common mistakes to avoid

- **Don't search only one platform** — AliExpress shows retail demand; 1688 gives factory cost; both matter
- **Don't accept "under $5 for 100 units"** — the $5 limit is the per-unit cost at the minimum viable order quantity
- **Don't skip photo quality** — these products sell on Facebook/TikTok; a toy with poor listing photos will be hard to repurpose for content
- **Don't score** — just collect; the Scorer subagent handles weighting
