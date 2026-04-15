# Aurum Korea — Canonical Project Context
> Last updated: 2026-04-15

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

## Breakeven
- $400K–$700K monthly GMV, 115–200 transactions/month

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
