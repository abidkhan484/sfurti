---
name: product-finding
description: Use when searching for new products to source from abroad and resell in Bangladesh — covers sourcing platforms (Alibaba, AliExpress, 1688, DHgate), mandatory URL verification for every listing, problem-solving product search intent, hard cost filters, and how to hand off verified results to the scoring subagent.
---

# Product Finding — Source Scout Subagent

This skill is executed by the **Source Scout subagent** in the parallel product
research pipeline. It runs concurrently with the Trend Analyst and Ad Spy
subagents. Do not wait for them — run your sourcing searches independently and
report back your raw candidates to the master agent.

**REQUIRED BACKGROUND:** Read `sfurti-business-mission` first to understand
which product types are on-mission. This skill tells you *where and how* to find them.

---

## Subagent role in the pipeline

```
Master Agent
  ├── [YOU] Source Scout   → finds + VERIFIES products on sourcing platforms
  ├── Trend Analyst        → finds viral signals (running in parallel)
  └── Ad Spy               → finds competitor ads (running in parallel)
```

When you finish, return your findings to the master agent in the structured
format at the bottom of this skill. The master agent will merge your data with
Trend Analyst and Ad Spy results before handing off to the Scorer.

---

## Search goal — problem-solving products first

**Your search is not a generic toy search.** The goal is to find products that
**develop problem-solving capabilities** in children — physical, hands-on products
that require the child to think, plan, attempt, fail, and try again.

Before running any search, internalise this intent:

> "I am looking for products where a child must *solve something* — arrange
> pieces, build a structure, find a pattern, complete a sequence, or navigate
> a puzzle — using their hands and their mind together."

Products that do not require the child to solve anything are off-mission. Apply
this lens when choosing search terms and when reviewing results.

**Problem-solving product examples (on-mission):**
- Jigsaw / pattern puzzles → child must orient, trial-and-error, persist
- Construction / building sets → child must plan, stack, balance
- Tangrams, magnetic tiles → child must visualise and rotate mentally
- Maze/logic boards → child must plan a path
- Sorting / matching toys (for younger ages) → child must categorise and decide
- STEM kits requiring assembly → child must follow and troubleshoot a sequence

**Off-mission examples (do not search for these):**
- Fidget / sensory toys (no problem to solve, no cognitive demand)
- Collectibles, blind bags (novelty only)
- Passive sand/kinetic play (sensory, not problem-solving)
- Luck-based games with no strategy

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
| **Problem-solving check** | Product requires no cognitive effort from the child (pure novelty, sensory, luck-based) → discard |
| **Luck-based** | Pure dice/card randomness with no skill element → discard |
| **Passive** | Sticker books, coloring books, consumables with no replay → discard |
| **URL invalid** | Listing URL returns 404, error, or cannot be verified as live → discard (see URL verification section) |
| **Age band** | No specific age range stated by the seller → flag ⚠️ (don't discard) |

---

## ⚠️ MANDATORY: URL verification for every listing

**This step is not optional.** Every product URL you include in your results
must be verified as a live, accessible page. Dead links corrupt the entire
pipeline — the Scorer, the master agent, and the user will all receive a broken
reference.

### Verification protocol

For each candidate product URL:

1. **Attempt to load the page** — visit the URL directly
2. **Check the response:**
   - ✅ **HTTP 200 + product page loads** → URL is verified; include the product
   - ❌ **HTTP 404 (Page Not Found)** → discard the product entirely; do not include it
   - ❌ **HTTP 5xx (Server Error)** → discard; retry once after 30 seconds; if still failing, discard
   - ❌ **Redirect to homepage or search page** (not the product) → discard
   - ❌ **"Product unavailable", "Item removed", "Sold out permanently"** → discard
   - ⚠️ **"Temporarily out of stock" with listing still active** → keep, flag as ⚠️ stock risk

3. **Confirm the page shows the correct product** — not a redirect to an unrelated listing

### What to record about URL verification

For every product in your output, record:

| Verification status | Meaning |
|---|---|
| `✅ Verified` | Page loaded, product visible, correct listing |
| `⚠️ Stock risk` | Page loads but item temporarily out of stock |
| `❌ Removed` | Product discarded — do not include in results |

### Common verification traps

- **AliExpress** sometimes redirects removed listings to the store homepage — confirm you are on a product page, not a seller's main page
- **1688** pages may load slowly — retry before discarding
- **Alibaba** supplier inquiry pages look like products but are not — confirm you have a product listing with a stated price
- **DHgate** occasionally returns 200 but shows "this item has been removed" in the body — read the page content, not just the status code

---

## What to capture per candidate

For every product that passes all hard filters AND URL verification, record:

| Field | Required | Notes |
|---|---|---|
| Product name | ✅ | Specific — "7-piece wooden tangram set", not "puzzle" |
| Platform | ✅ | AliExpress / Alibaba / 1688 / DHgate |
| Source URL | ✅ | Direct link to the listing — must be verified live |
| URL verification status | ✅ | ✅ Verified / ⚠️ Stock risk |
| Sourcing price (USD) | ✅ | Unit cost at lowest available quantity; label as "est." if range |
| MOQ | ✅ | Minimum order quantity |
| Order volume signal | ✅ | Number of orders/reviews on listing (proxy for demand) |
| Seller rating | ✅ | Star rating or trust badge |
| Age band | ✅ | From listing; flag ⚠️ if missing |
| Product dimensions / weight | ✅ | For shipping cost estimation downstream |
| Photo / video quality | ✅ | Rate High / Medium / Low — critical for social commerce |
| Problem-solving mechanism | ✅ | Specific: "child must arrange 7 pieces to match target shape"; not just "educational" |
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
Total found: X | Passed filters + URL verified: Y | Discarded (filter fail): Z1 | Discarded (URL invalid): Z2

[TABLE]
# | Product | Platform | Price USD | MOQ | Orders | Rating | Age | Weight | Photo Quality | Problem-solving mechanism | URL | URL Status
```

List all passing candidates. Do NOT score them — scoring is the Scorer
subagent's job. Do NOT wait for Trend Analyst or Ad Spy results.

Include a separate line at the bottom:
```
DISCARDED (URL invalid): [count] products removed due to 404/error/redirect
```

---

## Common mistakes to avoid

- **Don't include an unverified URL** — if you cannot confirm the page is live, the product does not exist in this pipeline
- **Don't search only one platform** — AliExpress shows retail demand; 1688 gives factory cost; both matter
- **Don't accept "under $5 for 100 units"** — the $5 limit is the per-unit cost at the minimum viable order quantity
- **Don't skip photo quality** — these products sell on Facebook/TikTok; a toy with poor listing photos will be hard to repurpose for content
- **Don't use "educational" as the problem-solving mechanism** — it must be specific: what does the child's brain actually *do* with this product?
- **Don't score** — just collect and verify; the Scorer subagent handles weighting
