# AURUM KOREA — WEBSITE DESIGN & CONTENT HANDOFF v1
## Prescriptive Fix Specification for Web Agent
**Prepared by:** Marketing Agent  
**Date:** 2026-04-16  
**Scope:** Full site audit — mobile/desktop layout, Korean text, USD cleanup, sign-up architecture, visual consistency  
**Files reviewed:** index.css, App.jsx, Nav.jsx, Ticker.jsx, HomePage.jsx, InfoPages.jsx, ShopPages.jsx, UserPages.jsx, CartCheckout.jsx, AGPPages.jsx, FoundersClubPage.jsx, AGPLaunchPage.jsx, Footer.jsx, UI.jsx, lib/index.jsx

See full handoff at tmpfiles: https://tmpfiles.org/dl/33994031/2026-04-16_aurum_website_design_handoff_v1.txt

## Summary of 22 Issues Found (9 Tiers)

TIER 1 - CRITICAL:
1. Hero eyebrow JSX bug - ternary renders as literal text (missing {})
2. html { zoom: 1.15/1.2 } makes ALL desktop text 15-20% oversized - REMOVE
3. '백업 리포트' typo - must be '백킹 리포트'

TIER 2 - LAYOUT:
4. 진짜 금 진짜 은 hero not centered desktop 100% + mobile h1 too big
5. InfoPages/UserPages/ShopPages/AGPPages don't use aurum-container
6. Various mobile font size reductions needed

TIER 3 - USD CLEANUP:
7A. LeaderboardWidget GMV in $ → ₩
7B. DualSavingsPanel $/oz → ₩/g
7C. GMVCalculator annual GMV $ → ₩ primary
7D. GateCards hardcoded gmvKR labels
7E. Dashboard summary cards USD → KRW primary
7F. SellFlow bid prices USD → KRW primary
7G. AGPEnrollPage SPOT_PER_GRAM hardcoded (₩155,000) → live price
7H. AGPLaunchPage GMV_BONUSES $USD gate labels → KRW primary

TIER 4 - SIGN-UP UNIFICATION:
8A. AGPLaunchPage email capture → redirect to agp-enroll
8B. ProductPage "Add to Cart" without user → navigate to agp-enroll
8C. CheckoutPage without user → account creation gate
8D. AGPEnrollPage note that account works for ALL products

TIER 5 - VISUAL DESIGN:
9. card-elevated / card-gold-accent CSS classes + apply to all old pages
10. Section background alternation on old info pages
11. Warm-brown #111008/#1a1510 artifacts in HomePage → neutral grays

TIER 6 - KOREAN TEXT:
12. AGP premium 2% vs 8% inconsistency (flag to founder)
13. '한국금거래소' → 'exgold 매도가' in comparison table
14. 'AGP 적금' → 'AGP 적립' in Nav
15. Fluency fixes: 5 specific phrases

TIER 7 - MOBILE POLISH:
16. Mobile campaign panels missing aurum-container gutter
17. Mobile nav font size reduction
18. Mobile shop card padding

TIER 8 - INTERACTIVE:
19. Accordion mobile padding
20. Ticker hover cursor feedback

TIER 9 - CLEANUP:
21. BaseUI.jsx legacy comment
22. MARKET_FACTS goldATH update to ~$3,500
