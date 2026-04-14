# AGP Onboarding Flow — Handoff Brief
**For: Website Agent + Marketing Agent + GC Agent review**
**Prepared: 2026-04-14 (rev 2 — adds routing diagram + CTA audit)**
**Prototype file: `aurum_agp_flow_prototype.html` (single-file HTML, open in any browser)**

---

## 1. What This Delivers

Four new screens that convert the current `매매` (Shop) nav item from a direct listing into a proper funnel, and add the AGP enrollment flow that doesn't exist yet.

| # | Screen | Route | Purpose |
|---|--------|-------|---------|
| 1 | **Shop Selector** | `/shop` (replaces current direct listing at this route) | Two-button landing: 실물 금·은 매매 (one-time purchase) vs AGP 아름 골드 플랜 (recurring savings) |
| 2 | **AGP Click-Through** | `/agp/intro` | 5-step pre-enrollment education carousel using the existing "AGP는 이렇게 작동합니다" content |
| 3 | **AGP Signup Form** | `/agp/enroll` | 6-section progressive form (Identity → Profile → Plan → Payment → Consent → Review) + success state |
| 4 | **Confirmation Email** | triggered server-side | Email verification + next-steps explainer |

The existing physical-bar shop page content is unchanged — it simply moves to a new route (`/shop/physical`) and becomes Card 1's destination.

---

## 2. End-to-End Routing Diagram

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

---

## 3. Entry Point & CTA Audit (IMPORTANT — for Website Agent)

Every CTA on the current site that routes to the shop listing or into purchase flow needs to be audited. Most should now route to the new `/shop` selector instead of directly to the listing.

**Audit checklist — Website Agent should grep the codebase for each:**

| Current CTA text | Current behavior (likely) | New behavior |
|------------------|---------------------------|--------------|
| `매매` (top nav) | → shop listing | → `/shop` selector |
| `지금 배분 시작` (hero CTA) | → shop listing | → `/shop` selector |
| `Start Allocation` | → shop listing | → `/shop` selector |
| `Buy First Bar` or similar | → shop listing | → `/shop` selector |
| Any `/shop` link anywhere | → listing | → `/shop` selector |
| Footer "매매" link | → shop listing | → `/shop` selector |
| AGP page CTA `AGP 시작하기` | (currently may not route) | → `/agp/intro` |
| Homepage AGP section CTA | (currently may not route) | → `/agp/intro` |

**Rationale.** The selector is the new front door. A user clicking `지금 배분 시작` from the homepage may actually want the AGP subscription path (probably more common for first-time visitors), not a direct bar purchase. The selector lets both paths coexist without forcing a choice from the homepage copy. Users who already know they want a specific bar can click through in two taps.

**Exception — keep direct-route behavior for these.**
- Deep links from marketing emails that land directly on a product detail page (e.g., `/shop/physical/product/gold-bar-1kg`) — these still go straight to the PDP.
- AGP enrollment completion emails that send users to `/agp/dashboard` (future) — don't route through selector.

---

## 4. The 4 Screens — Detailed Specifications

### 4.1 Shop Selector (`/shop`)

**Trigger.** Any homepage CTA listed in the audit above.

**Layout.**
- Desktop: two cards side-by-side, centered, max-width 1240px.
- Mobile (<820px): cards stack vertically, full-width.
- Head section above cards: eyebrow "매매 · Shop" → headline "어떻게 시작하시겠습니까" → sub-lead "Choose how you begin." → one-line descriptor paragraph.

**Card 1 — 실물 금·은 매매.**
- Icon: stacked monogram "AU / AG" (AU on top line, AG on bottom line, both centered in 72×72 gold-bordered square)
- Korean title: 실물 금·은 매매
- English subtitle (italic): "Physical Gold & Silver — Direct Purchase"
- Body paragraph describing one-time LBMA bar / sovereign coin purchase
- Three feature bullets with KR primary + EN italic note:
  - `1 oz ~ 1 kg 바 · 1/2 oz 코인` / *Bars from 1 oz to 1kg · Coins from 1/2 oz*
  - `한 번의 결제 · 영구 보관` / *One-time purchase, permanent allocation*
  - `유선·카드·암호화폐 결제 지원` / *Wire, card, and crypto supported*
- CTA link at bottom: "제품 둘러보기 · Browse Products →"
- **Action:** routes to `/shop/physical` (existing listing, renamed route).

**Card 2 — AGP 아름 골드 플랜** (featured/recommended).
- "Featured · 추천" tag top-right corner
- Subtle gold-tinted gradient background, slightly brighter border
- Icon: monogram "AGP"
- Korean title: 아름 골드 플랜
- English subtitle: "Aurum Gold Plan — Automated Accumulation"
- Body paragraph describing monthly automatic accumulation starting at ₩200K
- Three feature bullets:
  - `월 200,000원부터 시작` / *Start from KRW 200,000 / month*
  - `매일·매주·매월 자동 적립` / *Daily, weekly, or monthly auto-debit*
  - `100g 도달 시 실물 바 무료 전환` / *Free conversion to physical at 100g*
- CTA link: "AGP 시작하기 · Begin Enrollment →"
- **Action:** routes to `/agp/intro`.

**Interaction polish.**
- Hover: card lifts 4px, border brightens, radial gold light tracks cursor position (see `::before` with `--mx`, `--my` CSS vars), animated gold line sweeps across top edge.
- Mobile: tap = immediate navigation, no hover state needed.

---

### 4.2 AGP Click-Through (`/agp/intro`)

**Purpose.** Five-step education carousel that builds confidence before the form. Reuses the founder's existing "AGP는 이렇게 작동합니다" content verbatim.

**Layout.**
- Single card centered, max-width 880px
- Top row: step counter `01 / 05` (left) + SKIP button (right, lets power users jump to form)
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
- Previous disabled on step 1.
- Next button text = "Next →" for steps 1-4, changes to "가입 신청 시작 →" on step 5.
- Clicking Next on step 5 → `/agp/enroll`.
- SKIP top-right also jumps to `/agp/enroll`.
- Progress dots: current step = gold solid, completed steps = dim gold, upcoming = faint line.
- Keyboard support: ← → arrows navigate (add this in production).
- Mobile: swipe gestures left/right (recommended addition).

---

### 4.3 AGP Signup Form (`/agp/enroll`)

**Purpose.** Progressive KYC + plan-setup + payment method + consents, designed to Korean fintech norms.

**Layout.**
- Two-column: sidebar with progress stepper (sticky) + main form panel.
- Mobile (<860px): sidebar collapses to horizontal step-scroller at top.
- 6 sections total; each slides in with fade animation. Section 7 is the success state.

**Section breakdown.**

#### Section 1: 본인 정보 · Identity & Contact
| Field | Type | Required | Notes |
|-------|------|----------|-------|
| 성명 (한글) | text | ✓ | Korean legal name |
| 영문 성명 | text | ✓ | Matches passport / ID |
| 주민등록번호 앞 7자리 | text, 8 chars max | ✓ | Format YYMMDD-N. **Only first 7 stored** — do not store the full RRN. Use for 실명확인 API only. |
| 국적 | select | ✓ | Default: Republic of Korea |
| 휴대폰 번호 | tel + verify button | ✓ | Triggers PASS app verification or SMS OTP fallback |
| 이메일 | email | ✓ | Primary contact for all notices |
| 우편번호 + 주소 | text (with search) | ✓ | Integrate Daum 주소 API (`daum.Postcode`) |
| 상세 주소 | text | ✓ | User-entered detail |
| 신분증 업로드 | file (jpg/png/pdf, 10MB max) | ✓ | Front + back of 주민등록증 OR passport bio page |

**Compliance notes for GC agent.** RRN collection is regulated by 개인정보보호법 (PIPA). Best practice: collect only first 7 digits + run 실명확인 via a certified KYC vendor (e.g., NICE평가정보, KCB, or KB Kookmin Bank 실명확인 API). Store only the verification token, not the raw RRN. Consider using 간편인증 via PASS app as a preferred alternative — it satisfies real-name verification without RRN storage.

#### Section 2: 투자자 프로필 · Investor Profile
| Field | Type | Required | Purpose |
|-------|------|----------|---------|
| 직업 | text | ✓ | Occupation |
| 연소득 범위 | select (5 brackets) | ✓ | AML risk classification |
| 자금의 출처 | select (6 options) | ✓ | Source of funds — AML |
| 예상 월 적립액 | select (5 brackets) | ✓ | Account limit setting |
| PEP 여부 | radio (예/아니오) | ✓ | Politically Exposed Person screening |
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
Three tiles presented as mutually-exclusive radio cards, each with a fee callout.

**Option A — 토스뱅크 자동이체 (recommended, ~0.3% fee).** Conditional detail panel:
- 은행 선택 (dropdown of major Korean banks)
- 계좌번호 (text)
- Verify button triggers **1원 송금 인증** via Open Banking API
- Customer enters 4-digit code from deposit description

**Option B — 신용·체크카드 (instant, ~3.5% fee).** Conditional detail panel:
- Card number + expiry + CVC (PCI-compliant hosted fields — use Toss Payments SDK)
- 카드 소유자명
- Automatic recurring billing enrollment checkbox

**Option C — 암호화폐 USDT·USDC (~1.0% fee).**
- Stablecoin selection (USDT / USDC)
- Network (TRC20 recommended for low fee, ERC20, Polygon)
- Customer wallet address (Travel Rule compliance for >1M KRW equivalent)
- **Important UX note:** Crypto is NOT auto-recurring. Customer receives a reminder email each cycle with a unique deposit address. Flag this clearly on selection.

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

**Each consent must have a "View →" link** that opens the full text in a modal or separate route. GC agent owns the copy of these six documents.

**Required PIPA consent separation.** Korean law requires that "essential-for-service" consents (#1–5) be visually separated from "optional marketing" consent (#6), and that the user be told specifically which party receives which data. The GC agent needs to provide the exact PIPA disclosure language per consent.

#### Section 6: 검토 및 제출 · Review & Submit
Auto-populated summary cards (identity, plan, payment) with inline edit links that jump back to the relevant section. Final "신청 완료 · Submit →" button.

#### Section 7: 완료 · Success State
After submit, show success card with:
- Gold check icon
- Headline: "신청이 접수되었습니다"
- Echo of submitted email address (gold-bordered box)
- 4-step "next steps" ordered list
- Link to preview the confirmation email

---

### 4.4 Confirmation Email

**Trigger.** Server-side on successful form submission. Use Aurum's mail provider (recommend Postmark, Resend, or SendGrid — needs DMARC/DKIM configured on `aurum.sg` domain).

**Subject line:** `[Aurum] AGP 가입을 거의 완료하셨습니다 — 이메일 확인 필요`

**From:** `Aurum Korea <accounts@aurum.sg>`

**Content structure.** (see prototype for the exact visual)
1. Metadata header (From/To/Subject — preview only)
2. Brand block (black background, gold "AURUM" wordmark)
3. Body:
   - Greeting "홍길동 님, 안녕하세요."
   - Explanation paragraph
   - Gold CTA button: "이메일 주소 확인 → Verify Email" with 24-hour signed token
   - Enrollment ID card (e.g., `AGP-KR-26041400137`)
   - "What happens next" 4-step box (KYC, account activation, first deposit, gram accumulation)
   - Support contact (support@aurum.sg + KakaoTalk channel)
   - Signoff
4. Footer (legal address, unsubscribe, privacy policy links)

**Palette change for email.** The email uses a lighter cream palette (`#f5f0e8` bg, `#1a1814` text) instead of the dark site theme — easier to read in most mail clients, and signals "welcome/approachable" vs the dark HNW site aesthetic.

**Technical notes.**
- Must render in Gmail, Outlook, Apple Mail, Naver Mail, KakaoMail (Korean providers).
- Inline CSS only (no `<style>` blocks) for max client compatibility.
- All external fonts fall back to Georgia / Noto Serif KR / system serif since most email clients block Google Fonts.
- Verify link = signed JWT with 24h expiry containing enrollment ID.

---

## 5. Design Tokens (Brand System)

Already established in Aurum's brand, documented here for the agents:

```css
--bg:         #0a0a0a;  /* primary black */
--bg-2:       #111008;  /* gradient companion */
--bg-3:       #1a1814;  /* elevated panels */
--gold:       #C5A572;  /* primary accent */
--gold-bright:#E3C187;  /* hover */
--gold-dim:   #8a7d6b;  /* muted */
--text:       #f5f0e8;  /* primary */
--text-dim:   #a8a096;  /* secondary */
--line:       rgba(197,165,114,0.2);

Fonts:
  Display Latin: Cormorant Garamond (weights 400-700, italic available)
  Display Korean: Noto Serif KR (weights 500-700)
  Body: Outfit (300-700) paired with Noto Sans KR
  Mono / data: JetBrains Mono (400, 500, 700)
```

**Typographic rules.**
- Korean-primary, English italic secondary throughout (matches existing site).
- Korean display headlines use Noto Serif KR weight 500–600 (not 700 — too heavy).
- English subtitles are always italic Cormorant Garamond.
- All numbers, codes, enrollment IDs, account numbers use JetBrains Mono with `letter-spacing: 0.06em`.

---

## 6. Korean Compliance Notes (flag for GC Agent)

The website agent should build the scaffolding; the GC agent must own / review these items before production launch:

1. **RRN handling.** Confirm the chosen implementation (full RRN vs 간편인증 via PASS) and ensure vendor has PIPA-compliant contract in place. Recommend: PASS-first, RRN fallback.
2. **PIPA consent language.** Provide lawyer-approved text for each of the 5 required consents. Must specifically disclose: data recipients (including Aurum Pte. Ltd. Singapore), retention period, and right to withdraw.
3. **국외이전 동의** (cross-border data transfer). Specifically required because Aurum's servers and entity are in Singapore. Must name Singapore, list the data categories transferred, and name the recipient entity.
4. **Foreign Exchange Transactions Act (외국환거래법).** KRW → SGD conversion implications for each customer. Confirm whether Aurum should be filing per-transaction reports with Bank of Korea (likely only applies above thresholds; below ~USD 5K/transaction + annual cap simpler reporting applies).
5. **Real-Name Financial Transactions Act (금융실명법).** 실명확인 is mandatory; confirm chosen KYC vendor is on the approved list.
6. **FATCA / CRS.** Required questions are in the form. Confirm reporting workflow when a customer declares U.S. tax residency.
7. **PSPM (Payment Services / Prepaid / Stored-Value) status.** Singapore side — confirm that "accumulation in metal grams backed by pooled allocated holdings" does NOT trigger Singapore PSPM licensing (per GC memo this was already addressed; re-check in light of the crypto payment option).
8. **투자 위험 고지** (investment risk disclosure). Provide approved language: price volatility, storage fee erosion, FX risk on redemption, no deposit insurance, liquidity timing.
9. **Marketing consent separation.** Optional marketing consent must be visibly distinct from required consents (Korean law). Prototype already does this visually; GC to confirm language.
10. **Complaint / dispute resolution clause.** Korean financial product rules require disclosure of the complaint channel and FSS / Financial Dispute Resolution process. Flag for inclusion.

---

## 7. Payment Integration — Technical Notes for Website Agent

| Channel | Primary Vendor | API / SDK | Status |
|---------|---------------|-----------|--------|
| Toss Bank auto-debit | Toss Payments (Open Banking) | REST API + 간편결제 SDK | Contract required — PG (payment gateway) registration with FSS |
| Credit / debit card | Toss Payments | Hosted checkout iframe (PCI-DSS scope minimized) | Same Toss Payments contract |
| Crypto (USDT/USDC) | Singapore wallet provider (recommend Fireblocks or BitGo for custody) | Reminder-email based manual flow at launch | Singapore-side setup only; no Korean license issue since customer initiates transfer |

**1원 송금 (one-won bank verification) flow.**
1. User submits bank + account number
2. Website → Open Banking API → issue 1원 transfer with 4-digit code in the memo/description field
3. User sees code in their bank app, enters it in the form
4. Website → verify code → mark account verified
5. Website → store bank account token (not raw account number) for future auto-debits

**Recurring billing (for auto-debit).**
1. User completes verification + KYC
2. Website → create recurring 자동이체 mandate (electronic signature captured during enrollment)
3. Schedule executes per user's frequency (매일/매주/매월)
4. On success → credit AGP grams at live spot + 2.0% premium → send receipt email
5. On failure → retry logic + customer notification

---

## 8. Routing & App Structure Changes

The current `App.jsx` already has a simple page-state router. Changes required:

### 8.1 Route map

| Current route | New route | Notes |
|---------------|-----------|-------|
| `shop` → direct listing | `shop` → selector page (new component) | Listing content preserved, moved to new route |
| — | `shop/physical` → existing listing (bars + coins) | New route holding the old listing |
| `agp` (existing landing) | `agp` (unchanged) | The existing AGP marketing landing stays as-is |
| — | `agp/intro` → 5-step click-through (new) | Pre-enrollment funnel |
| — | `agp/enroll` → signup form (new) | KYC + plan + payment |

### 8.2 Files to create (suggested structure, matching existing flat pattern)
- `src/pages/ShopSelectorPage.jsx`
- `src/pages/AGPIntroPage.jsx`
- `src/pages/AGPEnrollPage.jsx`
- `src/components/FormStep.jsx` (reusable form section wrapper)
- `src/components/PaymentMethodCard.jsx` (reusable radio card)
- `src/components/ConsentCheckbox.jsx`
- `src/lib/api.js` — API client for KYC, Toss integration, form submission
- Server-side: `api/enroll` endpoint + email trigger

### 8.3 CTA updates required across existing code
Grep for the following strings and update their target route to `/shop`:
- `지금 배분 시작`
- `Start Allocation`
- `Buy First Bar`
- `매매` (nav item)
- Any `<Link to="/shop">` or `router.push('/shop')` calls that currently render the listing directly

---

## 9. Marketing Agent Deliverables

The Marketing Agent should own the copy layer and make sure the tone stays HNW-sophisticated (aligns with the brand positioning memo already in `files/brand/`).

**Copy blocks already written in the prototype — marketing to review and polish.**
- Shop selector head, two card bodies, bullet lists, CTAs
- AGP intro 5-step descriptions (use the founder's exact wording — do not paraphrase)
- Form section headers and descriptions
- Success screen messaging
- Full email copy (Korean primary, English italic accents)

**Copy blocks still needed from Marketing Agent.**
- Full T&C text (6 consent documents — coordinate with GC Agent)
- Email subject-line variants for A/B testing
- SMS confirmation (optional, if we add it later)
- Thank-you / welcome email #2 (sent after KYC approval + first successful deposit)
- Error-state messaging (card declined, 1원 송금 failed, etc.)
- Help / FAQ link blocks for each form section

---

## 10. Admin Fee Layer (Future — noted for later addition)

Founder flagged: "We also want to add a small admin fee % but that will be later but fyi."

**Placeholder spots in the model already exist:**
- AGP Plan Design screen → ideal place to disclose admin fee (`관리 수수료 · Admin fee: X.XX% per year on AUC`)
- Review screen → include in pricing summary row
- Confirmation email → disclose in "What happens next" block

**Recommendation.** When the admin fee is finalized by the CFO agent, add one line to Section 3 (Plan Design) below the contribution input:
```
관리 수수료 · Admin fee: 0.25% per year on total AUC
(deducted monthly from your gold grams)
```
And one row to the Review summary card. Don't hide it — HNW customers expect transparent fee disclosure.

---

## 11. Accessibility & Mobile Notes

- All form inputs have associated labels and appropriate `autocomplete` hints (`name`, `email`, `tel`, `postal-code`, `street-address`, etc.).
- Korean mobile keyboards: ensure `inputmode="numeric"` on RRN and account number fields.
- Minimum tap target: 44×44px on mobile (buttons and consent checkboxes comply).
- Form validation should be inline (show errors next to the field, not at the top).
- Color contrast meets WCAG AA on gold/black combinations (verified on prototype).
- Progress is preserved on browser back/refresh — save form state to sessionStorage between sections.

---

## 12. Open Questions Needing Founder Decision

1. **RRN vs PASS-only.** Preference? PASS-only is cleaner PIPA-wise but fewer users may complete if PASS app not installed. Recommend: collect both paths, default to PASS with RRN fallback.
2. **Admin fee %.** CFO to finalize. Recommendation: 0.20–0.30% per year on AUC.
3. **Crypto as auto-recurring.** Technically possible via customer-held wallet + programmatic memos, but adds Travel Rule complexity. Launch with manual-with-reminder and upgrade in Phase 2?
4. **Bank list.** Which banks beyond Toss/Kakao/KB should we support at launch? Recommend top 8 by Korean market share.
5. **KYC vendor choice.** Decide between NICE평가정보, KCB, or KB Kookmin 실명확인 API for real-name verification — GC Agent to negotiate contract.
6. **Email provider.** Postmark (best deliverability), Resend (best DX), SendGrid (most features), or native AWS SES with SNS for webhooks.

---

## 13. Implementation Priority & Timeline

**Phase 1 (week 1–2).** Build scaffolding only — no real KYC or payment integration.
- Move existing `/shop` listing content to `/shop/physical`
- Build new `/shop` selector page
- Update all homepage CTAs to route to `/shop` (per Section 8.3 audit)
- Build `/agp/intro` 5-step click-through
- Build `/agp/enroll` form UI with all 6 sections + success screen
- Confirmation email template (sent via SES or Resend)
- Stub PASS button and 1원 송금 verify button (return mock success)

**Phase 2 (week 3–5).** Wire up real integrations.
- KYC vendor integration (real RRN → 실명확인 token)
- Toss Payments PG contract signed + test mode
- Open Banking 1원 송금 live
- Recurring auto-debit mandate creation
- Email verification link flow

**Phase 3 (week 6+).** Enhancements.
- Crypto payment channel (USDT/USDC)
- Admin fee disclosure layer
- Customer dashboard (grams, KRW value, conversion progress)
- Daily backing report page (`/backing`)

---

## 14. Files Delivered in This Handoff

1. **`aurum_agp_flow_prototype.html`** — fully interactive single-file prototype. Open in browser, use top nav to switch between the four screens. Includes desktop/mobile toggle bottom-right. This is the **visual source of truth**.
2. **This document** (`aurum_agp_flow_handoff.md`) — written specification.

**Next handoff.** Once the website agent has a working scaffold, Marketing Agent reviews copy polish; GC Agent reviews consent/KYC/FX items (Section 6 of this doc); CFO agent adds admin fee layer.

---

## 15. One-Line Summary For Agent Memory Archive

> Four-screen AGP onboarding flow: homepage CTAs (매매 top nav + 지금 배분 시작 hero) → `/shop` selector (physical purchase vs AGP) → `/agp/intro` 5-step click-through → `/agp/enroll` 6-section KYC+plan+payment form → confirmation email. Existing `/shop` listing moves to `/shop/physical`. Prototype file and spec committed 2026-04-14 rev 2. Website Agent owns scaffolding + CTA audit, Marketing Agent owns copy, GC Agent owns compliance, CFO Agent owns admin fee addition.
