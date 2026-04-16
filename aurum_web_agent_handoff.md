# AURUM KOREA — WEB AGENT SESSION BRIEF
**Date:** 2026-04-16  
**Type:** Full site audit, enhancement, and feature implementation  
**Repo:** `github.com/tkjintl/aurum-website` (single repo — all files live here)  
**Prepared by:** External Claude session (no MCP access)

---

## PART 1 — PROJECT CONTEXT (READ THIS FIRST)

### What Aurum Korea Is
Aurum Korea is a Singapore-incorporated physical gold and silver bullion platform for Korean investors. Customers buy LBMA-approved bars at international spot price plus a transparent premium (8% gold, 15% silver). Metal is stored at Malca-Amit Singapore FTZ. Core differentiator vs Korean retail (KRX, bank gold accounts): allocated storage, international spot pricing, no VAT, no counterparty risk.

### What the Website Is
A React/Vite single-page application (NOT Next.js despite the system prompt — verify this by checking `package.json`). Architecture:
- `src/App.jsx` — routing, state, shared props
- `src/BaseUI.jsx` — Ticker, Nav, LoginModal, Toast (all UI chrome in ONE file — same as aurum-test original)
- `src/lib/index.jsx` OR `src/lib.jsx` — data, hooks, helpers (check which path exists)
- `src/pages/HomePage.jsx` — main landing
- `src/pages/ShopPages.jsx` — product listing, cart
- `src/pages/InfoPages.jsx` — Why Gold, Storage, Learn
- `src/pages/UserPages.jsx` — Dashboard, KYC, Orders
- `src/pages/AGPPages.jsx` — AGP enroll flow
- `src/campaigns/FoundersClubPage.jsx` — Founders Club campaign
- `src/campaigns/AGPLaunchPage.jsx` — AGP launch campaign
- `api/prices.js` — Vercel serverless: fetches live gold/silver prices + KRW rate
- `src/index.css` — global styles, ticker animation

### How the Site Got to Its Current State
The site was rebuilt from scratch over ~15 sessions from an older single-file architecture (`aurum-test`). The rebuild split everything into a multi-file structure. During this process:
1. **Many alignment issues accumulated** — sections used inconsistent horizontal padding (48px vs 60px vs 80px) causing content to misalign with the nav logo
2. **A `.aurum-container` CSS class was added** to fix alignment — both the Nav and all page sections should use `className="aurum-container"` for their inner content wrapper
3. **Premium constants** were defined in multiple places — they must live ONLY in `src/BaseUI.jsx`
4. **The ticker** was moved to `BaseUI.jsx` (matching the original architecture) and uses a CSS marquee animation (`.ticker-track` class)
5. **English version** was never properly implemented — the `lang` prop threads through all components but many English strings are empty or wrong
6. **The Founders Club savings calculator** still shows USD instead of KRW

### Critical Files to Check Before Anything Else
```bash
# Verify these exist and note their actual paths:
find src -name "*.jsx" | head -30
cat src/BaseUI.jsx | grep -n "KR_GOLD_PREMIUM\|KR_SILVER_PREMIUM\|AURUM_GOLD\|AURUM_SILVER"
cat src/index.css | grep -n "ticker\|aurum-container\|zoom"
cat api/prices.js | grep -n "metals.live\|yahoo\|source"
```

---

## PART 2 — THINGS TO CRITICALLY REVIEW ON FIRST PASS

Before making any changes, do a full read-through and catalogue these issues:

### 2a. Alignment (HIGHEST PRIORITY — was asked 4+ times)
- Every page section should wrap its content in `<div className="aurum-container">` 
- The Nav's inner content row must also use `className="aurum-container"` 
- `.aurum-container` is defined in `index.css` as: `max-width: 1340px; margin: 0 auto; padding: 0 60px;`
- On mobile: `padding: 0 16px`
- **Check:** Does the logo left edge align with the page content left edge on desktop? If not, find which sections are missing `aurum-container` and add it.

### 2b. Pricing Constants Location
These constants must exist ONLY in `src/BaseUI.jsx` and be imported from there:
```js
KR_GOLD_PREMIUM     = 0.20   // Korean retail (kimchi premium + VAT)
KR_SILVER_PREMIUM   = 0.30   // Korean retail silver
AURUM_GOLD_PREMIUM  = 0.08   // Aurum gold markup
AURUM_SILVER_PREMIUM = 0.15  // Aurum silver markup
```
Grep for these across all files. Remove any duplicates. Ensure imports reference `BaseUI.jsx`.

### 2c. Ticker Scroll Direction + Label
- Ticker should scroll **left-to-right** (original Korean news chyron style)
- In `index.css`, `@keyframes ticker-scroll` should go from `translateX(-50%)` to `translateX(0)` — NOT 0 to -50%
- Item label: `한국금거래소 1돈 매도가` must be changed to `한국금바실가 1돈`

### 2d. Founders Club Gate Calculator — USD → KRW
Section with text: `실물과 AGP, 모두 더 싸집니다 / 게이트별 절약액을 직접 확인하세요`
- All monetary values showing `$` must show `₩`
- Gate thresholds ($5K/$15K etc.) → display in KRW using live `krwRate`
- Savings amounts → `fKRW()` helper, not `fUSD()`
- Gate labels (시작의 문, 셋의 표식, 정점, 볼트 순례) — **do NOT rename**

### 2e. Text/Label Changes Required
| Current | Change to | Where |
|---------|-----------|-------|
| `파운더스 클럽` | `Founders Club` | ALL locations, both KO and EN |
| `클럽 가입` (nav CTA) | `AGP 적금 가입` (KO) | Nav top-right button |
| `Join Club` (EN nav CTA) | `Start AGP` | Nav top-right button |
| `AGP` (nav link KO) | `AGP 적금` | Nav links array |
| Nav CTA click target | Must go to `agp-enroll` | Check App.jsx routing |

### 2f. English Version Completeness
The `lang` prop (`'ko'` or `'en'`) threads through every component. Toggle EN in the nav and walk through the entire site. Document every Korean string that appears when lang=EN. Common gaps:
- Inline ternaries: `lang === 'ko' ? '...' : ''` — the empty string means nothing shows
- Hero subtext, stats bar labels, trust strip, accordion items, page titles
- Use premium financial English: "allocated storage" not "distributed storage", "spot price" not "real price", "vault" not "safe"

### 2g. Button Consistency (Image 6 reference standard)
Every CTA bar/button must conform to one of two types:
- **Primary:** `background: linear-gradient(135deg,#c5a572,#8a6914)`, white text, `padding: 14px 28px`, `font-size: 15px`, `font-weight: 700`, `border-radius: 6px`
- **Secondary:** `background: transparent`, `border: 1px solid rgba(197,165,114,0.4)`, gold text, same padding/size
- Equal-width CTAs in pairs should use `flex: 1` so both buttons are the same width
- Check: Founders Club hero buttons, HomePage shop cards, AGP Launch page, Why Gold page CTAs

---

## PART 3 — IMPLEMENTATION TASKS (in order)

### TASK 1 — Ticker fixes
**File:** `src/BaseUI.jsx`, `src/index.css`

1. In `index.css`, change ticker scroll direction:
```css
/* FROM */
@keyframes ticker-scroll {
  0%   { transform: translateX(0); }
  100% { transform: translateX(-50%); }
}
/* TO — left-to-right */
@keyframes ticker-scroll {
  0%   { transform: translateX(-50%); }
  100% { transform: translateX(0); }
}
```

2. In `BaseUI.jsx` Ticker items array, find label `한국금거래소 1돈 매도가` (or `한국금실물가`) and replace with `한국금바실가 1돈`

3. Verify price formula: `Math.round(prices.gold * krwRate * (1 + KR_GOLD_PREMIUM) / 31.1035 * 3.75)`

---

### TASK 2 — Label/text changes
**Files:** `src/BaseUI.jsx`, `src/pages/InfoPages.jsx`, `src/campaigns/FoundersClubPage.jsx`, `src/App.jsx`

```bash
# Find all instances first:
grep -rn "파운더스 클럽\|클럽 가입\|Join Club\|\"agp\"" src/
```

Execute:
- `파운더스 클럽` → `Founders Club` everywhere
- Nav CTA button: `AGP 적금 가입` / `Start AGP`, route → `agp-enroll`
- Nav `AGP` link → `AGP 적금` (KO only, EN stays `AGP Savings`)

---

### TASK 3 — Founders Club savings calculator → KRW
**File:** `src/campaigns/FoundersClubPage.jsx` (or wherever the gate calculator lives)

```bash
grep -n "fUSD\|\$\|USD\|savings\|절약" src/campaigns/FoundersClubPage.jsx | head -30
```

Replace all `fUSD(...)` in this section with `fKRW(... * krwRate)`. Ensure `krwRate` is passed as prop from App.jsx.

---

### TASK 4 — English version: full bilingual pass
**All files**

Walk `lang === 'ko' ? '...' : '...'` patterns across all pages. Fill every empty or missing English string with accurate, premium-tone financial English. Specific terminology:
- `배분 보관` → `Allocated Storage`
- `현물가` → `Spot Price`  
- `실물 금` → `Physical Gold`
- `국제 현물가` → `International Spot Price`
- `한국실금가` → `Korea Retail Price`
- `절약 금액` → `Your Savings`
- `볼트` → `Vault`
- `AGP 적금` → `AGP Savings Plan`

---

### TASK 5 — Design conformity audit

**Typography standard:**

| Element | Size | Font | Weight |
|---------|------|------|--------|
| Eyebrow labels | 10px, letter-spacing 4px | Outfit, uppercase | 500 |
| Section titles | clamp(28px, 4vw, 46px) | Cormorant Garamond | 300 |
| Card titles | clamp(18px, 2.5vw, 26px) | Cormorant Garamond | 500 |
| Body copy | 14-15px | Outfit | 400, line-height 1.75 |
| Price/numbers | 16-24px | JetBrains Mono | 600-700 |
| Nav links | 13px | Outfit | 400/600 active |
| CTA buttons | 14-15px | Outfit | 700 |
| Badge text | 10-11px | Outfit, letter-spacing 1.5px | — |

**Hover interactions to add (where missing):**

| Element | Hover effect |
|---------|-------------|
| Product cards | `translateY(-4px)` + brighten border (`.magnetic-card` class) |
| Stats bar items | `rgba(197,165,114,0.04)` background wash |
| Comparison table rows | `rgba(197,165,114,0.03)` row highlight |
| Trust strip icons | `scale(1.1)` + `filter: drop-shadow(0 0 8px rgba(197,165,114,0.4))` |
| Gate cards (Founders) | `translateY(-3px)` + border brighten |
| Accordion items (open) | Left accent `border-left: 2px solid #c5a572` |
| Nav links | `border-bottom: 1px solid #c5a572` slide-in (use `transition`) |

Use `onMouseEnter`/`onMouseLeave` inline or extend existing CSS classes in `index.css`. Do NOT add external animation libraries.

---

### TASK 6 — Alignment final pass (confirm `.aurum-container` applied everywhere)

```bash
# Find sections that use padding 0 80px or 0 60px directly on outer divs instead of aurum-container:
grep -n "padding.*60px\|padding.*80px\|0 80\|0 60" src/pages/*.jsx src/campaigns/*.jsx
```

For every page section:
- Outer wrapper: full-width background, no horizontal padding
- Inner content: `<div className="aurum-container" style={{ paddingTop: ..., paddingBottom: ... }}>`
- Never use `maxWidth` + `margin: 0 auto` inside an already-padded container (this was the bug)

---

### TASK 7 — Premium constants final verification

```bash
grep -rn "0\.184\|0\.02\b\|0\.06\b\|KR_GOLD\|AURUM_GOLD\|KR_SILVER\|AURUM_SILVER" src/
```

All four constants must be:
1. Defined ONCE in `src/BaseUI.jsx`
2. Exported: `export const KR_GOLD_PREMIUM = 0.20;` etc.
3. Imported everywhere else: `import { KR_GOLD_PREMIUM, ... } from '../BaseUI.jsx';`
4. No hardcoded decimal substitutes anywhere

---

## PART 4 — DELIVERY REQUIREMENTS

Follow your system prompt's DELIVERY BLOCK FORMAT exactly:
- `npm run build` must pass before any file is delivered
- Every changed file gets: tmpfiles link + permanent archive link + repo path
- Update `last_session.md` in the memory repo after delivery

**Files expected to change:**
1. `src/BaseUI.jsx`
2. `src/pages/HomePage.jsx`
3. `src/pages/InfoPages.jsx`
4. `src/campaigns/FoundersClubPage.jsx`
5. `src/campaigns/AGPLaunchPage.jsx`
6. `src/index.css`
7. `src/App.jsx` (if routing changes needed)
8. `src/lib/index.jsx` or `src/lib.jsx` (constants cleanup)

---

## PART 5 — OPEN ITEMS TO LOG

After your session, log these as open items if not resolved:
- [ ] Does `agp-enroll` route exist in `App.jsx`? If not, flag it
- [ ] Are there any Korean strings in components not covered by `lang` prop (hardcoded Korean)?
- [ ] Are there pages/components with no English translations at all?
- [ ] Does `api/prices.js` return `source: 'metals.live'` or `source: 'fallback'`? Log which
- [ ] Font size conformity: which pages had the most deviations?

---

*End of brief. All files are in `github.com/tkjintl/aurum-website`. No separate memory repo.*
