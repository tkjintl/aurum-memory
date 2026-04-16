## 2026-04-16 23:30 UTC — Marketing Agent (website design audit + handoff)

**Request:** Full visual + content audit of live site across mobile/desktop: text sizing, Korean copy quality, USD amounts, sign-up flow unification, card differentiation, alignment, Education/Storage/Why Gold refinement, 진짜 금 진짜 은 centering bug
**Outcome:** ✅ Handoff document produced — 22 issues across 9 tiers, fully prescriptive code-level instructions for Web Agent

**Files generated:**
- 2026-04-16_aurum_website_design_handoff_v1.md (47KB) — full prescriptive web agent handoff
  tmpfiles: https://tmpfiles.org/dl/33994031/2026-04-16_aurum_website_design_handoff_v1.txt
  permanent: https://raw.githubusercontent.com/tkjintl/aurum-memory/main/deliverables/2026-04-16_23-30-00_marketing_website_design_handoff_v1.md

**Channel(s):** N/A (internal handoff doc)
**Mode:** N/A

**Key findings:**
- CRITICAL: Hero eyebrow JSX bug — ternary renders as literal code text, visible on production site (missing `{}`)
- CRITICAL: `html { zoom: 1.15/1.2 }` in index.css causes ALL desktop text to be 15-20% oversized — REMOVE
- HIGH: 진짜 금 진짜 은 hero not centered on desktop 100% — flex layout fix + mobile h1 reduce 34→28px
- HIGH: InfoPages/UserPages/ShopPages still use hardcoded `80px` horizontal padding — must switch to `.aurum-container`
- HIGH: USD amounts in Leaderboard, DualSavingsPanel, Dashboard, SellFlow — all must be KRW primary
- HIGH: AGPEnrollPage uses hardcoded SPOT_PER_GRAM=155000 (33% off live price) — must use live prices
- HIGH: Sign-up flows not unified — AGPLaunch email capture doesn't route to agp-enroll; Shop cart without login shows login modal not sign-up
- HIGH: '백업 리포트' typo (data backup) — must be '백킹 리포트' (physical backing)
- MED: Box differentiation — bg1 (#111111) cards on bg (#0a0a0a) near-invisible on OLED — new CSS classes added
- MED: AGP premium 2% vs 8% inconsistency — flagged to founder for resolution
- MED: 'AGP 적금' in nav should be 'AGP 적립'
- MED: Warm-brown color artifacts (#111008, #1a1510) remain in HomePage — replace with neutral grays

**Decisions recorded in aurum_context.md:** none (founder decision needed on AGP premium 2% vs 8%)

**Open items added:**
- ⚠️ FOUNDER DECISION: AGP premium — is it 8% (same as physical, per FPA v9) or 2% (separate AGP rate)? Currently inconsistent across InfoPages (2%) and HomePage calc (8%). Must resolve before launch.
- Web Agent to implement 22 issues in handoff document in 9-pass sequence
- CartCheckout.jsx not yet audited for full flow — partial fix in handoff (checkout gate), full audit pending

**Cross-domain notes:**
- CFO: Confirm AGP premium rate (2% vs 8%) — affects all displayed calculations in AGPPage, AGPEnrollPage, AGPLaunchPage
- Web Agent: Full handoff document at tmpfiles above — implement in order, Tier 1 first

---

## 2026-04-16 22:22 UTC — Website Agent (handoff audit v1)

**Request:** Full audit of live site vs aurum_web_agent_handoff.md — fix all 7 tasks: alignment, icon sizing, links, labels, ticker, KRW conversion, English pass
**Outcome:** ✅ npm build PASSED (48 modules, 0 errors). 5 files delivered.

**Files generated:**
- Nav.jsx (8.8KB) → target: src/components/Nav.jsx
  tmpfiles: http://tmpfiles.org/dl/33991166/nav.jsx
  permanent: https://raw.githubusercontent.com/tkjintl/aurum-memory/main/deliverables/2026-04-16_22-22-00_website_handoff_audit_v1.md
- Ticker.jsx (4.6KB) → target: src/components/Ticker.jsx
  tmpfiles: http://tmpfiles.org/dl/33991169/ticker.jsx
- index.css (22KB) → target: src/index.css
  tmpfiles: http://tmpfiles.org/dl/33991173/index.css
- FoundersClubPage.jsx (42KB) → target: src/campaigns/FoundersClubPage.jsx
  tmpfiles: http://tmpfiles.org/dl/33991177/foundersclubpage.jsx
- HomePage.jsx (36KB) → target: src/pages/HomePage.jsx
  tmpfiles: http://tmpfiles.org/dl/33991180/homepage.jsx

**Changes:** Nav alignment, SVG icons, Ticker marquee fix, FoundersClubPage KRW, HomePage bilingual 47 ternaries

**Open items added:**
- BaseUI.jsx legacy file needs cleanup
- InfoPages.jsx, ShopPages.jsx, AGPLaunchPage.jsx, UserPages.jsx not yet audited for English strings

**Cross-domain notes:** none

---

## 2026-04-16 16:30 UTC — Website Agent (redesign Phase 1)

**Request:** Full website redesign per aurum-korea-redesign-handoff.md
**Outcome:** ✅ npm build PASSED. 6 files delivered (all tmpfiles expired).

---

## 2026-04-16 07:24 UTC — GC (Legal Counsel)

**Request:** Diagnostic run
**Outcome:** ✅ All 6 checks passed

---

## 2026-04-15 18:40 UTC — Website Agent (exgold source + don conversion fix)

**Request:** Change KR gold/silver price source to exgold.co.kr
**Outcome:** ✅ npm build PASSED. 2 files delivered (expired).

---
