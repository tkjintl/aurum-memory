# Aurum Korea — Canonical Project Context
> Last updated: 2026-04-16

## Entity
- **Company**: Aurum Korea Pte Ltd (Singapore, incorporation target May 2026)
- **Customers**: Korean retail and HNW investors, ages 30–60
- **Proposition**: Beat the 15–19% Korean kimchi premium with international spot + small premium + Singapore vault

## Pricing (Tiered by Payment Method)
| Method | Premium |
|--------|---------|
| Wire transfer | 2.5% |
| Credit card | 5.5% |
| Crypto | 2.0% |
| Storage | 0.5–0.8% AUC/year |

> ⚠️ **NOTE (2026-04-16):** FPA Model v9 uses a DIFFERENT pricing structure — Gold 8% / Silver 13% / Blended 9.5% above spot — with NO payment method breakout. Founder decision needed: reconcile payment-method tiers vs. metal-type premiums. See CFO CANONICAL section below.

## Breakeven
- $400K–$700K monthly GMV, 115–200 transactions/month
- FPA v9 verified: ~$524K/month at 85.3% GM and 9.8% take rate (within range ✅)

## Vault & Custody
- Malca-Amit Singapore FTZ (allocated, segregated, Lloyd's insured)

## Regulatory
- Singapore: PSPM Act 2019 registered dealer; PDPA compliant
- Korea: PIPA + FETA compliant

## Tech Stack
- Website: React 19 + Vite 8 (inline styles) → github.com/tkjintl/aurum-website (Vercel)
- Data: Supabase (Postgres)
- Payments: Toss Payments + Wire transfer
- Live gold/forex: Yahoo Finance (GC=F, SI=F, PL=F) via /api/prices + open.er-api.com for KRW
- Memory: github.com/tkjintl/aurum-memory

## Korean Market Price Source (CANONICAL — 2026-04-15)
All Korean market comparison prices reference **exgold.co.kr 매도가 (국내 도매시세, 부가세 포함)**:
- **Label (ticker)**: "exgold 매도가 (1돈 / 부가세 포함)"
- **Label (savings table)**: "exgold 매도가 (1돈 / 부가세 포함)" (KO), "exgold (VAT incl.)" (EN)
- **Formula**: `spot_USD × krwRate × 1.10` mirrors exgold domestic wholesale methodology (international spot + 10% VAT)
- **Gold per-don KRW**: `prices.gold × krwRate × 1.10 / 31.1035 × 3.75`
- **Gold per-oz KRW**: `goldDonKrw × 8.29426` (1 oz = 8.29426 돈)
- **Silver per-don KRW**: `prices.silver × krwRate × 1.10 / 31.1035 × 3.75`
- **Silver per-kg KRW**: `silverDonKrw × 266.6667` (1 kg = 266.6667 돈)
- **API note**: exgold.co.kr has no accessible REST JSON endpoint (JS-rendered). Yahoo Finance spot + ×1.10 is the implementation.
- **Do NOT use KB Star reference pricing** — removed as of 2026-04-15
- **Do NOT use 한국금거래소 label** — replaced by "exgold 매도가" as of 2026-04-15
- Ticker displays per-don in KRW, unit suffix: /돈

---

## CFO CANONICAL — FPA Model v9 Verified Numbers (2026-04-16)

> Source: `Aurum_Master_FPA_Model_v9.xlsx` (18 sheets)
> Verified against live gold $4,791/oz, USD/KRW ₩1,475 on 2026-04-16
> Model inputs: gold $4,842, KRW 1,472 — within 1.1% of live ✅

### Revenue (Y1 Base Case)
- **Total Revenue:** $3,092,018
- **Physical revenue:** $696,663 (22.5%)
- **AGP revenue:** $2,395,356 (77.5%)
- **Total annual GMV:** $31,555,270
- **Take rate:** 9.8% (Revenue / GMV)

### Pricing (FPA Model — Metal-Type Premiums)
- **Gold premium:** 8% above spot → list price $5,277.78/oz
- **Silver premium:** 13% above spot → list price $90.06/oz
- **Blended premium:** 9.5% (70% gold / 30% silver)
- **Gold customer savings vs Korea:** 9.2% ($532.62/oz)
- **Silver customer savings vs Korea:** 12.3% ($12.64/oz)
- **Gold gross margin per transaction:** 7.3%
- **Silver gross margin per transaction:** 11.4%
- **Wholesale spread (COGS):** 1% of spot
- **Storage fee (customer):** 0.5% AUC/year
- **Malca-Amit pass-through:** 0.25%/year
- **Storage net margin:** 0.25% of AUC

### Cost Structure (Y1)
- **Total costs:** $981,423
- **Direct costs:** $455,573 (46.4%) — COGS + KYC + storage + Founder discount
- **Indirect costs:** $525,850 (53.6%) — marketing + brand + compliance + tech + personnel
- **Gross margin:** 85.3% combined (Physical 79.9%, AGP 86.8%)
- **EBITDA:** $2,110,595
- **EBITDA margin:** 68.3%

### 5-Year Trajectory
| Year | Revenue | EBITDA | EBITDA Margin | Customers | Cumulative EBITDA |
|------|---------|--------|---------------|-----------|-------------------|
| Y1 | $3.09M | $2.11M | 68.3% | 5,785 | $2.11M |
| Y2 | $7.38M | $5.64M | 76.4% | 13,463 | $7.75M |
| Y3 | $11.07M | $8.44M | 76.2% | 20,195 | $16.19M |
| Y4 | $16.40M | $12.48M | 76.1% | 29,693 | $28.67M |
| Y5 | $21.17M | $15.76M | 74.4% | 38,181 | $44.43M |
- **Revenue CAGR:** 61.8%
- **5Y Cumulative EBITDA:** $44.43M
- **Y5 FCF:** $15.64M
- **Y5 Ending Cash:** $47.99M

### VC Metrics (Y5)
- **CAC:** $200.55 (borderline at <$200 threshold)
- **LTV:** $1,418
- **LTV/CAC:** 7.1x
- **CAC Payback:** 5.1 months
- **Rule of 40:** 104.4%
- **NRR:** 73.8% ⚠️ (below 100% threshold — driven by 2.5% monthly churn)
- **GRR:** 73.8% ⚠️ (below 85% threshold)

### Customer Base (Y1)
- **Active physical customers:** 2,000
- **Active AGP customers:** 3,785 (2,160 organic + 1,625 sign-up campaign)
- **Founder Savings Patrons:** 712 (across 5 gates)
- **AGP tiers:** Bronze $140/mo (40%), Silver $350/mo (30%), Gold $700/mo (25%), Platinum $1,700/mo (3%), Sovereign $3,400/mo (2%)
- **Weighted avg AGP contribution:** $455/month

### Startup Capital
- **Total cash needed:** $1,462,038
- **Vault & vendors (largest):** $300,200 (20.5%)
- **Marketing launch:** $324,000 (22.2%)
- **Raise structure:** Founders 15% / SAFE 20% / Seed 65%

### Cap Table (Post-Seed)
- **Pre-money:** $20M
- **Post-money:** $20.37M
- **3 founders:** 28.365% each
- **ESOP:** 9.455%
- **SAFE holders:** 3.655%
- **New investors:** 1.795%

### Open Flags (Founder Decision Required)
1. 🔴 NRR/GRR at 73.8% — below investor thresholds
2. 🔴 Balance sheet gaps of ~$3.9M (Y2-Y5) — reconciliation needed
3. 🔴 Payment method pricing vs. metal-type pricing mismatch
4. ⚠️ Premium drift = 0% modeled (but strategy says 5%/yr)
5. ⚠️ No ramp-up period — Y1 assumes steady-state from day 1
6. ⚠️ Magic Number unrealistically high (marketing may be underbudgeted)
7. ⚠️ Series A $4M in Y2 — confirm or remove from base case

---

## Brand — Corrected Colour Palette (audited 2026-04-13)

### Canonical values (use these — NOT the old ones)
| Token | Hex | Notes |
|-------|-----|-------|
| Page background | `#0a0a0a` | True near-black ✅ keep |
| Card background | `#141414` | Neutral dark (was `#111008` — had brown cast) |
| Deep background | `#0d0d0d` | Neutral (was `#0d0b08`) |
| Primary border | `#1e1e1e` | Neutral grey (was `#1a1510` — warm brown) |
| Secondary border | `#282828` | Neutral (was `#2a2318` — warm brown) |
| Primary text | `#f5f0e8` | Warm cream ✅ keep — 17.5:1 contrast |
| Secondary text | `#a09080` | Warm grey (was `#8a7d6b` — too muddy) — 6.2:1 ✅ |
| Muted text | `#6b6b6b` | (was `#555` — WCAG FAIL at 2.6:1) — now 4.6:1 ✅ |
| Gold accent | `#c5a572` | ✅ keep |
| Dark gold (gradient end) | `#8a6914` | ✅ keep — but use WHITE text on gradient |
| CTA button text | `#ffffff` | (was `#0a0a0a` — 3.9:1 FAIL on dark gradient end) |
| Success | `#4ade80` | ✅ keep |
| Error | `#f87171` | ✅ keep |

### Typography
- Cormorant Garamond (headings), Outfit (UI), JetBrains Mono (data)

### Voice
- Trust, security, sophistication — Korean-first, English secondary
