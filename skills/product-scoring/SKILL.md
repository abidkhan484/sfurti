---
name: product-scoring
description: Use when scoring and ranking product candidates after the Source Scout, Trend Analyst, and Ad Spy subagents have returned their results — cross-references all three data sources, applies a 4-dimension scorecard, and produces a ranked shortlist with a mission flag overlay.
---

# Product Scoring — Scorer Subagent

This skill is executed by the **Scorer subagent** as the final stage of the
parallel product research pipeline. You receive merged data from three upstream
subagents and produce the final ranked shortlist.

**REQUIRED BACKGROUND:** Read `cognitive-dev-business-mission` first. The mission
lens is applied as a flag/overlay on top of commercial scoring — it doesn't
replace the commercial score, but products that fail the mission lens must be
explicitly flagged.

---

## Subagent role in the pipeline

```
Master Agent
  ├── Source Scout results   → products + sourcing data
  ├── Trend Analyst results  → viral momentum + trend signals
  ├── Ad Spy results         → competitor ads + BD market intel
  └── [YOU] Scorer           → cross-references all three → ranked shortlist
```

You receive all three result sets from the master agent. Your job is to:
1. Merge products from Source Scout with their trend and ad signals
2. Score each product on 4 commercial dimensions
3. Apply the mission flag overlay
4. Produce the ranked shortlist in the required output format

---

## Step 1 — Match products across sources

Before scoring, match each Source Scout product to:
- Its **trend signal** from the Trend Analyst (by product name / keyword)
- Its **ad presence** from the Ad Spy (by product type)

If a product appears in all three sources (sourcing + viral + active competitor
ads), it's the highest-confidence candidate. If it only appears in one, flag
the gap.

| Evidence present | Confidence |
|---|---|
| All three sources | 🔥 High confidence |
| Source Scout + Trend Analyst (sourced + viral, no BD ads yet) | ✅ Opportunity |
| Source Scout + Ad Spy (sourced + already sold in BD, no viral) | ⚠️ Validate demand |
| Source Scout only (found but no trend/ad signal) | 🔍 Low — needs more validation |

---

## Step 2 — The scorecard (4 dimensions, 0–10 each)

Score each matched product on these four commercial dimensions:

### 1. Profit Margin Potential (weight: 30%)

Estimate: **BD Retail Price (BDT) − Sourcing Cost (USD converted to BDT) − Shipping est.**

| Score | Meaning |
|---|---|
| 9–10 | >60% gross margin at a realistic BD retail price |
| 7–8 | 45–60% gross margin |
| 5–6 | 30–45% gross margin |
| 3–4 | 15–30% gross margin — thin for a small business |
| 0–2 | <15% or price ceiling makes margin math impossible |

Use competitor BDT prices from Ad Spy as the retail ceiling.
Use 1USD ≈ 110 BDT for conversion (update if settings.toml specifies otherwise).

### 2. Viral Momentum (weight: 30%)

Based directly on Trend Analyst's overall trend score:

| Score | Meaning |
|---|---|
| 9–10 | Strong TikTok momentum + rising Google Trends + AliExpress velocity spike |
| 7–8 | 2 of 3 strong signals; trend is sustained (not a single spike) |
| 5–6 | 1 strong signal; trend is real but limited geographic reach or recency |
| 3–4 | Weak signals; slow-moving category with no clear momentum |
| 0–2 | No trend signal; flat or declining |

If Trend Analyst flagged this as a spike (not a shift), cap the score at 6
regardless of raw momentum strength.

### 3. Shipping Ease (weight: 20%)

Based on Source Scout's weight/dimensions data:

| Score | Meaning |
|---|---|
| 9–10 | Small, lightweight (<500g), no batteries, standard packaging — ideal for air freight |
| 7–8 | Moderate weight (500g–1kg), simple box, no special requirements |
| 5–6 | Heavier or bulkier but still manageable; or requires care in packaging |
| 3–4 | Heavy (>1kg), fragile, or large — adds significant shipping cost per unit |
| 0–2 | Impractical to ship: too heavy, too fragile, hazmat, or oversized |

Note: customs/BSTI checks are **out of scope** per business settings. Do not
score or comment on import duty or certification requirements.

### 4. Visual / Video Appeal (weight: 20%)

How well does this product translate to social-first sales (Facebook, TikTok,
Instagram)? Based on Source Scout's photo quality rating and Ad Spy's ad formats.

| Score | Meaning |
|---|---|
| 9–10 | Product has inherent motion/transformation (builds, stacks, reveals); naturally demo-able on video; bright/appealing colors; existing video ads working |
| 7–8 | Good visual — clearly appealing in photos; some video potential |
| 5–6 | Decent visuals but hard to demo on video; or product is plain/monochrome |
| 3–4 | Low visual interest; would need significant creative effort to make social-friendly |
| 0–2 | Not visual — completely unsuitable for social commerce without major production |

---

## Step 3 — Compute weighted total

```
Total = (Margin × 0.30) + (Viral × 0.30) + (Shipping × 0.20) + (Visual × 0.20)
```

Use weights from `settings.toml [scoring]` if different values are set there.

---

## Step 4 — Apply mission flag overlay

After computing the commercial score, check the mission lens from
`cognitive-dev-business-mission`:

- **✅ On-mission** — product builds effortful hands-on cognition; passes all 4 mission-lens questions
- **⚠️ Borderline** — commercially strong but cognitive mechanism is weak or contested
- **❌ Off-mission** — digital, luck-based, or purely passive

**The mission flag does NOT change the commercial score.** It is displayed
alongside it so the user can see both dimensions and make their own call.
Off-mission products are listed in a separate section, not hidden.

Discard any product whose weighted total < `settings.toml [scoring].min_total_score`.

---

## Output format (return to master agent for final presentation)

### Section 1 — Ranked Shortlist Table

```
Rank | Product | Evidence | Margin | Viral | Shipping | Visual | Total | Mission | Source URL
```

Ranked by Total score, descending. Maximum `shortlist_limit` rows (from settings.toml).

### Section 2 — Top 3 Detail Briefs

For the top 3 products only, add:
- **Why it ranked here** (1 sentence on the strongest driver)
- **Margin math** (cost → retail → estimated margin %)
- **Main risk** (1 sentence: what could go wrong)
- **Content angle** (1 sentence: what video/photo format would work best)

### Section 3 — Off-Mission but Commercially Strong

Products that scored well commercially (total ≥ 6) but received ❌ or ⚠️ mission flag.
List with: name, total score, mission flag reason, commercial case in one sentence.

---

## Common mistakes to avoid

- **Don't average the 4 dimensions equally** — Margin and Viral are each 30%; Visual and Shipping are each 20%
- **Don't let the mission flag inflate or deflate the commercial score** — they are separate outputs
- **Don't guess margin math** — use actual sourcing prices from Source Scout and actual retail prices from Ad Spy; label any estimate as "est."
- **Don't score customs/import risk** — this is explicitly out of scope
- **Don't give every product a 7** — a product with no trend signal should score 0–3 on Viral, not a diplomatic 5
