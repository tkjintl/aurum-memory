## 2026-04-15 18:10 UTC — Website Agent (KR ticker → per-don + remove % change)

**Request:** KR physical gold ticker: change unit to per-don (3.75g), update label to 한국금거래소 매도가 (1돈 / 부가세 포함), remove % change for USD/KRW and KR gold ticker items
**Outcome:** ✅ npm build PASSED (28 modules, 0 errors). 1 file delivered (BaseUI.jsx only).

**Files generated:**
- 20260415_181045_website_BaseUI_donprice_v1.jsx → src/BaseUI.jsx
  tmpfiles: https://tmpfiles.org/dl/33785351/20260415_181045_website_baseui_donprice_v1.jsx
  permanent: https://raw.githubusercontent.com/tkjintl/aurum-memory/main/deliverables/20260415_181045_website_BaseUI_donprice_v1.jsx

**What changed (BaseUI.jsx only, 4 targeted edits):**
- KR gold item label: "한국금거래소 금/g" → "한국금거래소 매도가 (1돈 / 부가세 포함)"
- KR gold item price: Math.round(…/31.1035) → Math.round(…/31.1035 * 3.75) — per 1 don (3.75g)
- Display string: /g → /돈
- % change span: `{!item.noChange && <span>…</span>}` — noChange:true added to USD/KRW and KR gold items; % change completely hidden for both
- ShopPages.jsx: NO CHANGES (already uses spot×1.10 formula; label already says 한국금거래소 매도가 (부가세 포함))

**Decisions recorded in aurum_context.md:** KR gold ticker now shows per-don price; unit suffix is /돈

**Open items added:** none

**Cross-domain notes:** none

---

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
- ShopPages.jsx: goldKB_usd/silverKB_usd → goldKR_usd/silverKR_usd; multipliers 1.15/1.25 → 1.10 (VAT 10%); labels updated to 한국금거래소 매도가
- BaseUI.jsx: Added 5th ticker item "한국금거래소 금/g" showing ₩/g = prices.gold * krwRate * 1.10 / 31.1035

---

## 2026-04-15 — Website Agent (panel swap + 아름→Aurum)

**Request:** Swap gold/silver savings panels, replace all 아름 with Aurum
**Outcome:** ✅ npm build PASSED (28 modules, 0 errors). 3 files delivered.

**Files generated:**
- 20260415_ShopPages_panelfix.jsx → src/ShopPages.jsx: https://tmpfiles.org/dl/33663897/20260415_shoppages_panelfix.jsx
- 20260415_UserPages_aurum.jsx → src/UserPages.jsx: https://tmpfiles.org/dl/33663898/20260415_userpages_aurum.jsx
- 20260415_ShopSelectorPage_aurum.jsx → src/pages/ShopSelectorPage.jsx: https://tmpfiles.org/dl/33663899/20260415_shopselectorpage_aurum.jsx

---

## 2026-04-15 — Website Agent (lib.jsx api/prices swap)

**Request:** Restore good Ticker format + swap useLivePrices to call /api/prices
**Outcome:** ✅ npm build PASSED. 1 file: https://tmpfiles.org/dl/33663210/20260415_lib_apiv2.jsx

---

## 2026-04-15 01:30 UTC — Website Agent (full audit + handoff v2)

**Request:** Full audit of handoff v2 doc + all additional founder requests
**Outcome:** ✅ npm build PASSED (28 modules). 4 files:
- src/lib.jsx: http://tmpfiles.org/dl/33651295/20260415_lib_final.jsx
- src/App.jsx: http://tmpfiles.org/dl/33651298/20260415_app_final.jsx
- src/ShopPages.jsx: http://tmpfiles.org/dl/33651301/20260415_shoppages_final.jsx
- src/UserPages.jsx: http://tmpfiles.org/dl/33651305/20260415_userpages_final.jsx
