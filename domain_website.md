# Website Domain Notes
> Last updated: 2026-04-12

## Architecture
- **Stack**: React 19 + Vite 8 (NOT Next.js despite earlier context)
- **Styling**: 100% inline React styles + global `index.css` (no Tailwind)
- **Hosting**: Vercel at `aurum-website-nu.vercel.app`
- **Repo**: `github.com/tkjintl/aurum-website`

## File Structure & Sizes
| File | Size | Purpose |
|------|------|---------|
| `src/index.css` | 7.2KB | Global styles, hover states, transitions, accessibility |
| `src/lib.jsx` | 33KB | Hooks, API stubs, products, mock data, formatters |
| `src/BaseUI.jsx` | 26KB | Toast, Ticker, Nav, LoginModal, NewsSection |
| `src/App.jsx` | 43KB | Dashboard, SellFlow, WithdrawFlow, Footer, Root App |
| `src/ShopPages.jsx` | 57KB | Home, Shop, ProductPage, CartPage, Checkout |
| `src/UserPages.jsx` | 56KB | OrderHistory, Account, KYC, WhyGold, Storage, Learn |

## Push Workflow (IMPORTANT)
### What works via GitHub MCP:
- Files **<10KB**: `create_or_update_file` with SHA → reliable
- `index.css` is the primary push target for visual changes
- CSS `!important` can override inline React styles for layout/spacing changes
- Attribute selectors like `button[style*="linear-gradient"]` target specific inline-styled elements

### What needs manual upload:
- JSX files (26-57KB each) — too large for reliable MCP push
- If JSX changes are needed: agent outputs to `/mnt/session/outputs/` → founder uploads via GitHub UI "Edit file"

### The CSS-Only Strategy (preferred approach):
Instead of editing 43-57KB JSX files to change visual styles:
1. Add CSS rules to `index.css` using `!important` + structural selectors
2. This overrides inline React styles without touching JSX
3. Keep `index.css` small and pushable via MCP
4. Reserve JSX edits for functional/logic changes only

## CSS Selectors That Target Inline Styles
```css
button[style*="linear-gradient"]     /* Gold CTA buttons */
button[style*="border: 1px solid"]   /* Ghost/outline buttons */
div[style*="#111008"]                 /* Dark card containers */
div[style*="cursor: pointer"]        /* Clickable elements */
div[style*="linear-gradient(90deg"]  /* Ticker bar */
footer > div:first-child             /* Footer grid */
```

## Responsive Breakpoints
- `useIsMobile()` hook: binary switch at 768px
- No tablet-specific logic in JSX (addressed via CSS media queries)
- CSS handles: <375px (tiny phone), <440px (ticker scroll), <767px (footer grid)
