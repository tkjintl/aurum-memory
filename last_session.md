## 2026-04-15 00:30 UTC — Website Agent

**Request:** Currency toggle (USD/KRW), pricing math fix, shop/cart overhaul, AGP grid, Storage FAQ move + all 9 gap items
**Outcome:** ✅ npm build PASSED (28 modules, 0 errors). 4 files.

**Files:**
- 20260415_lib.jsx → src/lib.jsx: http://tmpfiles.org/dl/33650394/20260415_lib.jsx
- 20260415_App.jsx → src/App.jsx: http://tmpfiles.org/dl/33650399/20260415_app.jsx
- 20260415_ShopPages.jsx → src/ShopPages.jsx: http://tmpfiles.org/dl/33650401/20260415_shoppages.jsx
- 20260415_UserPages.jsx → src/UserPages.jsx: http://tmpfiles.org/dl/33650402/20260415_userpages.jsx

**Changes:**
- Currency toggle (₩/$) — App.jsx state, passed to Home/Shop/ProductPage/CartPage/Checkout. CurrencyToggle component top-right in shop, product, cart pages. Single price line using fPrice() helper.
- Pricing math fixed: Korea=spot*1.15 (15% kimchi), duty=unit*0.13 (13%), storage=unit*0.003. Savings now ≠ duty.
- ProductPage: removed (KRX 기준) and (현물 + 8%) labels; storage fee row below Aurum 가격; removed annual comment; grand total fPrice primary.
- CartPage: product name link → navigate to product page; 쇼핑 계속하기 → shop-physical; cart summary toggle-aware; USD secondary line removed.
- lib.jsx: product names "1oz"→"1 oz", "1kg"→"1 kg", "100oz"→"100 oz" etc.
- Stat strip: clamp(24px,4vw,48px) + centered text in 3 columns
- Hero CTA bars + Silver CTAs: flex:1 equal width
- AGP steps: 3×2 grid (3 columns)
- Storage FAQ: moved to end of page (after compliance)
- G-1 chips: equal sizing, trailing spaces removed
- G-2 CTA bars: equal width with flex:1
- G-8 FAQ: question trailing spaces removed, English alternatives removed from q fields
- BenefitTile/StepCard trailing spaces cleaned
- C-3 clamp done, C-4/C-5 equal width done

---

## 2026-04-14 22:30 UTC — Website Agent
(see previous entries)
