# Aurum Korea — Website Redesign Handoff Document
**For: Web Agent / Developer Implementation**
**Source repo:** https://github.com/tkjintl/aurum-website/tree/main
**Reference shell:** aurum-shell.html (previously delivered)

---

## 0. HOW TO USE THIS DOCUMENT

This document maps every visual, structural, and content decision made when redesigning the Aurum Korea site. Each section is written as a **direct prompt or instruction** your web agent can execute against the existing React codebase. Follow sections in order — architecture changes first, then per-file changes, then polish passes.

**Files in scope:**
- `BaseUI.jsx` — Ticker, Nav, LoginModal, NewsSection
- `ShopPages.jsx` — Home, Shop, ProductPage, CartPage, Checkout
- `UserPages.jsx` — Dashboard, OrderHistory, Account, KYC, WhyGold, Learn, Storage, AGP, AGPBackingReport
- `AGPIntroPage.jsx` — 5-step walkthrough
- `AGPEnrollPage.jsx` — 6-step enrollment form
- `ShopSelectorPage.jsx` — Physical vs AGP chooser
- `index.css` — Global styles
- `aurum-motion.css` — Grain, magnetic cards

---

## 1. GLOBAL ARCHITECTURE CHANGE

### 1.1 Layout Paradigm: Horizontal Panes → Vertical Scroll SPA

**Problem:** Current site splits each page into horizontal panels (left/right or top/bottom fixed-height panes). This creates cognitive overload and prevents natural editorial flow.

**Change:** Convert all page layouts to full-width vertical scroll sections.

**Agent instruction:**
```
In every page component, remove any CSS that uses:
  - display: grid with fixed left/right column splits for content areas
  - height: 100vh or minHeight: 100vh on non-hero sections
  - overflow: hidden on page containers
  - fixed-position content panels (not counting the sticky nav)

Replace with:
  - Full-width sections stacked vertically
  - Each section: width: 100%, padding: 80px (desktop) / 40px 16px (mobile)
  - alternating background: #0a0a0a and #0d0b08 for visual separation
  - No horizontal splits except for 2-column layouts within a section (which is fine)
```

### 1.2 Add Scroll Progress Bar

**Add to `App.jsx` or root layout:**
```javascript
// Insert near top of root component render
useEffect(() => {
  const bar = document.createElement('div');
  bar.id = 'scroll-progress';
  bar.style.cssText = 'position:fixed;top:0;left:0;height:2px;background:linear-gradient(90deg,#c5a572,#8a6914);z-index:10000;width:0%;transition:width 0.1s;';
  document.body.appendChild(bar);
  const update = () => {
    const pct = window.scrollY / (document.documentElement.scrollHeight - window.innerHeight) * 100;
    bar.style.width = pct + '%';
  };
  window.addEventListener('scroll', update);
  return () => { window.removeEventListener('scroll', update); bar.remove(); };
}, []);
```

### 1.3 Add Global `.reveal` Entrance Animation System

**Add to `index.css`:**
```css
.reveal {
  opacity: 0;
  transform: translateY(28px);
  transition: opacity 0.65s ease, transform 0.65s ease;
}
.reveal.visible {
  opacity: 1;
  transform: translateY(0);
}
```

**Add to `App.jsx` or a shared hook:**
```javascript
useEffect(() => {
  const observer = new IntersectionObserver(
    (entries) => entries.forEach(e => { if (e.isIntersecting) e.target.classList.add('visible'); }),
    { threshold: 0.12 }
  );
  document.querySelectorAll('.reveal').forEach(el => observer.observe(el));
  return () => observer.disconnect();
}, []);
```

**Usage:** Add `className="reveal"` to every section wrapper `<div>` in every page component. Stagger children with `style={{ transitionDelay: '0.1s' }}`, `'0.2s'`, etc.

### 1.4 Remove All Emoji from UI Elements

**Problem:** Emoji (🥇📋💰🔒✅❌ etc.) are used as icons throughout the codebase. This conflicts with the luxury aesthetic.

**Agent instruction:**
```
Search all .jsx files for emoji characters used inside style/UI elements 
(not inside user data strings). Replace with one of:

  a) A simple inline SVG (see Section 6 for SVG library)
  b) A CSS-only shape (circle, line, checkmark via ::before)
  c) Remove entirely if decorative

Specifically:
  - 🥇 (gold medal) → inline SVG bar icon (rectangle with shine line)
  - 📋 (clipboard) → remove, use text label only
  - 💰 → remove, use the savings number itself as the hero element
  - 🔒 → SVG padlock (two arcs + rectangle)
  - ✅ → CSS checkmark: border-bottom + border-right rotated 45deg
  - ❌ → CSS × via ::before/::after
  - 🌍 → SVG globe outline
  - 🏆 → SVG laurel/podium shape

Do NOT remove emoji from user-facing data strings like product descriptions 
or order item names unless explicitly asked.
```

---

## 2. `BaseUI.jsx` — TICKER & NAV CHANGES

### 2.1 Ticker — Full-Width Animated Marquee

**Current state:** Ticker renders as a static flex row, items may overflow on mobile.

**Change:** Convert to CSS marquee animation so it scrolls continuously.

**Agent instruction:**
```
In the Ticker component:

1. Wrap all ticker items in TWO identical <div> containers side by side 
   inside a parent with overflow: hidden.

2. Apply this CSS animation to the inner wrapper:
   @keyframes ticker-scroll {
     0%   { transform: translateX(0); }
     100% { transform: translateX(-50%); }
   }
   .ticker-track {
     display: flex;
     animation: ticker-scroll 40s linear infinite;
     width: max-content;
   }
   
3. Each ticker item should be:
   - padding: 0 32px
   - borderRight: 1px solid rgba(197,165,114,0.15)
   - display: flex, gap: 10px, alignItems: center
   
4. Price values: fontFamily JetBrains Mono, fontSize 12px, color #f5f0e8
5. Labels: fontFamily Outfit, fontSize 10px, color #8a7d6b, letterSpacing 2, uppercase
6. Change %: green (#4ade80) if positive, red (#f87171) if negative
7. Add a thin gold left-border accent line on the far left of the ticker bar
8. Ticker bar background: #0d0b08, height: 36px, borderBottom: 1px solid #1a1510
```

### 2.2 Nav — Scroll-Aware Backdrop Blur

**Current state:** Nav has static background.

**Change:** Nav background deepens (more opaque, adds blur) after 60px scroll.

**Agent instruction:**
```
In the Nav component, add a scroll listener:

  const [scrolled, setScrolled] = useState(false);
  useEffect(() => {
    const fn = () => setScrolled(window.scrollY > 60);
    window.addEventListener('scroll', fn);
    return () => window.removeEventListener('scroll', fn);
  }, []);

Apply to nav container style:
  background: scrolled 
    ? 'rgba(10,10,10,0.95)' 
    : 'rgba(10,10,10,0.7)',
  backdropFilter: 'blur(12px)',
  borderBottom: scrolled 
    ? '1px solid rgba(197,165,114,0.2)' 
    : '1px solid transparent',
  transition: 'all 0.3s ease'
```

### 2.3 Nav — Always-Visible Gold CTA Button

**Change:** The primary CTA ("지금 시작" or "시작하기") should always be visible in the nav as a gold button, not just a text link.

**Agent instruction:**
```
In the Nav desktop link list, ensure the last item renders as:
  <button style={{
    background: 'linear-gradient(135deg, #c5a572, #8a6914)',
    color: '#ffffff',
    border: 'none',
    padding: '8px 20px',
    borderRadius: 4,
    fontFamily: "'Outfit', sans-serif",
    fontWeight: 700,
    fontSize: 13,
    letterSpacing: 0.5,
    cursor: 'pointer'
  }}>
    지금 시작
  </button>

This button navigates to 'shop-selector' or 'agp-enroll'.
```

---

## 3. `ShopPages.jsx` — HOME PAGE

The Home page is the highest-impact change. Apply these in order.

### 3.1 Hero — Add Live Price Comparison Card

**Current state:** Hero is text + two CTA buttons. Price data exists but is displayed in a simple inline savings panel below.

**Change:** Add a side-by-side price comparison card to the right of the hero text (on desktop) that shows Aurum price vs Korean retail price as animated bar chart.

**Agent instruction:**
```
In the Home component hero section, add a second column (desktop only — 
hide on mobile with display: none when isMobile) containing a price card:

Structure:
  <div style={{ background: 'rgba(197,165,114,0.04)', border: '1px solid rgba(197,165,114,0.15)', borderRadius: 12, padding: '24px', minWidth: 280 }}>
    
    HEADER ROW: "금 1온스 기준 가격 비교" in Outfit 10px gold uppercase
    
    TWO BAR ROWS:
      Row 1 — "한국 시중가":
        - Label in Outfit 13px #8a7d6b
        - Bar: width proportional to higher price (100%), 
          background: rgba(248,113,113,0.3), border: 1px solid #f87171
        - Price: goldKR_krwPerOz formatted in KRW, JetBrains Mono 15px #f87171
      
      Row 2 — "Aurum 가격":
        - Label in Outfit 13px #8a7d6b
        - Bar: width = (goldAurum_krw / goldKR_krwPerOz) * 100%
          background: rgba(197,165,114,0.3), border: 1px solid #c5a572
        - Price: goldAurum_krw in KRW, JetBrains Mono 15px #c5a572
    
    SAVINGS BADGE (bottom):
      background: rgba(74,222,128,0.08)
      border: 1px solid rgba(74,222,128,0.3)
      borderRadius: 6
      padding: 10px 14px
      Text: "절약 금액: {goldSavings_krw in KRW} ({goldSavingsPct.toFixed(1)}%)"
      Color: #4ade80, JetBrains Mono 14px bold
    
    LIVE INDICATOR (top right corner of card):
      A 6px circle pulsing green, with "LIVE" text 9px #4ade80
      
      @keyframes pulse {
        0%, 100% { opacity: 1; transform: scale(1); }
        50% { opacity: 0.4; transform: scale(1.6); }
      }
      Apply: animation: pulse 2s ease-in-out infinite

The pricing variables are already computed at the top of the Home component:
  goldKR_krwPerOz, goldAurum_krw, goldSavings_krw, goldSavingsPct
Use them directly.
```

### 3.2 Hero — Simulated Live Price Tick

**Change:** Gold price visually ticks every 9 seconds (slight random drift) to communicate liveness.

**Agent instruction:**
```
In Home component, add:
  const [displayPrice, setDisplayPrice] = useState(prices.gold);
  
  useEffect(() => {
    const interval = setInterval(() => {
      setDisplayPrice(p => p + (Math.random() - 0.48) * 0.35);
    }, 9000);
    return () => clearInterval(interval);
  }, []);

Use displayPrice anywhere you currently show prices.gold in the hero card.
Wrap the price value in a span with:
  transition: color 0.4s ease
and briefly flash it gold on change:
  onTick → set color #c5a572, after 400ms reset to #f5f0e8
```

### 3.3 Add "Kimchi Premium" Problem Section (After Hero, Before Products)

**Current state:** The price comparison is woven into the hero area. There is no dedicated "problem" section explaining WHY Aurum exists.

**Change:** Add a standalone section immediately after the hero that visualizes the Korean gold premium as a waterfall/bar chart, with short explanatory copy.

**Agent instruction:**
```
Add a new <section> between the hero and the product/pricing area in Home.

Background: #0d0b08
Padding: 80px desktop, 40px 16px mobile

CONTENT LAYOUT (2 columns desktop, stacked mobile):

LEFT COLUMN — Copy block:
  Eyebrow: "왜 한국에서 금을 사면 손해인가" (10px gold uppercase, 4px letter-spacing)
  H2: "김치 프리미엄" (Cormorant Garamond 52px desktop, 36px mobile, weight 300, color #f5f0e8)
  Body paragraph 1: 
    "한국에서 금을 구입할 경우 부가세(10%)와 유통 마진을 포함하면 
     국제 현물가 대비 평균 20% 이상 높은 가격을 지불하게 됩니다."
    (Outfit 15px, #8a7d6b, lineHeight 1.75)
  Body paragraph 2:
    "은의 경우 공급 희소성으로 인해 프리미엄이 최대 30%에 달합니다.
     Aurum Korea는 싱가포르 자유무역지대(FTZ)를 통해 이 프리미엄을 
     우회하여 국제 현물가 기준으로 공급합니다."
    (Same styling)

RIGHT COLUMN — SVG Waterfall Chart:
  Create an inline SVG (width 100%, viewBox "0 0 400 260"):
  
  BAR 1 — "국제 현물가" (base): 
    x:30, y:80, width:80, height:130 
    fill: rgba(197,165,114,0.2), stroke: #c5a572, strokeWidth: 1
    Label below: "국제 현물가" Outfit 11px #8a7d6b
    Value above: "기준 100" JetBrains Mono 12px #c5a572
  
  CONNECTOR ARROW (right-pointing) between bars
  
  BAR 2 — "부가세 +10%":
    x:140, y:47, width:80, height:18
    fill: rgba(248,113,113,0.3), stroke: #f87171
    Small label: "+10% 부가세"
  
  BAR 3 (stacked on bar 2) — "유통마진 +10%":
    x:140, y:29, width:80, height:18  
    fill: rgba(251,146,60,0.3), stroke: #fb923c
    Label: "+10% 마진"
  
  COMBINED BAR 4 — "한국 시중가":
    x:250, y:29, width:80, height:181 (100 + 18 + 18 + connecting gaps)
    fill: rgba(248,113,113,0.12), stroke: #f87171
    Label below: "한국 시중가"
    Value above: "+20%" in #f87171 bold

  AURUM BAR 5 — "Aurum 가격":
    x:350, y:80, width:80, height:130 (same as base)
    fill: rgba(197,165,114,0.15), stroke: #c5a572, strokeWidth: 2
    Label below: "Aurum 가격"
    Value above: "+8%" in #c5a572

  SAVINGS BRACKET (connecting bar 4 top to bar 5 top):
    A curly-brace or bracket SVG path
    Label in middle: "절약" in #4ade80

Note: use approximate proportional heights — exact pixel perfection not required, 
visual impression of the gap is what matters.
```

### 3.4 Add "3 Pillars" Solution Section

**Current state:** Benefits are listed as text paragraphs or bullet lists.

**Change:** Replace with a 3-column card layout. Each pillar gets a custom SVG icon, a bold title, and 2-sentence description. Hover state reveals a gold underline accent.

**Agent instruction:**
```
After the Kimchi Premium section, add a 3-column pillars section.

Background: #0a0a0a
Padding: 80px desktop, 40px 16px mobile

Section header:
  Eyebrow: "Aurum이 다른 이유" (gold, uppercase, 10px, 4px letter-spacing)
  H2: "세 가지 핵심 차별점" (Cormorant Garamond 44px, weight 300, #f5f0e8)

3 CARDS in a CSS grid (repeat(3,1fr) desktop, 1fr mobile):
  Each card: background #111008, border 1px solid #1a1510, borderRadius 12, padding 28px
  On hover: borderColor rgba(197,165,114,0.4), transform translateY(-4px)
  Transition: all 0.3s ease

  CARD 1 — Icon: Globe SVG, Title: "국제 현물가", Body: "싱가포르 시장 기준 실시간 현물가로 공급합니다. 한국 내 부가세 및 유통마진을 완전히 제거합니다."
  
  CARD 2 — Icon: Vault SVG, Title: "배분 보관", Body: "Malca-Amit 싱가포르 FTZ 금고에 고객 명의로 완전 배분 보관됩니다. 혼장이 없습니다."
  
  CARD 3 — Icon: Chart SVG, Title: "월 적립 (AGP)", Body: "월 20만원부터 자동 적립으로 실물 금을 꾸준히 모아갑니다. 언제든 인출 및 배송 가능합니다."

INLINE SVG ICONS (place above each card title):
  Globe: 
    <svg width="32" height="32" viewBox="0 0 32 32" fill="none" stroke="#c5a572" strokeWidth="1.5">
      <circle cx="16" cy="16" r="13"/>
      <ellipse cx="16" cy="16" rx="6" ry="13"/>
      <line x1="3" y1="16" x2="29" y2="16"/>
      <line x1="5" y1="10" x2="27" y2="10"/>
      <line x1="5" y1="22" x2="27" y2="22"/>
    </svg>
  
  Vault:
    <svg width="32" height="32" viewBox="0 0 32 32" fill="none" stroke="#c5a572" strokeWidth="1.5">
      <rect x="4" y="6" width="24" height="20" rx="2"/>
      <circle cx="16" cy="16" r="5"/>
      <circle cx="16" cy="16" r="2"/>
      <line x1="16" y1="11" x2="16" y2="8"/>
      <line x1="21" y1="16" x2="24" y2="16"/>
      <line x1="4" y1="22" x2="28" y2="22"/>
    </svg>
  
  Chart:
    <svg width="32" height="32" viewBox="0 0 32 32" fill="none" stroke="#c5a572" strokeWidth="1.5">
      <polyline points="4,24 10,16 16,19 22,10 28,13"/>
      <line x1="4" y1="28" x2="28" y2="28"/>
      <line x1="4" y1="6" x2="4" y2="28"/>
    </svg>

HOVER UNDERLINE ACCENT on card title:
  .pillar-title::after {
    content: '';
    display: block;
    width: 0;
    height: 1px;
    background: #c5a572;
    transition: width 0.3s ease;
    marginTop: 6px;
  }
  .pillar-card:hover .pillar-title::after { width: 100%; }
```

### 3.5 Add Animated Metrics Strip

**Current state:** Customer/volume stats appear inline in copy paragraphs.

**Change:** Extract key metrics into a full-width dark strip with animated counters that count up from 0 when scrolled into view.

**Agent instruction:**
```
Add a metrics strip section (background: #090807, borderTop/Bottom: 1px solid #1a1510).
Padding: 48px 80px desktop, 32px 16px mobile.
Display: flex, justifyContent: space-around, flexWrap: wrap, gap: 32px.

4 METRIC ITEMS:

  1. Target: 5785 → display "5,785+"  label: "실물 보관 고객"
  2. Target: 38181 → display "38,181g" label: "총 보관 금 (g)"  
  3. Target: 20 → display "20%" label: "평균 절감 (금)"
  4. Target: 8 → display "8%" label: "Aurum 금 프리미엄"

COUNTER ANIMATION (JavaScript):
  Use IntersectionObserver. When the strip enters viewport, 
  animate each number from 0 to target over 1800ms using easeOutQuad:
  
  function animateCounter(el, target, duration, suffix) {
    const start = performance.now();
    const step = (now) => {
      const progress = Math.min((now - start) / duration, 1);
      const eased = 1 - Math.pow(1 - progress, 3);
      const current = Math.floor(eased * target);
      el.textContent = current.toLocaleString() + suffix;
      if (progress < 1) requestAnimationFrame(step);
    };
    requestAnimationFrame(step);
  }

Each metric item style:
  textAlign: center
  Value: Cormorant Garamond 48px, fontWeight 300, color #c5a572
  Label: Outfit 11px, letterSpacing 2, textTransform uppercase, color #8a7d6b
  Add a thin 1px gold bottom border under value with width 40px centered
```

### 3.6 Add Savings Calculator Widget

**Current state:** Savings comparison is static — shows 1 oz gold comparison only.

**Change:** Add an interactive slider widget where users input a purchase amount (in oz or KRW) and see their savings vs Korean retail in real time.

**Agent instruction:**
```
Add a savings calculator section after the metrics strip.
Background: #111008, padding: 80px desktop, 40px 16px mobile.

Layout: 2 columns desktop (copy left, calculator right), stacked mobile.

LEFT: 
  Eyebrow: "절감 계산기"
  H2: "얼마나 절약할 수 있나요?" (Cormorant Garamond 40px)
  Body: "구매하려는 금의 양을 입력하면 한국 시중가 대비 
         Aurum 구매 시 절약되는 금액을 실시간으로 확인할 수 있습니다."

RIGHT — Calculator Card:
  background: rgba(197,165,114,0.04)
  border: 1px solid rgba(197,165,114,0.2)
  borderRadius: 12, padding: 28px

  UNIT TOGGLE: 2 buttons — "온스 (oz)" / "그램 (g)"
  
  SLIDER:
    input type="range" min=1 max=100 defaultValue=10
    Custom CSS:
      input[type=range] { accent-color: #c5a572; width: 100%; }
    Label above: "{value} oz" or "{value}g" dynamically
  
  OUTPUT ROWS:
    "한국 시중가":  computed KRW price, color #f87171
    "Aurum 가격":   computed KRW price, color #c5a572  
    "절약 금액":    difference, color #4ade80, fontSize larger (20px), fontWeight bold
    "절약 비율":    percentage badge (green pill)
  
  COMPUTATION (using existing price variables from Home scope):
    const oz = sliderValue (if unit=oz) or sliderValue/31.1035 (if g)
    const koreanTotal = goldKR_krwPerOz * oz
    const aurumTotal = goldAurum_krw * oz  
    const saved = koreanTotal - aurumTotal
    const pct = (saved / koreanTotal * 100).toFixed(1)
  
  CTA BUTTON at bottom:
    "지금 구매하기 →" in gold gradient style
    onClick → navigate('shop')
```

---

## 4. `ShopPages.jsx` — SHOP / PRODUCT PAGES

### 4.1 Shop — Tab Filter Bar

**Current state:** Products displayed in a flat grid without filtering.

**Change:** Add horizontal tab bar above the grid for filtering: 전체 / 금 / 은 / 바 / 코인

**Agent instruction:**
```
In the Shop component, add state:
  const [filter, setFilter] = useState('all');

Filter config array:
  [
    { key: 'all', label: '전체' },
    { key: 'gold', label: '금' },
    { key: 'silver', label: '은' },
    { key: 'bar', label: '바' },
    { key: 'coin', label: '코인' }
  ]

Tab bar styles:
  Container: display flex, gap 8, marginBottom 32, flexWrap wrap
  Each tab: 
    background: filter===key ? '#c5a572' : 'transparent'
    color: filter===key ? '#0a0a0a' : '#8a7d6b'
    border: 1px solid (active: #c5a572, inactive: #2a2318)
    padding: 6px 18px, borderRadius: 20, fontSize 13
    fontFamily Outfit, fontWeight 600 when active
    cursor pointer

Filter logic on PRODUCTS array (from lib.jsx):
  const filtered = filter === 'all' ? PRODUCTS 
    : PRODUCTS.filter(p => 
        (filter === 'gold' && p.metal === 'gold') ||
        (filter === 'silver' && p.metal === 'silver') ||
        (filter === 'bar' && p.type === 'bar') ||
        (filter === 'coin' && p.type === 'coin')
      )

Note: if PRODUCTS objects don't have .metal and .type fields, add them to 
the products array in lib.jsx based on the product names.
```

### 4.2 Product Page — SVG Product Illustration

**Current state:** Product page shows product name and specs but no compelling visual.

**Change:** Replace any emoji product image with a minimalist SVG illustration of the product (gold bar or coin).

**Agent instruction:**
```
In ProductPage, replace the product image area with an inline SVG based on product type.

FOR GOLD/SILVER BAR:
  <svg viewBox="0 0 200 120" width="200" style={{display:'block',margin:'auto'}}>
    <defs>
      <linearGradient id="bar-grad" x1="0" y1="0" x2="1" y2="1">
        <stop offset="0%" stopColor="#e8d5a8"/>
        <stop offset="50%" stopColor="#c5a572"/>
        <stop offset="100%" stopColor="#8a6914"/>
      </linearGradient>
    </defs>
    {/* Main face */}
    <rect x="20" y="30" width="160" height="70" rx="4" fill="url(#bar-grad)"/>
    {/* Top bevel */}
    <polygon points="20,30 35,15 165,15 180,30" fill="#d4b88a"/>
    {/* Right side bevel */}
    <polygon points="180,30 165,15 165,85 180,100" fill="#9a7520"/>
    {/* Shine line */}
    <line x1="40" y1="45" x2="160" y2="45" stroke="rgba(255,255,255,0.3)" strokeWidth="1.5"/>
    {/* Weight text */}
    <text x="100" y="75" textAnchor="middle" fill="rgba(10,10,10,0.6)" 
          fontSize="14" fontFamily="'JetBrains Mono',monospace" fontWeight="600">
      {product.weight}
    </text>
  </svg>

FOR COIN:
  <svg viewBox="0 0 120 120" width="140" style={{display:'block',margin:'auto'}}>
    <defs>
      <radialGradient id="coin-grad" cx="40%" cy="35%">
        <stop offset="0%" stopColor="#e8d5a8"/>
        <stop offset="60%" stopColor="#c5a572"/>
        <stop offset="100%" stopColor="#8a6914"/>
      </radialGradient>
    </defs>
    <circle cx="60" cy="60" r="52" fill="url(#coin-grad)"/>
    <circle cx="60" cy="60" r="52" fill="none" stroke="#8a6914" strokeWidth="3"/>
    <circle cx="60" cy="60" r="44" fill="none" stroke="rgba(255,255,255,0.15)" strokeWidth="1"/>
    <text x="60" y="55" textAnchor="middle" fill="rgba(10,10,10,0.5)" 
          fontSize="10" fontFamily="'Outfit',sans-serif" letterSpacing="2">AURUM</text>
    <text x="60" y="72" textAnchor="middle" fill="rgba(10,10,10,0.5)" 
          fontSize="13" fontFamily="'JetBrains Mono',monospace" fontWeight="600">
      {product.weight}
    </text>
  </svg>

Determine bar vs coin by checking product.type or product.nameKo for 바 vs 코인.
```

---

## 5. `AGPIntroPage.jsx` — INTERACTIVE STEP PANEL

**Current state:** 5-step AGP walkthrough is likely a static list or horizontal carousel.

**Change:** Convert to vertical step selector — clicking a step opens an animated detail panel below it. Only one step open at a time. Non-selected steps show just the step number + title.

**Agent instruction:**
```
In AGPIntroPage, restructure the 5-step section as follows:

STATE: const [activeStep, setActiveStep] = useState(0)

STEP DATA ARRAY (5 items):
  Each item: { num, title, subtitle, body, meta: [{key, value}] }
  
  Step 0: "월 적립 설정" / "매월 자동 이체로 시작" / 
    body: "매달 지정한 날짜에 설정 금액이 자동 이체됩니다. 최소 월 20만원부터 시작할 수 있습니다."
    meta: [["최소 금액","₩200,000/월"],["이체일","월 1~28일 선택"],["결제","TossPay/KakaoPay/이체"]]
  
  Step 1: "실물 금 매입" / "국제 현물가 기준 즉시 매입" /
    body: "이체 완료 후 당일 국제 현물가 기준으로 실물 금 또는 은을 매입합니다."
    meta: [["매입 기준","국제 현물가 + 8%"],["매입 시점","이체 당일"],["최소 단위","0.01g"]]
  
  Step 2: "싱가포르 금고 배분 보관" / "Malca-Amit FTZ 귀하 명의 등록" /
    body: "매입된 금속은 싱가포르 자유무역지대 Malca-Amit 금고에 고객 명의로 배분 보관됩니다."
    meta: [["금고","Malca-Amit Singapore"],["지역","싱가포르 FTZ"],["보험","Lloyd's of London"]]
  
  Step 3: "보고서 & 투명성" / "매일 업데이트되는 적립 현황" /
    body: "대시보드에서 매일 적립량, 현재 시세 기준 자산 가치, 보관 증빙을 확인할 수 있습니다."
    meta: [["업데이트","매일"],["증빙","감사 추적 제공"],["접근","웹/앱 대시보드"]]
  
  Step 4: "인출 또는 배송" / "언제든 실물로 출금 가능" /
    body: "보관 중인 실물 금속은 언제든 한국으로 배송 신청하거나 현금화할 수 있습니다."
    meta: [["배송","한국 문 앞까지"],["처리","영업일 5~7일"],["출금","시세 기준 현금화 가능"]]

LAYOUT per step:
  Outer: border-bottom 1px solid #1a1510
  
  HEADER ROW (always visible, clickable):
    display flex, alignItems center, gap 16, padding 20px 0, cursor pointer
    Step number circle: 32px circle, 
      active: background #c5a572, color #0a0a0a
      inactive: background transparent, border 1px solid #2a2318, color #555
    Title: Cormorant Garamond 20px #f5f0e8 (active) or #8a7d6b (inactive)
    Subtitle: Outfit 13px #555 (hidden when active, shown when inactive)
    Right: chevron "›" rotated 90deg when active
  
  DETAIL PANEL (animated, visible only when active):
    maxHeight: activeStep===i ? '400px' : '0'
    overflow: hidden
    transition: max-height 0.4s ease
    
    Inner padding: 0 0 24px 48px (indented under the step circle)
    Body text: Outfit 15px #8a7d6b lineHeight 1.75
    Meta grid: 3 columns, each cell shows key (10px gold uppercase) + value (JetBrains Mono 13px #f5f0e8)

NAVIGATION DOTS below the entire step list:
  5 small circles (8px), gold when active step matches, dark otherwise
  Clicking a dot sets activeStep
```

---

## 6. `UserPages.jsx` — SINGAPORE VAULT MAP SECTION (in StoragePage)

**Current state:** Storage page lists Malca-Amit features as text/cards without a geographic visual.

**Change:** Add an SVG world map with Singapore highlighted and an animated pulse dot + route line to Korea.

**Agent instruction:**
```
In StoragePage (or add as a section at the top of the Storage page), 
insert a world map SVG visualization:

SVG SETUP:
  <svg viewBox="0 0 900 450" width="100%" style={{display:'block', maxWidth:900, margin:'0 auto'}}>
  
  BACKGROUND GRID (subtle lat/lon lines):
    Horizontal lines at y: 112, 225, 337 (stroke: rgba(197,165,114,0.06))
    Vertical lines at x: 225, 450, 675 (stroke: rgba(197,165,114,0.06))
  
  SIMPLIFIED CONTINENT OUTLINES (use basic polygon approximations):
    — These don't need to be accurate world maps. Use rough polygon shapes.
    — Fill: rgba(197,165,114,0.07), stroke: rgba(197,165,114,0.2), strokeWidth: 0.8
    
    Korea region (dot/highlight instead of outline):
      Circle at x:730, y:165, r:12
      fill: rgba(197,165,114,0.15), stroke: #c5a572, strokeWidth: 1.5
      Label: "대한민국" Outfit 11px #c5a572 above the dot
    
    Singapore (main focus):
      Circle at x:640, y:255, r:8
      fill: #c5a572, opacity 0.9
      
      PULSE RING (animated):
        <circle cx="640" cy="255" r="8" fill="none" stroke="#c5a572" strokeWidth="1.5">
          <animate attributeName="r" values="8;22;8" dur="2.5s" repeatCount="indefinite"/>
          <animate attributeName="opacity" values="0.9;0;0.9" dur="2.5s" repeatCount="indefinite"/>
        </circle>
      
      Label: "Singapore FTZ" Outfit 11px #c5a572 below dot
      Sub-label: "Malca-Amit" Outfit 9px #8a7d6b below that
    
    ROUTE LINE (Singapore → Korea):
      <path d="M 640 255 Q 690 200 730 165" 
            fill="none" stroke="rgba(197,165,114,0.4)" strokeWidth="1" 
            strokeDasharray="4 4"/>
      
      Animated dash offset for "traveling" effect:
        <animateTransform or use CSS:
          animation: dash-travel 3s linear infinite
          @keyframes dash-travel { to { stroke-dashoffset: -24; } }
  
  STATS STRIP below the map (4 items in a flex row):
    "보험 적용" / Lloyd's of London
    "감사 주기" / 매일
    "보관 방식" / 배분 보관 (혼장 없음)
    "출입 통제" / 24/7 보안

Each stat: small gold label (10px uppercase) + value (JetBrains Mono 14px #f5f0e8)
```

---

## 7. `UserPages.jsx` — COMPETITION TABLE (in WhyGold or Home)

**Current state:** Advantages over Korean alternatives are described in paragraphs or simple lists.

**Change:** Render as a proper comparison table with checkmarks and X marks.

**Agent instruction:**
```
Add or replace existing comparison content with this table component.
Can live in WhyGold page or as a section in Home (recommend Home — higher visibility).

TABLE DATA:
  Columns: ["기능", "Aurum Korea", "한국 금거래소", "KRX 금 ETF", "일반 은행 예금"]
  
  Rows:
    ["실물 소유", true, true, false, false]
    ["국제 현물가", true, false, false, false]
    ["배분 보관 (혼장 없음)", true, false, false, false]
    ["월 적립 가능 (₩20만~)", true, false, false, true]
    ["부가세 없음", true, false, false, true]
    ["해외 FTZ 보관", true, false, false, false]
    ["실물 배송 가능", true, true, false, false]
    ["금속 가격 연동", true, true, true, false]

RENDER:
  Table: width 100%, borderCollapse separate, borderSpacing 0
  
  Header row:
    background #111008
    First cell: Outfit 11px #555 uppercase "비교항목"
    "Aurum Korea" cell: background rgba(197,165,114,0.08), border-bottom 2px solid #c5a572
      Color #c5a572, fontWeight 700
    Other headers: color #8a7d6b
  
  Data rows:
    Alternating background: #0a0a0a and #0d0b08
    On hover: background rgba(197,165,114,0.03)
    
    Feature cell: Outfit 14px #f5f0e8
    
    true  → render: <span style={{color:'#4ade80',fontSize:16}}>✓</span>
    false → render: <span style={{color:'rgba(100,100,100,0.4)',fontSize:14}}>—</span>
    
    Aurum column cells: background rgba(197,165,114,0.03)
  
  Cells: padding 14px 20px, borderBottom 1px solid #1a1510, textAlign center (except first col: left)
  
  Note: replace ✓ and — with CSS shapes if emoji removal pass is applied (Section 1.4).
    ✓ → a 6px × 10px CSS rotated border-right + border-bottom in green
    — → a 20px horizontal line via background
```

---

## 8. ACCORDION FAQ COMPONENT

**Current state:** FAQ content exists but may be displayed as static text blocks.

**Change:** Convert all FAQ sections to single-open accordion with smooth expand animation.

**Agent instruction:**
```
Create a reusable FAQ accordion component:

function FaqAccordion({ items }) {
  const [open, setOpen] = useState(null);
  return (
    <div>
      {items.map((item, i) => (
        <div key={i} style={{borderBottom:'1px solid #1a1510'}}>
          <button
            onClick={() => setOpen(open === i ? null : i)}
            style={{
              width:'100%', textAlign:'left', background:'none', border:'none',
              padding:'20px 0', cursor:'pointer', display:'flex',
              justifyContent:'space-between', alignItems:'center', gap:16
            }}
          >
            <span style={{fontFamily:"'Outfit',sans-serif", fontSize:16, 
                          color: open===i ? '#c5a572' : '#f5f0e8', fontWeight: open===i ? 600 : 400}}>
              {item.q}
            </span>
            <span style={{
              color:'#c5a572', fontSize:20, flexShrink:0,
              transform: open===i ? 'rotate(45deg)' : 'none',
              transition:'transform 0.3s ease', lineHeight:1
            }}>+</span>
          </button>
          <div style={{
            maxHeight: open===i ? '400px' : '0',
            overflow:'hidden',
            transition:'max-height 0.35s ease'
          }}>
            <p style={{fontFamily:"'Outfit',sans-serif", fontSize:15, color:'#8a7d6b', 
                       lineHeight:1.75, paddingBottom:20, margin:0}}>
              {item.a}
            </p>
          </div>
        </div>
      ))}
    </div>
  );
}

FAQ ITEMS for AGP section:
  [
    { q: "월 납입 금액을 변경하거나 해지할 수 있나요?",
      a: "네. 언제든지 대시보드에서 월 납입 금액을 변경하거나 플랜을 일시 중지, 해지할 수 있습니다. 해지 시 보관 중인 실물 금속은 그대로 귀하의 소유입니다." },
    { q: "최소 가입 금액과 최대 납입 한도는 어떻게 되나요?",
      a: "최소 월 20만원부터 시작하며, 최대 한도는 없습니다. 티어별 혜택이 달라지며, 월 100만원 이상은 프리미엄 티어로 분류됩니다." },
    { q: "적립한 금은 실제로 존재하는 실물인가요?",
      a: "네. 귀하의 AGP 잔고는 싱가포르 Malca-Amit 금고에 실물로 보관된 금속에 1:1 연동됩니다. 혼장이 없으며, 매일 감사 보고서를 제공합니다." },
    { q: "한국에서 세금 신고가 필요한가요?",
      a: "현재 해외 실물 귀금속 보유는 한국 내 과세 대상이 아닙니다. 다만 인출 또는 매도 시점에 따라 양도소득세가 적용될 수 있으므로, 세무사와 상담을 권장합니다." },
    { q: "실물 배송을 요청하면 얼마나 걸리나요?",
      a: "배송 요청 후 영업일 기준 5~7일 내에 한국 주소로 배송됩니다. 배송비 및 보험료는 주문 시 안내됩니다." }
  ]

Use this component in: AGP page, AGPIntroPage (bottom), and any existing FAQ sections.
```

---

## 9. `index.css` — TYPOGRAPHY & SPACING PASS

**Agent instruction:**
```
Add or update the following global CSS rules in index.css:

/* ── Section spacing rhythm ── */
section, .page-section {
  padding: 80px 80px;
}
@media (max-width: 768px) {
  section, .page-section {
    padding: 40px 16px;
  }
}

/* ── H2 section headers ── */
h2.section-title {
  font-family: 'Cormorant Garamond', serif;
  font-size: clamp(32px, 5vw, 52px);
  font-weight: 300;
  color: #f5f0e8;
  line-height: 1.15;
  margin: 0 0 12px;
}

/* ── Section eyebrow labels ── */
.eyebrow {
  font-family: 'Outfit', sans-serif;
  font-size: 10px;
  color: #c5a572;
  letter-spacing: 4px;
  text-transform: uppercase;
  margin-bottom: 12px;
  display: block;
}

/* ── Body text ── */
.body-text {
  font-family: 'Outfit', sans-serif;
  font-size: 15px;
  color: #8a7d6b;
  line-height: 1.75;
  max-width: 640px;
}

/* ── JetBrains data values ── */
.data-value {
  font-family: 'JetBrains Mono', monospace;
  font-size: 13px;
  color: #f5f0e8;
}

/* ── Gold gradient button ── */
.btn-gold {
  background: linear-gradient(135deg, #c5a572, #8a6914);
  color: #ffffff;
  border: none;
  padding: 14px 28px;
  font-family: 'Outfit', sans-serif;
  font-weight: 700;
  font-size: 14px;
  letter-spacing: 0.5px;
  border-radius: 4px;
  cursor: pointer;
  transition: opacity 0.2s ease;
}
.btn-gold:hover { opacity: 0.88; }

/* ── Ghost button ── */
.btn-ghost {
  background: transparent;
  color: #8a7d6b;
  border: 1px solid #2a2318;
  padding: 14px 28px;
  font-family: 'Outfit', sans-serif;
  font-size: 14px;
  border-radius: 4px;
  cursor: pointer;
  transition: border-color 0.2s ease, color 0.2s ease;
}
.btn-ghost:hover {
  border-color: rgba(197,165,114,0.4);
  color: #c5a572;
}

/* ── Card base ── */
.card {
  background: #111008;
  border: 1px solid #1a1510;
  border-radius: 12px;
  transition: border-color 0.25s ease, transform 0.25s ease;
}
.card:hover {
  border-color: rgba(197,165,114,0.35);
  transform: translateY(-3px);
}

/* ── Grain overlay (keep from aurum-motion.css) ── */
body::before {
  content: '';
  position: fixed;
  inset: 0;
  pointer-events: none;
  z-index: 9998;
  opacity: 0.025;
  background-image: url("data:image/svg+xml,%3Csvg viewBox='0 0 256 256' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='n'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.9' numOctaves='4' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23n)'/%3E%3C/svg%3E");
  background-size: 200px 200px;
}
```

---

## 10. CONTENT CONSOLIDATION RULES (Progressive Disclosure)

Apply these rules across ALL pages where you see long blocks of text or stacked information panels.

### 10.1 When to Use Tabs
```
Use tabs when content covers the same "topic" from different angles.
Example: Shop page — Physical products vs AGP products (same shop, two modes)
Example: WhyGold page — Gold stats vs Silver stats

Implementation:
  const [tab, setTab] = useState('gold');
  
  Tab bar: pills style, gap 8px
    active: background #c5a572, color #0a0a0a
    inactive: background transparent, color #8a7d6b, border 1px solid #2a2318
  
  Content: render only active tab's content (conditional rendering, not hidden with CSS)
```

### 10.2 When to Use Accordions
```
Use accordions when content is a list of Q&A, specifications, or sub-items
that users won't need all at once.
Examples:
  - All FAQ sections → FaqAccordion component (Section 8)
  - Product spec tables on ProductPage → collapsible "상세 제원" section
  - AGP tier comparison details → collapsible tier rows
  - KYC step explanations
```

### 10.3 When to Use Step Panels
```
Use numbered step panels (like Section 5) when content is a sequential process.
Examples:
  - AGPIntroPage 5 steps (already done in Section 5)
  - CheckoutPage 3 steps
  - KYC verification steps
  
  Rule: Show one step expanded at a time. Auto-advance to next step on completion.
```

### 10.4 Paragraph Tightening Rules
```
Apply to ALL body text across the site:
  - Max 3-4 lines per paragraph
  - If a paragraph has 4+ clauses separated by commas → convert to 3-item bullet list
  - Lead with the most important fact (inverted pyramid)
  - Remove filler phrases: "이를 통해", "따라서", "이러한 이유로", "앞서 말씀드린 바와 같이"
  - Bold the key claim in each paragraph (1 bold phrase per paragraph maximum)
```

---

## 11. MOBILE RESPONSIVENESS PASS

**Agent instruction for all components:**
```
Audit every component for these mobile issues and fix:

1. FONT SIZES: 
   Any fontSize > 48px on desktop → clamp(28px, 6vw, [desktop size]) 
   Any fontSize > 16px on body text → keep at 15px mobile

2. PADDING:
   Desktop padding 80px → mobile 16px horizontal, 40px vertical
   Pattern: padding: isMobile ? '40px 16px' : '80px'

3. GRID COLUMNS:
   Any 3-column grid → single column on mobile (gridTemplateColumns: isMobile ? '1fr' : 'repeat(3,1fr)')
   Any 2-column split → stacked on mobile

4. HERO:
   Hero sections: minHeight isMobile ? '100dvh' : '90vh'
   Price comparison card: hide on mobile (display: isMobile ? 'none' : 'block')

5. NAV:
   Ensure hamburger menu is functional on mobile
   Menu opens as full-screen overlay with:
     position fixed, inset 0, background rgba(10,10,10,0.97), zIndex 9999
     Links centered, large (20px), spaced 28px apart

6. TABLE RESPONSIVENESS:
   Competition table on mobile → horizontal scroll wrapper:
     <div style={{overflowX: 'auto', WebkitOverflowScrolling: 'touch'}}>
       [table]
     </div>
```

---

## 12. BACK TO TOP BUTTON

**Add to root App component:**
```javascript
const [showTop, setShowTop] = useState(false);

useEffect(() => {
  const fn = () => setShowTop(window.scrollY > 400);
  window.addEventListener('scroll', fn);
  return () => window.removeEventListener('scroll', fn);
}, []);

// In render:
{showTop && (
  <button
    onClick={() => window.scrollTo({ top: 0, behavior: 'smooth' })}
    style={{
      position: 'fixed', bottom: 32, right: 24, zIndex: 999,
      width: 44, height: 44, borderRadius: '50%',
      background: 'rgba(197,165,114,0.15)',
      border: '1px solid rgba(197,165,114,0.4)',
      color: '#c5a572', fontSize: 18, cursor: 'pointer',
      display: 'flex', alignItems: 'center', justifyContent: 'center',
      backdropFilter: 'blur(8px)',
      transition: 'opacity 0.3s ease',
    }}
  >
    ↑
  </button>
)}
```

---

## 13. IMPLEMENTATION ORDER FOR WEB AGENT

Execute in this sequence to avoid dependency issues:

1. **`index.css`** — Add all global utility classes (Section 9) first
2. **`App.jsx`** — Add scroll progress bar, reveal observer, back-to-top (Sections 1.2, 1.3, 12)
3. **`BaseUI.jsx`** — Ticker marquee, nav scroll-aware (Sections 2.1, 2.2, 2.3)
4. **`ShopPages.jsx` Home** — Kimchi Premium section, 3 Pillars, metrics strip, savings calculator (Sections 3.3–3.6)
5. **`ShopPages.jsx` Home** — Price comparison card, live tick (Sections 3.1–3.2)
6. **`ShopPages.jsx` Shop** — Tab filter (Section 4.1)
7. **`ShopPages.jsx` ProductPage** — SVG product illustration (Section 4.2)
8. **`AGPIntroPage.jsx`** — Step panel redesign (Section 5)
9. **`UserPages.jsx` StoragePage** — Vault map SVG (Section 6)
10. **Add FaqAccordion** component, replace all FAQ sections (Section 8)
11. **Add competition table** to Home or WhyGold (Section 7)
12. **Remove all emoji** from UI (Section 1.4)
13. **Mobile pass** on all modified files (Section 11)
14. **`.reveal` class** — Add to all section wrappers (Section 1.3 usage)

---

## 14. BRAND TOKENS REFERENCE

Always use these values. Do not introduce new colors.

```
BACKGROUNDS:
  Primary:   #0a0a0a
  Elevated:  #111008
  Deeper:    #0d0b08
  Darkest:   #090807

GOLD PALETTE:
  Main:      #c5a572
  Light:     #e8d5a8
  Dark:      #8a6914
  Gradient:  linear-gradient(135deg, #c5a572, #8a6914)
  Subtle bg: rgba(197,165,114,0.04)
  Border:    rgba(197,165,114,0.15)  /  rgba(197,165,114,0.4)

TEXT:
  Primary:   #f5f0e8
  Secondary: #8a7d6b
  Muted:     #555555
  Dimmed:    rgba(100,100,100,0.4)

BORDERS:
  Default:   #1a1510
  Active:    rgba(197,165,114,0.4)

STATUS:
  Green:     #4ade80  (savings, positive, success)
  Red:       #f87171  (Korean premium, error, negative)
  Blue:      #60a5fa  (info, processing)
  Amber:     #f59e0b  (pending, warning)

FONTS:
  Serif/display:  'Cormorant Garamond', serif  → headlines, large numbers
  Sans/body:      'Outfit', sans-serif          → all UI text, labels, body
  Mono/data:      'JetBrains Mono', monospace   → prices, IDs, technical values
  Korean:         'Noto Sans KR', sans-serif    → Korean body text (supplement Outfit)
  
  Import string (already in index.css — verify it includes all four):
  @import url('https://fonts.googleapis.com/css2?family=Cormorant+Garamond:wght@300;400;600&family=Outfit:wght@300;400;600;700&family=JetBrains+Mono:wght@400;600&family=Noto+Sans+KR:wght@300;400;500&display=swap');
```

---

*End of handoff document. Total changes: 14 sections covering architecture, 8 component files, global CSS, and brand governance.*
