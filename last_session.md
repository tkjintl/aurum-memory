## 2026-04-15 — Website Agent (한국금거래소 savings calc source swap)

**Request:** Replace KB Star reference with 한국금거래소 매도가 in savings calculator + audit all Korean price comparisons
**Outcome:** ✅ npm build PASSED (28 modules, 0 errors). 2 files delivered.

**Files generated:**
- 20260415_174522_ShopPages_krgold.jsx → src/ShopPages.jsx
  tmpfiles: https://tmpfiles.org/dl/33780561/20260415_174522_shoppages_krgold.jsx
  permanent: best-effort archive attempted (file >85KB)
- 20260415_174522_BaseUI_krgold.jsx → src/BaseUI.jsx
  tmpfiles: https://tmpfiles.org/dl/33780550/20260415_174522_baseui_krgold.jsx

**What changed:**
- ShopPages.jsx: goldKB_usd/silverKB_usd → goldKR_usd/silverKR_usd; multipliers 1.15/1.25 → 1.10 (VAT 10%); labels "한국 실물 시가" → "한국금거래소 매도가 (부가세 포함)"; footnote removes KB Star; ProductPage koreaPrice 1.15→1.10; CartPage koreaTotal 1.15→1.10; labels updated to 한국금거래소 매도가
- BaseUI.jsx: Added 5th ticker item "한국금거래소 금/g" showing ₩/g = prices.gold * krwRate * 1.10 / 31.1035; updated price formatter to handle isKrGold flag

**Decisions recorded in aurum_context.md:**
- Korean market comparison price source: 한국금거래소 매도가 (부가세 포함) = spot_USD × KRW rate × 1.10

**Open items added:** none

**Cross-domain notes:** none

---

## 2026-04-15 — Website Agent (panel swap + 아름→Aurum)

**Request:** Swap gold/silver savings panels, replace all 아름 with Aurum
**Outcome:** ✅ npm build PASSED (28 modules, 0 errors). 3 files delivered.

**Files generated:**
- 20260415_ShopPages_panelfix.jsx → src/ShopPages.jsx
  tmpfiles: https://tmpfiles.org/dl/33663897/20260415_shoppages_panelfix.jsx
- 20260415_UserPages_aurum.jsx → src/UserPages.jsx
  tmpfiles: https://tmpfiles.org/dl/33663898/20260415_userpages_aurum.jsx
- 20260415_ShopSelectorPage_aurum.jsx → src/pages/ShopSelectorPage.jsx
  tmpfiles: https://tmpfiles.org/dl/33663899/20260415_shopselectorpage_aurum.jsx

**What changed:**
- ShopPages.jsx: swapped savings panels (silver LEFT, gold RIGHT); 아름→Aurum; footer note
- UserPages.jsx: AGP section header 아름 골드 플랜 → Aurum 골드 플랜
- ShopSelectorPage.jsx: AGP card heading + comment 아름→Aurum

---

## 2026-04-15 — Website Agent

**Request:** Restore good Ticker format + swap useLivePrices to call /api/prices (Yahoo Finance serverless)
**Outcome:** ✅ npm build PASSED (28 modules, 0 errors). 1 file delivered (lib.jsx only).

**Files generated:**
- 20260415_lib_apiv2.jsx → src/lib.jsx
  tmpfiles: https://tmpfiles.org/dl/33663210/20260415_lib_apiv2.jsx

**What changed (lib.jsx only):**
- `useLivePrices` now calls `/api/prices` (Vercel serverless) instead of GoldAPI direct
- Ticker format in BaseUI.jsx left EXACTLY as-is

---

## 2026-04-15 01:30 UTC — Website Agent

**Request:** Full audit of handoff v2 doc + all additional founder requests, test-fix-test loop
**Outcome:** ✅ npm build PASSED (28 modules, 0 errors). 50+ items verified. 4 files delivered (final).

**Files (FINAL):**
- src/lib.jsx: http://tmpfiles.org/dl/33651295/20260415_lib_final.jsx
- src/App.jsx: http://tmpfiles.org/dl/33651298/20260415_app_final.jsx
- src/ShopPages.jsx: http://tmpfiles.org/dl/33651301/20260415_shoppages_final.jsx
- src/UserPages.jsx: http://tmpfiles.org/dl/33651305/20260415_userpages_final.jsx
