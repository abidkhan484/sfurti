# Sfurti RnD — Agent Rules & Guardrails

This file is automatically loaded whenever the agent works in the `/home/polymath/sfurti`
workspace. All rules here apply to every task in this project unless the user
explicitly overrides them in the chat.

---

## 1. Always load the business mission first

Before doing any product research, scoring, trend analysis, or competitor work,
you MUST read:

```
/home/polymath/sfurti/skills/cognitive-dev-business-mission/SKILL.md
```

This is the tiebreaker when commercial criteria and mission criteria conflict.
Do not skip this step, even for "quick" lookups.

For multi-agent pipeline runs, also read:

```
/home/polymath/sfurti/skills/master-agent-orchestration/SKILL.md
```

---

## 2. Mission guardrails — hard stops

The following actions are **prohibited** without explicit user confirmation:

- **Never shortlist a digital or app-based product** as a core recommendation.
  Screen-based "educational" tools are off-mission by definition. If asked,
  flag the mismatch and ask the user whether they want to formally expand scope.

- **Never shortlist a luck-based game** (pure dice/card randomness with no skill
  or strategy element) as an on-mission product.

- **Never expand the product roadmap** beyond what is defined in the mission skill
  without asking the user first. "This category seems related" is not sufficient
  justification — ask.

- **Never recommend a product without a source URL.** Every candidate must have
  a verifiable sourcing reference (Alibaba, AliExpress, 1688, DHgate, competitor page, etc.).

- **Never omit the mission flag** from a scoring table. Even if a product scores
  9/10 commercially, its mission alignment must be stated explicitly.

- **Never include customs, BSTI, or import duty analysis.** This is explicitly
  out of scope. If the user asks, acknowledge the gap and offer to address it
  separately if they want to formally add it to scope.

- **Never recommend a product with a sourcing cost above $5 USD per unit** without
  explicit user approval. The $5 ceiling is a hard business constraint.

---

## 3. Output standards — always enforce these

Every product research output must include:

| Field | Required |
|---|---|
| Product name (specific, not generic) | ✅ |
| Target age band (specific range, not "all ages") | ✅ |
| Sourcing price USD (at unit level, not bulk total) | ✅ |
| BD retail price estimate (BDT) | ✅ |
| Estimated gross margin % | ✅ |
| Mission flag (✅ on-mission / ⚠️ borderline / ❌ off-mission) | ✅ |
| Sourcing URL (Alibaba / AliExpress / 1688 / DHgate link) | ✅ |
| Viral / trend signal (TikTok, Google Trends, or order volume) | ✅ |
| Evidence tier (🔥 all-three / ✅ opportunity / ⚠️ validate / 🔍 low) | ✅ |

If any field is unknown or unverifiable, say so explicitly — do not leave it
blank or fill it with a guess.

---

## 4. Language and tone guardrails

- Write all outputs in **English** unless the user requests Bengali.
- When framing product recommendations, lead with **parent benefit** (attention
  span, screen-time antidote) and **social-commerce angle** (how it looks on video,
  what the TikTok/Reel hook would be).
- Avoid phrases like "educational toy" without specifying the cognitive mechanism.
  "This builds spatial reasoning by requiring the child to rotate and fit
  3D pieces" is acceptable. "Educational wooden toy" is not — it's meaningless.
- Do not use vague qualifiers ("probably good," "might work") in scored outputs.
  Use the scoring rubric from `product-scoring/SKILL.md` and commit to a number.
- When stating margin, always show the math: sourcing cost → BD retail → margin %.

---

## 5. Skills available in this workspace

All skills live in `/home/polymath/sfurti/skills/`. Load them explicitly when
relevant:

| Skill file | Role in pipeline | When to load |
|---|---|---|
| `cognitive-dev-business-mission/SKILL.md` | Mission reference | Always — before any task |
| `master-agent-orchestration/SKILL.md` | Orchestration rules | Before running the parallel pipeline |
| `product-finding/SKILL.md` | Source Scout subagent | When sourcing products from Alibaba/AliExpress/1688/DHgate |
| `product-scoring/SKILL.md` | Scorer subagent | When cross-referencing results and ranking candidates |
| `trend-forecasting/SKILL.md` | Trend Analyst subagent | When researching TikTok/Google Trends/AliExpress velocity |
| `competitor-research/SKILL.md` | Ad Spy subagent | When searching Meta Ad Library and BD seller pages |

---

## 6. Scope boundaries

This project covers **only** the Bangladesh cognitive-development / wooden-toys
business. Do not apply these rules or skills to other projects or conversations.

If a task arrives that is clearly out of scope (e.g., a general coding task),
confirm with the user whether they intended it for this project context before
applying these guardrails.
