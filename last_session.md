## 2026-04-14 16:30 UTC — Website Agent

**Request:** Implement all 23 changes from 20260414_marketing_website_revision_handoff_v1.docx
**Outcome:** ✅ All 23 changes coded + build verified. index.css (Phase 6) pushed to GitHub. 3 JSX files delivered via tmpfiles for manual upload (GitHub MCP rate-limited on large files).

**Files generated:**
- UserPages.jsx (125KB) — tmpfiles: http://tmpfiles.org/dl/33592245/userpages.jsx
- ShopPages.jsx (79KB) — tmpfiles: http://tmpfiles.org/dl/33592246/shoppages.jsx
- lib.jsx (34KB) — tmpfiles: http://tmpfiles.org/dl/33592248/lib.jsx
- index.css (14KB) — tmpfiles: http://tmpfiles.org/dl/33592250/index.css — ✅ ALSO PUSHED to GitHub (commit ef0c60e)

**Decisions recorded in aurum_context.md:**
- 20만원 is canonical AGP minimum (all 10만원 refs replaced)

**Open items added:**
- Manual upload: src/lib.jsx (GitHub push was truncated — 6KB not 34KB) → https://github.com/tkjintl/aurum-website/edit/main/src/lib.jsx
- Manual upload: src/ShopPages.jsx → https://github.com/tkjintl/aurum-website/edit/main/src/ShopPages.jsx
- Manual upload: src/UserPages.jsx → https://github.com/tkjintl/aurum-website/edit/main/src/UserPages.jsx

**Cross-domain notes:**
- 20만원 canonical confirmed → Marketing/CFO agents should align on this figure
- KB Star URLs added as price data sources: gold https://obank.kbstar.com/quics?page=C029415, silver https://obank.kbstar.com/quics?page=C039201
- BullionStar removed from news feeds (competitor); TossBank added as static resource card

---

## REPO STATE (2026-04-14 16:30 UTC):
- `src/index.css` — ✅ 14KB, Phase 1+2+4+5+6 (Phase 6 = handoff v1: H-01 font floor, S-01/02 grids, A-01/02/04/10 AGP layout) — LIVE on GitHub
- `src/lib.jsx` — ⚠️ NEEDS MANUAL UPLOAD (pushed version truncated at 6KB, full is 34KB) → tmpfiles: http://tmpfiles.org/dl/33592248/lib.jsx
- `src/ShopPages.jsx` — ⚠️ NEEDS MANUAL UPLOAD → tmpfiles: http://tmpfiles.org/dl/33592246/shoppages.jsx
- `src/UserPages.jsx` — ⚠️ NEEDS MANUAL UPLOAD (unchanged on GitHub, 128KB old version) → tmpfiles: http://tmpfiles.org/dl/33592245/userpages.jsx
- `src/BaseUI.jsx` — unchanged (H-03 news changes are in lib.jsx STATIC_NEWS, not BaseUI)

## CHANGES IMPLEMENTED:
- H-01: Global font floor (CSS) — index.css ✅
- H-02: 3→2 CTA buttons (duplicate shop btn removed) — ShopPages.jsx
- H-03: BullionStar removed, TossBank added to news — lib.jsx
- H-04: Gold tracker 1온스→1돈 (KB Star source) — ShopPages.jsx
- H-05: Silver 1kg tracker new right panel — ShopPages.jsx
- H-06: Premium caption below both trackers — ShopPages.jsx
- W-01: 환율 위험 분산 stat added (+394%) — lib.jsx
- W-02: (포트폴리오 분산 stat already present)
- W-03: 왜 은인가 heading 36px bold — UserPages.jsx
- S-01: Storage 2×3 grid (CSS) — index.css ✅
- S-02: Ownership 2-column (CSS) — index.css ✅
- S-03: Storage subtext size (CSS H-01) — index.css ✅
- S-04: 10만원→20만원 — UserPages.jsx
- A-01: Step bars equal sizing (CSS) — index.css ✅
- A-02: Feature boxes 2×3 grid (CSS) — index.css ✅
- A-03: Flag images — already using emoji 🇸🇬🇰🇷, no broken img tags found
- A-04: AGP font sizes (CSS H-01) — index.css ✅
- A-05: 민준씨→고객님 — UserPages.jsx
- A-06: Month 1→1개월, Korean month labels — UserPages.jsx
- A-07: NH+Toss column removed from comparison table — UserPages.jsx
- A-08: 기준점 split lines + 1kg LBMA 바 — UserPages.jsx
- A-09: 10만원→20만원 AGP — UserPages.jsx
- A-10: AGP buttons larger/centered (CSS) — index.css ✅

---

## 2026-04-14 15:30 UTC — Marketing Agent

**Request:** Read asdfdsf.pdf from memory repo (founder's website markup notes) and synthesize into a clean handoff document for the Website Agent
**Outcome:** ✅ 23-change Website Revision Handoff DOCX delivered — covers all 11 pages of founder markup across Home, Why Gold, Storage, and AGP pages. Phased implementation plan included (CSS-only Phase 1 / JSX Phase 2).

**Files generated:**
- files/marketing/20260414_marketing_website_revision_handoff_v1.docx — Website Agent handoff: 23 changes (H-01 to H-06, W-01 to W-03, S-01 to S-04, A-01 to A-10) with OLD/NEW tables, implementation types, and data source specs
  (tmpfiles: https://tmpfiles.org/dl/33584102/20260414_marketing_website_revision_handoff_v1.docx)
  (⚠️ GitHub archive has truncated content — base64 too long for MCP tool parameter. Re-archive needed.)

**Decisions recorded in aurum_context.md:**
- none

**Open items added:**
- Re-archive 20260414_marketing_website_revision_handoff_v1.docx to GitHub (base64 truncation on first commit)
- Website Agent: deliver all 23 changes per the handoff DOCX

**Cross-domain notes:**
- Full website revision spec now exists for Website Agent. Key items: 10만원 → 20만원 everywhere (canonical number), silver tracker new (right panel), KB Star data sources, flag logos broken in AGP, personal name 민준씨 → 고객님.

---

## 2026-04-13 18:20 UTC — Website Agent

**Request:** (1) Fix login — any email/password should work, test account wsl@aurum.com/1234; (2) Fix colours — Phase 3 CSS was broken (hex selectors don't match browser-normalized RGB inline styles); (3) Fix desktop sizing — too small, ~115% zoom needed.
**Outcome:** ✅ index.css Phase 4 pushed (commit 63a4053) — hex+RGB selectors, desktop zoom 1.15×. BaseUI.jsx delivered via tmpfiles (fixes broken login: API was never imported). Manual upload required for BaseUI.jsx.

**Files generated:**
- files/website/20260413_website_BaseUI_demoauth_v1.jsx — stub archive; full file at tmpfiles https://tmpfiles.org/dl/33438262/baseui.jsx (60min)

**Decisions recorded in aurum_context.md:**
- none

**Open items added:**
- BaseUI.jsx manual upload required: download https://tmpfiles.org/dl/33438262/baseui.jsx → upload to src/BaseUI.jsx via GitHub UI. This fixes login (was completely broken — API never imported).

**Cross-domain notes:**
- none

---

## PUSH LIMITATION NOTE:
JSX files >10KB cannot be pushed via GitHub MCP reliably. Always use tmpfiles + manual GitHub UI upload for JSX changes. index.css is always MCP-pushable (<14KB).
