# Sfurti RnD — Agent Rules & Guardrails

This file is automatically loaded whenever the agent works in the `/home/polymath/sfurti`
workspace. All rules here apply to every task in this project unless the user
explicitly overrides them in the chat.

---

## 1. Always load the business mission first

Before doing **any task** for the Sfurti business — product research, campaign writing,
sales copy, retention messaging, trend analysis, competitor research, or brand strategy
— you MUST read:

```
/home/polymath/sfurti/skills/sfurti-business-mission/SKILL.md
```

This skill is the shared mission reference for **all business functions**, not just
product work. It is the tiebreaker when any criteria conflict with the mission.
Do not skip this step, even for "quick" tasks.

For multi-agent pipeline runs, also read:

```
/home/polymath/sfurti/skills/master-agent-orchestration/SKILL.md
```

---

## 2. Mission guardrails — hard stops

The following actions are **prohibited** without explicit user confirmation:

### Product tasks
- **Never shortlist a digital or app-based product** as a core recommendation.
  Screen-based "educational" tools are off-mission by definition. Flag the mismatch
  and ask the user whether they want to formally expand scope.

- **Never shortlist a luck-based game** (pure dice/card randomness with no skill
  or strategy element) as an on-mission product.

- **Never shortlist a sensory or novelty product that does not require the child
  to solve a problem.** Fidget toys, kinetic sand, squishies, pop-its, and similar
  products may be commercially attractive but they are off-mission. If the child
  does not have to *figure something out* using their hands and mind together,
  the product is off-mission. Flag it; do not shortlist it as a core product.

- **Never expand the product roadmap** beyond what is defined in the mission skill
  without asking the user first. "This category seems related" is not sufficient
  justification — ask.

- **Never recommend a product without a source URL.** Every candidate must have
  a verifiable sourcing reference (Alibaba, AliExpress, 1688, DHgate, competitor page, etc.).

- **Never include a sourcing URL that has not been verified as live.** A 404,
  server error, redirect to homepage, or "item removed" page is a hard discard.
  The Source Scout subagent must verify every URL before reporting it. The Scorer
  must reject any product whose URL status is not `✅ Verified`.

- **Never omit the mission flag** from a product scoring table. Even if a product
  scores 9/10 commercially, its mission alignment must be stated explicitly.

- **Never recommend a product with a sourcing cost above $5 USD per unit** without
  explicit user approval. The $5 ceiling is a hard business constraint.

- **Never include customs, BSTI, or import duty analysis.** This is explicitly
  out of scope. If the user asks, acknowledge the gap and offer to address it
  separately if they want to formally add it to scope.

### Marketing and content tasks
- **Never run a sales campaign without verified stock behind it.** A purchase offer
  requires an in-stock product. Awareness content and community content may run
  independently of product availability.

- **Never make unverified developmental or medical claims.** "Research shows" requires
  a citable source. "Clinically proven" or "scientifically guaranteed" are prohibited
  without specific citations.

- **Never shame parents or children** for screen use. Frame Sfurti's approach as
  building something positive, not correcting a failure.

- **Never use fear-mongering or catastrophising language** about child development.

- **Never position competitors as bad.** Only position Sfurti's approach as good.

---

## 3. Output standards — product research tasks

Every product research output must include:

| Field | Required |
|---|---|
| Product name (specific, not generic) | ✅ |
| Target age band (specific range, not "all ages") | ✅ |
| Sourcing price USD (at unit level, not bulk total) | ✅ |
| BD retail price estimate (BDT) | ✅ |
| Estimated gross margin % | ✅ |
| Problem-solving mechanism (what the child must mentally do — not just "educational") | ✅ |
| Mission flag (✅ on-mission / ⚠️ borderline / ❌ off-mission) | ✅ |
| Sourcing URL (Alibaba / AliExpress / 1688 / DHgate link) | ✅ |
| URL verification status (✅ Verified / ⚠️ Stock risk) | ✅ |
| Viral / trend signal (TikTok, Google Trends, or order volume) | ✅ |
| Evidence tier (🔥 all-three / ✅ opportunity / ⚠️ validate / 🔍 low) | ✅ |

If any field is unknown or unverifiable, say so explicitly — do not leave it
blank or fill it with a guess.

---

## 4. Language and tone guardrails

- Write all **agent and internal outputs** in **English**.
- Write all **consumer-facing copy** in **Bangla** unless the user specifies otherwise.
- For content tasks: identify the content pillar and voice mode (nurture / convert)
  before writing anything. Both are defined in the mission skill.
- Avoid phrases like "educational toy" without specifying the cognitive mechanism.
  "This builds spatial reasoning by requiring the child to rotate and fit 3D pieces"
  is acceptable. "Educational wooden toy" is not — it is meaningless.
- Do not use vague qualifiers ("probably good," "might work") in scored outputs.
  Use the scoring rubric from `product-scoring/SKILL.md` and commit to a number.
- When stating margin, always show the math: sourcing cost → BD retail → margin %.

---

## 5. Skills available in this workspace

All skills live in `/home/polymath/sfurti/skills/`. Load them explicitly when relevant:

| Skill file | Role | When to load |
|---|---|---|
| `sfurti-business-mission/SKILL.md` | Mission reference for all tasks | Always — before any task |
| `master-agent-orchestration/SKILL.md` | Orchestration rules | Before running the parallel pipeline |
| `product-finding/SKILL.md` | Source Scout subagent | When sourcing products from Alibaba/AliExpress/1688/DHgate |
| `product-scoring/SKILL.md` | Scorer subagent | When cross-referencing results and ranking candidates |
| `trend-forecasting/SKILL.md` | Trend Analyst subagent | When researching TikTok/Google Trends/AliExpress velocity |
| `competitor-research/SKILL.md` | Ad Spy subagent | When searching Meta Ad Library and BD seller pages |

---

## 6. Scope boundaries

This project covers **only** the Bangladesh cognitive-development business (Sfurti).
Do not apply these rules or skills to other projects or conversations.

If a task arrives that is clearly out of scope (e.g., a general coding task),
confirm with the user whether they intended it for this project context before
applying these guardrails.
