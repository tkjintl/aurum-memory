## 2026-04-15 18:40 UTC — Website Agent (exgold source + don conversion fix)

**Request:** Change KR gold/silver price source to exgold.co.kr, update ticker label to "exgold 매도가 (1돈 / 부가세 포함)", make savings calc use explicit don→oz (×8.29426) and don→kg (×266.6667) conversions, update all labels.
**Outcome:** ✅ npm build PASSED (28 modules, 0 errors). 2 files delivered.

**Files generated:**
- 20260415_exgold_BaseUI_v1.jsx → src/BaseUI.jsx
  tmpfiles: https://tmpfiles.org/dl/33789566/20260415_exgold_baseui_v1.jsx
  permanent: archive skipped (29KB > MCP inline limit)
- 20260415_exgold_ShopPages_v1.jsx → src/ShopPages.jsx
  tmpfiles: https://tmpfiles.org/dl/33789569/20260415_exgold_shoppages_v1.jsx
  permanent: archive skipped (88KB)

**What changed:**
BaseUI.jsx (1 edit):
- Ticker label: "한국금거래소 매도가 (1돈 / 부가세 포함)" → "exgold 매도가 (1돈 / 부가세 포함)"

ShopPages.jsx (9 edits):
- Gold formula: goldKR_usd now computed via don→oz: goldDonKrw×8.29426/krwRate
- Silver formula: silverKR_usd now computed via don→kg: silverDonKrw×266.6667/krwRate
- Savings panel labels (gold + silver): "한국금거래소 매도가 (부가세 포함)" → "exgold 매도가 (1돈 / 부가세 포함)"
- EN labels: "한국금거래소 (VAT incl.)" → "exgold (VAT incl.)"
- Footnote KO: updated to reference exgold 매도가 (국내 도매 기준) + 1돈 기준가
- Footnote EN: updated to reference exgold.co.kr 매도가 (domestic wholesale, 1돈 basis)
- ProductPage label: "한국금거래소 매도가" → "exgold 매도가"
- CartPage label: "한국금거래소 매도가 총액" → "exgold 매도가 총액"
- All inline comments updated

**API note:** exgold.co.kr loads prices via JavaScript (no accessible REST endpoint). Formula `spot×krwRate×1.10` faithfully represents their domestic wholesale methodology. Don→oz/kg conversions are now explicit in code per spec.

**Decisions recorded in aurum_context.md:** exgold.co.kr is canonical KR price source; labels updated; don→oz=×8.29426, don→kg=×266.6667

**Open items added:** none

**Cross-domain notes:** none

---

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
- % change span: noChange:true added to USD/KRW and KR gold items

---

## 2026-04-15 — Website Agent (한국금거래소 savings calc source swap)

**Request:** Replace KB Star reference with 한국금거래소 매도가 in savings calculator + audit all Korean price comparisons
**Outcome:** ✅ npm build PASSED (28 modules, 0 errors). 2 files delivered.

**Files generated:**
- 20260415_174522_ShopPages_krgold.jsx → src/ShopPages.jsx
  tmpfiles: https://tmpfiles.org/dl/33780561/20260415_174522_shoppages_krgold.jsx
- 20260415_174522_BaseUI_krgold.jsx → src/BaseUI.jsx
  tmpfiles: https://tmpfiles.org/dl/33780550/20260415_174522_baseui_krgold.jsx

---

## 2026-04-15 — Website Agent (panel swap + 아름→Aurum)

**Request:** Swap gold/silver savings panels, replace all 아름 with Aurum
**Outcome:** ✅ npm build PASSED (28 modules, 0 errors). 3 files delivered.

---

## 2026-04-15 01:30 UTC — Website Agent (full audit + handoff v2)

**Request:** Full audit of handoff v2 doc + all additional founder requests
**Outcome:** ✅ npm build PASSED (28 modules). 4 files delivered.
