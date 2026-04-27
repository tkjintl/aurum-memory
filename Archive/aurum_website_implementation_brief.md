# Aurum Website — Implementation Brief
**For: Website Agent**
**Prepared: 2026-04-14**
**Companion file: `aurum_agp_flow_prototype.html` (visual source of truth — open in browser alongside this doc)**

---

## 0. Overview

This brief contains two work streams, to be executed in order:

- **Part A — Visual Foundation** (site-wide refresh). Logo swap, atmospheric treatment, hover primitives (magnetic-card + lift-card classes). Touches existing pages. Must be done first because Part B builds on these primitives.

- **Part B — AGP Onboarding Flow**. Four new screens: Shop Selector, AGP Click-Through, AGP Signup Form, Confirmation Email. Uses the Part A primitives by class name.

Part C at the end captures shared deliverables, acceptance criteria, timeline, and open questions.

**The HTML prototype is the visual source of truth.** Any pixel question, any motion question, any spacing question — open it, inspect it, match it.

---

## 1. Shared Context (read once; applies to both work streams)

### 1.1 Brand tokens (already correct in the codebase — do not redefine)

```css
--bg:         #0a0a0a;  /* primary black */
--bg-2:       #111008;  /* gradient companion */
--bg-3:       #1a1814;  /* elevated panels */
--gold:       #C5A572;  /* primary accent */
--gold-bright:#E3C187;  /* hover */
--gold-dim:   #8a7d6b;  /* muted */
--text:       #f5f0e8;  /* primary */
--text-dim:   #a8a096;  /* secondary */
--line:       rgba(197, 165, 114, 0.2);
--line-strong:rgba(197, 165, 114, 0.5);

Fonts:
  Display Latin: Cormorant Garamond (400–700, italic available)
  Display Korean: Noto Serif KR (500–700)
  Body: Outfit (300–700) paired with Noto Sans KR
  Mono / data: JetBrains Mono (400, 500, 700)
```

### 1.2 Typographic rules
- Korean-primary, English italic secondary throughout.
- Korean display headlines use Noto Serif KR weight 500–600 (not 700 — too heavy).
- English subtitles are always italic Cormorant Garamond.
- All numbers, codes, enrollment IDs, account numbers use JetBrains Mono with `letter-spacing: 0.06em`.

### 1.3 What NOT to change across either work stream
- Brand color tokens
- Font stack
- Korean copy already written in the prototype (use verbatim)
- Existing page content outside the scope of this brief (Homepage, Storage, AGP marketing landing — their content stays)
- Nav menu labels, footer content, legal pages
- Backend, FPA model, agent configurations

If you find yourself wanting to restructure something, stop — file it as a follow-up for the founder, don't bundle it into this brief.

---

# PART A — VISUAL FOUNDATION

**Execute this before Part B. Estimated time: 1–2 days.**

## A.1 What this delivers

Five site-wide visual upgrades that extract the polish from the AGP prototype into reusable primitives:

1. **Logo swap** — replace gold-circle "Au" with square-bordered "AU" monogram
2. **Grain texture** — fixed SVG noise overlay for atmospheric depth
3. **Magnetic hover** — cursor-tracking radial gold light + top-edge sweep (for feature cards only)
4. **Lift hover** — subtle lift + border brightens (for standard cards)
5. **Unified hover primitives** as CSS classes that Part B reuses

No color changes. No copy changes. No layout restructuring.

## A.2 Ready-to-paste code

### A.2.1 `src/components/Logo.jsx` (new file)

```jsx
export default function Logo({ size = 40, showWordmark = true, onClick }) {
  return (
    <a
      href="/"
      onClick={onClick}
      style={{
        display: 'inline-flex',
        alignItems: 'center',
        gap: '14px',
        textDecoration: 'none',
      }}
    >
      <span
        className="aurum-logo-mark"
        style={{
          width: size,
          height: size,
          border: '1px solid rgba(197, 165, 114, 0.5)',
          display: 'flex',
          alignItems: 'center',
          justifyContent: 'center',
          fontFamily: "'Cormorant Garamond', serif",
          fontSize: size * 0.42,
          fontWeight: 500,
          color: '#C5A572',
          letterSpacing: '0.04em',
          transition: 'border-color 0.3s ease',
        }}
      >
        AU
      </span>
      {showWordmark && (
        <span
          style={{
            fontFamily: "'Cormorant Garamond', serif",
            fontSize: size * 0.52,
            fontWeight: 500,
            letterSpacing: '0.32em',
            color: '#f5f0e8',
          }}
        >
          AURUM
        </span>
      )}
    </a>
  );
}
```

### A.2.2 `src/styles/aurum-motion.css` (new file)

```css
/* ============================================================
   AURUM SITE-WIDE MOTION & ATMOSPHERE
   Applied globally; reuses existing brand tokens.
   ============================================================ */

/* 1. Grain overlay — atmospheric depth, fixed to viewport */
body::before {
  content: "";
  position: fixed;
  inset: 0;
  background-image: url("data:image/svg+xml;utf8,<svg xmlns='http://www.w3.org/2000/svg' width='200' height='200'><filter id='n'><feTurbulence baseFrequency='0.9' numOctaves='2' stitchTiles='stitch'/><feColorMatrix values='0 0 0 0 0.77 0 0 0 0 0.65 0 0 0 0 0.45 0 0 0 0.05 0'/></filter><rect width='200' height='200' filter='url(%23n)'/></svg>");
  opacity: 0.4;
  pointer-events: none;
  z-index: 1;
  mix-blend-mode: overlay;
}
body > * { position: relative; z-index: 2; }

/* 2. Magnetic card — for primary/featured cards ONLY */
.magnetic-card {
  position: relative;
  overflow: hidden;
  transition: transform 0.45s cubic-bezier(0.2, 0.8, 0.2, 1),
              border-color 0.45s ease;
}
.magnetic-card::before {
  content: "";
  position: absolute;
  inset: 0;
  background: radial-gradient(
    800px circle at var(--mx, 50%) var(--my, 50%),
    rgba(197, 165, 114, 0.08),
    transparent 40%
  );
  opacity: 0;
  transition: opacity 0.4s;
  pointer-events: none;
}
.magnetic-card::after {
  content: "";
  position: absolute;
  top: 0;
  left: 0;
  height: 1px;
  width: 0%;
  background: linear-gradient(90deg, transparent, #C5A572, transparent);
  transition: width 0.7s ease;
}
.magnetic-card:hover {
  transform: translateY(-4px);
  border-color: rgba(197, 165, 114, 0.5);
}
.magnetic-card:hover::before { opacity: 1; }
.magnetic-card:hover::after { width: 100%; }
.magnetic-card > * { position: relative; z-index: 1; }

/* 3. Light lift-card — for standard cards (product grid, articles) */
.lift-card {
  transition: transform 0.3s ease, border-color 0.3s ease;
}
.lift-card:hover {
  transform: translateY(-2px);
  border-color: rgba(197, 165, 114, 0.5);
}

/* 4. Logo hover */
.aurum-logo-mark:hover {
  border-color: #C5A572 !important;
}
```

### A.2.3 `src/lib/magnetic.js` (new file)

```js
// Attach cursor tracking to any .magnetic-card element
export function initMagneticCards() {
  const cards = document.querySelectorAll('.magnetic-card');
  cards.forEach(card => {
    card.addEventListener('mousemove', (e) => {
      const rect = card.getBoundingClientRect();
      card.style.setProperty('--mx', `${e.clientX - rect.left}px`);
      card.style.setProperty('--my', `${e.clientY - rect.top}px`);
    });
  });
}
```

### A.2.4 Wire it up in `src/App.jsx` or `src/main.jsx`

```jsx
import './styles/aurum-motion.css';
import { useEffect } from 'react';
import { initMagneticCards } from './lib/magnetic';

// inside App component
useEffect(() => {
  initMagneticCards();
}, [/* route or page changes */]);
```

### A.2.5 Swap the logo in header

Replace the existing logo component inside `Header.jsx` / `Navbar.jsx` with `<Logo />`. Keep the existing container wrapper and positioning.

## A.3 Tier mapping — where to apply the hover classes

### Tier A — full magnetic treatment (`.magnetic-card` class)
Large, feature-weight elements. Use sparingly so the effect stays "wow," not chaotic.

- Homepage hero CTA card (if card-based)
- Shop selector cards — both "실물 금·은 매매" and "AGP 아름 골드 플랜" (Part B.4.1)
- AGP marketing landing page featured tile / primary CTA card
- "Why Gold" / "Why Silver" feature sections on homepage
- Storage page trust-chip tiles
- Why Aurum benefit grid (only if grid is ≤4 columns)

### Tier B — light lift only (`.lift-card` class)
Standard repeated cards where magnetic would be visually noisy.

- Product listing grid cards (`/shop/physical`)
- Learn article cards (`/learn` hub)
- FAQ accordion cards
- Dashboard widgets (future)

### Tier C — no hover treatment
- Body text blocks
- Footer content
- Value-prop text tiles with no CTA

**Rule of thumb.** If a card already has some hover state, replace it with the appropriate unified class — do not stack multiple hover mechanisms.

## A.4 Logo swap specifics

**Current state:** solid gold-filled circle with "Au" text.
**New state:** bordered square with capitalized "AU" monogram inside, Cormorant Garamond, gold outline, transparent background. Matches the card icons in the AGP prototype.

**Header placement.** Top-left of every page. Pair with "AURUM" wordmark (already present — preserve positioning).

**Recommended sizes.**
- Desktop header: 40×40px box
- Mobile header: 36×36px box
- Footer (if logo repeats): 32×32px box

**Hover.** Border brightens from `rgba(197,165,114,0.5)` to `#C5A572`. No magnetic effect on the logo.

## A.5 Acceptance criteria for Part A

Founder confirms by opening live site:

- [ ] Top-left logo is a square-bordered "AU" monogram + "AURUM" wordmark (not a gold circle)
- [ ] Logo border brightens on hover
- [ ] All pages show subtle grain texture overlay (visible on flat dark backgrounds)
- [ ] Hovering Tier A cards shows: gold radial light tracking cursor + gold line sweeping across top edge + 4px lift
- [ ] Hovering Tier B cards shows: 2px lift + brighter border only (no radial light)
- [ ] No layout shifts, no missing content, no broken spacing
- [ ] Mobile: hover effects gracefully degrade on touch devices
- [ ] Performance: no scroll lag on mid-tier Korean mobile (Galaxy A-series, iPhone SE)

---

# PART B — AGP ONBOARDING FLOW

**Execute after Part A. Estimated time: 2–3 weeks for Phase 1 scaffolding.**

## B.1 What this delivers

Four new screens that convert the `매매` (Shop) nav item from a direct listing into a proper funnel, and add the AGP enrollment flow that doesn't exist yet.

| # | Screen | Route | Purpose |
|---|--------|-------|---------|
| 1 | Shop Selector | `/shop` (replaces current direct listing at this route) | Two-card landing: 실물 금·은 매매 vs AGP 아름 골드 플랜 |
| 2 | AGP Click-Through | `/agp/intro` | 5-step pre-enrollment education carousel |
| 3 | AGP Signup Form | `/agp/enroll` | 6-section progressive form + success state |
| 4 | Confirmation Email | triggered server-side | Email verification + next-steps explainer |

The existing physical-bar shop content is unchanged — it moves to `/shop/physical` and becomes Card 1's destination.

## B.2 End-to-end routing diagram

```
HOMEPAGE
  ├─ [매매] top nav                ──┐
  └─ [지금 배분 시작] hero CTA     ──┤
                                    │
                                    ▼
                        /shop  (new selector page)
                                    │
                    ┌───────────────┴───────────────┐
                    │                               │
                    ▼                               ▼
          실물 금·은 매매                   AGP 아름 골드 플랜
          Card 1                           Card 2 (featured)
                    │                               │
                    ▼                               ▼
          /shop/physical                   /agp/intro
          (existing listing,               (5-step click-through
           bars + coins grid)               using existing content)
                                                    │
                                    ┌───────────────┤
                                    │               │
                                  SKIP →         Next through
                                    │           all 5 steps →
                                    │               │
                                    ▼               ▼
                                    └──▶ /agp/enroll
                                         (6-section signup form)
                                                    │
                                                    ▼
                                         Success screen
                                                    │
                                                    ▼
                                         Confirmation email sent
                                         (24h signed verify link)
                                                    │
                                                    ▼
                                         User clicks "이메일 주소 확인"
                                                    │
                                                    ▼
                                         Account activated +
                                         KYC review (< 24h) +
                                         First auto-debit on start date
```

## B.3 Entry point & CTA audit (IMPORTANT)

Every CTA on the current site that routes to the shop listing or into purchase flow needs to be audited. Most should route to the new `/shop` selector instead of directly to the listing.

| Current CTA text | Current behavior | New behavior |
|------------------|------------------|--------------|
| `매매` (top nav) | → shop listing | → `/shop` selector |
| `지금 배분 시작` (hero CTA) | → shop listing | → `/shop` selector |
| `Start Allocation` | → shop listing | → `/shop` selector |
| `Buy First Bar` or similar | → shop listing | → `/shop` selector |
| Any `<Link to="/shop">` | → listing | → `/shop` selector |
| Footer "매매" link | → shop listing | → `/shop` selector |
| AGP landing CTA `AGP 시작하기` | (may not route currently) | → `/agp/intro` |
| Homepage AGP section CTA | (may not route currently) | → `/agp/intro` |

**Rationale.** The selector is the new front door. A user clicking `지금 배분 시작` from the homepage may actually want the AGP subscription path (probably more common for first-time visitors), not a direct bar purchase. The selector lets both paths coexist.

**Exception — keep direct routes for these:**
- Deep links from marketing emails → product detail pages (`/shop/physical/product/gold-bar-1kg`) — these stay on PDP
- Future AGP dashboard links (`/agp/dashboard`) — don't route through selector

## B.4 The 4 screens — detailed specs

### B.4.1 Shop Selector (`/shop`)

**Layout.**
- Desktop: two cards side-by-side, centered, max-width 1240px
- Mobile (<820px): cards stack vertically, full-width
- Head section above cards: eyebrow "매매 · Shop" → headline "어떻게 시작하시겠습니까" → sub-lead "Choose how you begin." → descriptor paragraph

**Card 1 — 실물 금·은 매매** (apply `.magnetic-card` class + existing border).
- Icon: stacked monogram "AU / AG" (AU on top line, AG on bottom line, centered in 72×72 gold-bordered square — see prototype section 2.1 for exact CSS)
- Korean title: 실물 금·은 매매
- English subtitle (italic): "Physical Gold & Silver — Direct Purchase"
- Body paragraph describing one-time LBMA bar / sovereign coin purchase
- Three feature bullets:
  - `1 oz ~ 1 kg 바 · 1/2 oz 코인` / *Bars from 1 oz to 1kg · Coins from 1/2 oz*
  - `한 번의 결제 · 영구 보관` / *One-time purchase, permanent allocation*
  - `유선·카드·암호화폐 결제 지원` / *Wire, card, and crypto supported*
- CTA link: "제품 둘러보기 · Browse Products →"
- **Action:** routes to `/shop/physical`

**Card 2 — AGP 아름 골드 플랜** (featured, apply `.magnetic-card` + gold-tinted background override).
- "Featured · 추천" tag top-right corner
- Subtle gold-tinted gradient background, slightly brighter border
- Icon: monogram "AGP"
- Korean title: 아름 골드 플랜
- English subtitle: "Aurum Gold Plan — Automated Accumulation"
- Body paragraph
- Three feature bullets:
  - `월 200,000원부터 시작` / *Start from KRW 200,000 / month*
  - `매일·매주·매월 자동 적립` / *Daily, weekly, or monthly auto-debit*
  - `100g 도달 시 실물 바 무료 전환` / *Free conversion to physical at 100g*
- CTA link: "AGP 시작하기 · Begin Enrollment →"
- **Action:** routes to `/agp/intro`

### B.4.2 AGP Click-Through (`/agp/intro`)

**Purpose.** Five-step education carousel that builds confidence before the form. Reuses the existing "AGP는 이렇게 작동합니다" content verbatim.

**Layout.**
- Single card centered, max-width 880px
- Top row: step counter `01 / 05` (left) + SKIP button (right)
- Card body: eyebrow + large emoji icon + Korean title + English italic subtitle + description paragraph
- Bottom row: Previous button + progress dots + Next button

**The 5 steps (content locked — do not rewrite).**

| # | Icon | KR Title | EN Title | Description |
|---|------|----------|----------|-------------|
| 1 | ✍️ | 가입 | Enroll | 10분 내 온라인으로 가입하고 한국 표준 KYC (실명 확인, 휴대폰 인증)를 완료합니다. 모든 절차는 국내 금융기관 방식에 맞춰 설계되었습니다. |
| 2 | 💰 | 입금 | Fund | 토스뱅크·한국 주요 은행에서 일회 또는 월간 자동이체. 신용카드 및 암호화폐 (USDT·USDC) 입금도 지원합니다. 최소 월 20만원부터. |
| 3 | ⚖️ | 그램 적립 | Accumulate | 입금액이 실시간 국제 현물가 + 2.0% 프리미엄으로 AGP 그램으로 전환됩니다. 국내 실물 프리미엄을 피하고 깔끔한 국제 시세에 접근하세요. |
| 4 | 📊 | 관리 | Monitor | 대시보드에서 보유 그램, KRW 가치, 손익, 보관료, 전환 기준 진행률을 실시간으로 확인하세요. 매일 백킹 리포트 공개. |
| 5 | 🥇 | 전환 | Convert | 100g (또는 1kg 기준) 도달 시 LBMA 승인 실물 바로 무료 전환. 또는 언제든 국제 현물가로 매도 후 KRW를 한국 은행 계좌로 수령. |

**Interactions.**
- Previous disabled on step 1
- Next text = "Next →" for steps 1-4, changes to "가입 신청 시작 →" on step 5
- Clicking Next on step 5 → `/agp/enroll`
- SKIP top-right also jumps to `/agp/enroll`
- Progress dots: current = gold solid, completed = dim gold, upcoming = faint line
- Keyboard support: ← → arrows navigate
- Mobile: swipe gestures left/right

### B.4.3 AGP Signup Form (`/agp/enroll`)

**Purpose.** Progressive KYC + plan-setup + payment method + consents, designed to Korean fintech norms.

**Layout.**
- Two-column: sidebar with sticky progress stepper + main form panel
- Mobile (<860px): sidebar collapses to horizontal step-scroller at top
- 6 form sections + success state (section 7); each slides in with fade animation

#### Section 1: 본인 정보 · Identity & Contact

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| 성명 (한글) | text | ✓ | Korean legal name |
| 영문 성명 | text | ✓ | Matches passport / ID |
| 주민등록번호 앞 7자리 | text, 8 chars max | ✓ | Format YYMMDD-N. **Store only first 7 digits.** Use for 실명확인 API only. |
| 국적 | select | ✓ | Default: Republic of Korea |
| 휴대폰 번호 | tel + verify button | ✓ | PASS app verification or SMS OTP fallback |
| 이메일 | email | ✓ | Primary contact for all notices |
| 우편번호 + 주소 | text (with search) | ✓ | Integrate Daum 주소 API (`daum.Postcode`) |
| 상세 주소 | text | ✓ | User-entered detail |
| 신분증 업로드 | file (jpg/png/pdf, 10MB max) | ✓ | Front + back of 주민등록증 OR passport bio page |

**Compliance note (flag to GC Agent).** RRN collection is regulated by PIPA. Recommended: collect only first 7 digits + run 실명확인 via certified vendor (NICE평가정보, KCB, or KB Kookmin Bank API). Store verification token, not raw RRN. Consider PASS-first with RRN fallback.

#### Section 2: 투자자 프로필 · Investor Profile

| Field | Type | Required | Purpose |
|-------|------|----------|---------|
| 직업 | text | ✓ | Occupation |
| 연소득 범위 | select (5 brackets) | ✓ | AML risk classification |
| 자금의 출처 | select (6 options) | ✓ | Source of funds — AML |
| 예상 월 적립액 | select (5 brackets) | ✓ | Account limit setting |
| PEP 여부 | radio (예/아니오) | ✓ | PEP screening |
| 미국 세법 거주자 | radio | ✓ | FATCA declaration |

#### Section 3: AGP 플랜 설계 · Plan Design

| Field | Type | Required | Options |
|-------|------|----------|---------|
| 자산 구성 | radio cards (4) | ✓ | 금 100% / 은 100% / 금 70% 은 30% / 직접 설정 |
| 월 적립액 (KRW) | number | ✓ | Min 200,000 · Max 10,000,000 |
| 적립 주기 | radio (3) | ✓ | 매일 / 매주 / 매월 |
| 시작일 | date | ✓ | Earliest = 3 business days after KYC approval |
| 자동 전환 목표 | select | optional | 100g / 1kg / 수동 결정 |

#### Section 4: 결제 수단 · Payment Method

Three tiles as mutually-exclusive radio cards, each with fee callout.

**Option A — 토스뱅크 자동이체** (recommended, ~0.3% fee). Conditional detail panel:
- 은행 선택 (dropdown of major Korean banks)
- 계좌번호 (text)
- Verify button triggers **1원 송금 인증** via Open Banking API
- Customer enters 4-digit code from deposit description

**Option B — 신용·체크카드** (instant, ~3.5% fee). Conditional detail panel:
- Card number + expiry + CVC (PCI-compliant hosted fields — Toss Payments SDK)
- 카드 소유자명
- Automatic recurring billing enrollment checkbox

**Option C — 암호화폐 USDT·USDC** (~1.0% fee).
- Stablecoin selection (USDT / USDC)
- Network (TRC20 recommended, ERC20, Polygon)
- Customer wallet address (Travel Rule for >1M KRW)
- **UX note:** Crypto is NOT auto-recurring. Customer receives reminder email each cycle with unique deposit address. Flag on selection.

#### Section 5: 약관 동의 · Consent

Six checkboxes + one "agree to all" master toggle.

| # | Consent | Required? |
|---|---------|-----------|
| 1 | 서비스 이용약관 | 필수 |
| 2 | 개인정보 수집·이용 (PIPA) | 필수 |
| 3 | 개인정보 국외이전 (싱가포르) | 필수 |
| 4 | 금융 정보 제공 (오픈뱅킹) | 필수 |
| 5 | 투자 위험 고지 확인 | 필수 |
| 6 | 마케팅 정보 수신 (이메일·SMS) | 선택 |

Each consent has a "View →" link opening full text in modal. GC Agent owns these 6 documents.

**PIPA rule.** Essential consents (#1–5) must be visually separated from optional marketing consent (#6). GC Agent provides exact disclosure language per consent.

#### Section 6: 검토 및 제출 · Review & Submit

Auto-populated summary cards (identity, plan, payment) with inline edit links jumping back to relevant section. Final "신청 완료 · Submit →" button.

#### Section 7: 완료 · Success State

- Gold check icon
- Headline: "신청이 접수되었습니다"
- Echo of submitted email address (gold-bordered box)
- 4-step "next steps" ordered list
- Link to preview the confirmation email

### B.4.4 Confirmation Email

**Trigger.** Server-side on successful form submission. Use Aurum's mail provider (recommend Postmark, Resend, or SendGrid — needs DMARC/DKIM on `aurum.sg`).

**Subject:** `[Aurum] AGP 가입을 거의 완료하셨습니다 — 이메일 확인 필요`

**From:** `Aurum Korea <accounts@aurum.sg>`

**Content structure** (see prototype screen 4 for exact visual):
1. Metadata header (From/To/Subject)
2. Brand block (black bg, gold "AURUM" wordmark)
3. Body:
   - Greeting "홍길동 님, 안녕하세요."
   - Explanation paragraph
   - Gold CTA button: "이메일 주소 확인 → Verify Email" with 24h signed token
   - Enrollment ID card (e.g., `AGP-KR-26041400137`)
   - "What happens next" 4-step box
   - Support contact (support@aurum.sg + KakaoTalk)
   - Signoff
4. Footer (legal address, unsubscribe, privacy policy)

**Palette change for email.** Lighter cream (`#f5f0e8` bg, `#1a1814` text) — better across mail clients, more welcoming tone vs dark site aesthetic.

**Technical notes.**
- Must render in Gmail, Outlook, Apple Mail, Naver Mail, KakaoMail
- Inline CSS only (no `<style>` blocks)
- External fonts fall back to Georgia / Noto Serif KR / system serif
- Verify link = signed JWT, 24h expiry, enrollment ID embedded

## B.5 Korean compliance notes (GC Agent review items)

Website agent builds scaffolding; GC Agent owns/reviews these before production launch:

1. **RRN handling.** PASS-first vs RRN. Vendor contract PIPA-compliant.
2. **PIPA consent language.** Lawyer-approved text for each of the 5 required consents. Must disclose: data recipients (including Aurum Pte. Ltd. Singapore), retention period, right to withdraw.
3. **국외이전 동의** (cross-border data transfer). Required because servers + entity in Singapore. Must name Singapore, list data categories, name recipient entity.
4. **Foreign Exchange Transactions Act (외국환거래법).** KRW → SGD conversion. Confirm Bank of Korea reporting obligations.
5. **Real-Name Financial Transactions Act (금융실명법).** 실명확인 mandatory. Confirm KYC vendor on approved list.
6. **FATCA / CRS.** Reporting workflow for U.S. tax residents.
7. **PSPM status.** Singapore side — re-check PSPM licensing in light of crypto payment option.
8. **투자 위험 고지** (investment risk disclosure). Approved language covering volatility, storage fee erosion, FX risk, no deposit insurance, liquidity timing.
9. **Marketing consent separation.** Legally required visual distinction. Prototype handles it; GC confirms copy.
10. **Complaint / dispute resolution clause.** Required disclosure of complaint channel + FSS process.

## B.6 Payment integration technical notes

| Channel | Vendor | API / SDK | Status |
|---------|--------|-----------|--------|
| Toss Bank auto-debit | Toss Payments (Open Banking) | REST + 간편결제 SDK | PG contract + FSS registration required |
| Credit / debit card | Toss Payments | Hosted checkout iframe (PCI minimized) | Same contract |
| Crypto (USDT/USDC) | Fireblocks or BitGo (recommended) | Reminder-email manual flow | Singapore-side; no Korean license issue |

**1원 송금 flow.**
1. User submits bank + account number
2. Open Banking API → issue 1원 transfer with 4-digit code in memo
3. User sees code in bank app, enters in form
4. API verifies → mark account verified
5. Store bank account token (not raw account number) for future auto-debits

**Recurring billing.**
1. User completes KYC + verification
2. Create 자동이체 mandate (electronic signature captured at enrollment)
3. Schedule executes per user frequency
4. On success → credit AGP grams at live spot + 2.0% premium → receipt email
5. On failure → retry logic + customer notification

## B.7 Routing & app structure changes

### B.7.1 Route map

| Current route | New route | Notes |
|---------------|-----------|-------|
| `shop` → direct listing | `shop` → selector page (new) | Listing content moves to new route |
| — | `shop/physical` → existing listing (bars + coins) | New route holding old listing |
| `agp` (existing landing) | `agp` (unchanged) | Existing AGP marketing landing stays |
| — | `agp/intro` → 5-step click-through (new) | Pre-enrollment funnel |
| — | `agp/enroll` → signup form (new) | KYC + plan + payment |

### B.7.2 Files to create

- `src/pages/ShopSelectorPage.jsx`
- `src/pages/AGPIntroPage.jsx`
- `src/pages/AGPEnrollPage.jsx`
- `src/components/FormStep.jsx` (reusable form section wrapper)
- `src/components/PaymentMethodCard.jsx` (reusable radio card)
- `src/components/ConsentCheckbox.jsx`
- `src/lib/api.js` (API client for KYC, Toss, form submission)
- Server-side: `api/enroll` endpoint + email trigger

### B.7.3 CTA updates — grep for these strings

Update target route to `/shop`:
- `지금 배분 시작`
- `Start Allocation`
- `Buy First Bar`
- `매매` (nav item)
- Any `<Link to="/shop">` or `router.push('/shop')` currently rendering the listing

## B.8 Acceptance criteria for Part B

- [ ] All homepage CTAs (매매 nav, 지금 배분 시작 hero, etc.) land on `/shop` selector
- [ ] `/shop/physical` renders the pre-existing product grid (unchanged content)
- [ ] Selector Card 1 routes to `/shop/physical`; Card 2 routes to `/agp/intro`
- [ ] `/agp/intro` 5-step carousel renders all 5 steps verbatim per B.4.2 table
- [ ] SKIP button from any step jumps to `/agp/enroll`; Next button on step 5 does the same
- [ ] `/agp/enroll` 6-section form with sidebar progress, mobile horizontal stepper
- [ ] Each form section's required fields match B.4.3 tables
- [ ] PASS button + 1원 송금 button are present (can return mock success in Phase 1)
- [ ] Consent section visually separates required from optional per B.4.3 Section 5
- [ ] Review section auto-populates from prior sections with edit-jump links
- [ ] Submit triggers server-side email send + success state render
- [ ] Confirmation email renders in Gmail, Naver Mail, KakaoMail (test all three)
- [ ] All screens match prototype visuals at pixel level
- [ ] All screens use `.magnetic-card` / `.lift-card` primitives from Part A (no inline re-implementation of hover logic)

---

# PART C — SHARED DELIVERABLES & HAND-OFF

## C.1 Marketing Agent follow-ups (NOT website agent's job)

The Website Agent delivers copy blocks as they appear in the prototype. Marketing Agent then polishes and provides these still-missing pieces:

- Full T&C text (6 consent documents — with GC Agent)
- Email subject-line variants for A/B testing
- SMS confirmation template (optional, Phase 2)
- Thank-you / welcome email #2 (post-KYC-approval + first deposit)
- Error-state messaging (card declined, 1원 송금 failed, etc.)
- Help / FAQ link blocks for each form section

## C.2 Admin fee layer (Phase 3 — noted for later)

Founder flagged: "Small admin fee % to be added later."

Placeholder spots in the model:
- Section 3 (AGP Plan Design) → disclose `관리 수수료 · Admin fee: X.XX% per year on AUC`
- Review section → include in summary row
- Confirmation email → disclose in "What happens next" block

When CFO finalizes %, add one line to Section 3 below contribution input:
```
관리 수수료 · Admin fee: 0.25% per year on total AUC
(deducted monthly from your gold grams)
```

## C.3 Accessibility & mobile notes

- All form inputs: associated labels + `autocomplete` hints (`name`, `email`, `tel`, `postal-code`, `street-address`)
- Korean mobile keyboards: `inputmode="numeric"` on RRN and account fields
- Minimum tap target: 44×44px on mobile
- Form validation: inline (next to field, not top)
- Color contrast: WCAG AA on gold/black combinations (verified)
- Form state preserved via sessionStorage across section changes and browser back/refresh

## C.4 Implementation timeline

**Week 1–2: Phase 1 scaffolding (no real KYC/payment integration)**
- Part A in full (visual foundation)
- Move `/shop` listing content to `/shop/physical`
- Build selector, click-through, form UI with stubs, success screen
- Email template sent via SES/Resend
- Mock PASS and 1원 송금 verify (return success)

**Week 3–5: Phase 2 real integrations**
- KYC vendor integration (real 실명확인)
- Toss Payments PG contract + test mode
- Open Banking 1원 송금 live
- Recurring auto-debit mandate creation
- Email verification link flow with signed tokens

**Week 6+: Phase 3 enhancements**
- Crypto payment channel (USDT/USDC)
- Admin fee disclosure layer
- Customer dashboard (grams, KRW value, conversion progress)
- Daily backing report page (`/backing`)

## C.5 Open questions needing founder decision

1. **RRN vs PASS-only.** Recommend PASS-first with RRN fallback.
2. **Admin fee %.** CFO to finalize. Recommendation: 0.20–0.30% per year on AUC.
3. **Crypto as auto-recurring.** Possible but Travel Rule complex. Launch with manual-with-reminder, upgrade in Phase 2?
4. **Bank list at launch.** Toss + Kakao + KB confirmed; which other 4–5 banks?
5. **KYC vendor.** NICE평가정보 / KCB / KB Kookmin 실명확인 API.
6. **Email provider.** Postmark / Resend / SendGrid / AWS SES.

## C.6 Files to deliver (final checklist)

### From Part A
- [ ] `src/components/Logo.jsx`
- [ ] `src/styles/aurum-motion.css`
- [ ] `src/lib/magnetic.js`
- [ ] Edits to `src/App.jsx` or `src/main.jsx` (imports + useEffect)
- [ ] Edits to header/nav component (swap logo)
- [ ] Edits to every existing card component (add `magnetic-card` or `lift-card` per A.3 tier mapping)
- [ ] Before/after screenshots of homepage

### From Part B
- [ ] `src/pages/ShopSelectorPage.jsx`
- [ ] `src/pages/AGPIntroPage.jsx`
- [ ] `src/pages/AGPEnrollPage.jsx`
- [ ] `src/components/FormStep.jsx`
- [ ] `src/components/PaymentMethodCard.jsx`
- [ ] `src/components/ConsentCheckbox.jsx`
- [ ] `src/lib/api.js`
- [ ] `api/enroll` server endpoint
- [ ] Email template (HTML, inline CSS)
- [ ] Routing changes (move listing, add 3 new routes, update CTAs per B.3 audit)
- [ ] Before/after routing diagram screenshot

## C.7 Memory archive one-liner

> Aurum website implementation brief executed 2026-04-14. Part A: site-wide visual refresh — square "AU" monogram replaces gold-circle logo, `.magnetic-card` + `.lift-card` hover primitives added via `aurum-motion.css`, SVG grain overlay applied to body. Part B: AGP onboarding flow — homepage CTAs (매매 nav + 지금 배분 시작 hero) → `/shop` selector (physical vs AGP) → `/agp/intro` 5-step click-through → `/agp/enroll` 6-section KYC+plan+payment form → confirmation email. Existing `/shop` listing moved to `/shop/physical`. Brand colors/fonts unchanged. Companion file: `aurum_agp_flow_prototype.html`. Website Agent owns scaffolding, Marketing Agent owns copy polish, GC Agent owns compliance items (B.5), CFO Agent owns admin fee addition (C.2).
