# Open Items
> Last updated: 2026-04-13

## Active

- [2026-04-13] Website Agent — **UserPages.jsx manual upload required**: Combined UserPages.jsx (128KB — full 13-section Storage + AGP + AGPBackingReport + all 8 correct exports) built and validated. Too large for MCP push. Download from tmpfiles (60min): https://tmpfiles.org/dl/33435151/userpages.jsx — Founder must upload manually via GitHub UI to `src/UserPages.jsx`. This fixes the current build failure (missing Storage/AGP/AGPBackingReport exports).

- [2026-04-13] Website Agent — **StoragePage.jsx deleted**: ✅ Done (commit 31cb11e). Rogue standalone file removed.

## Completed
- [x] Memory repo initialized (2026-04-12)
- [x] CFO xlsx failure root cause diagnosed (2026-04-12)
- [x] GC agent memory access confirmed (2026-04-12)
- [x] All 6 agents memory access verified (2026-04-12)
- [x] GC prompt updated: bash+git → GitHub MCP, fixed stale `memories/` path prefix
- [x] CFO xlsx verification gate tested and proven (2026-04-12) — cell-map pattern + float casting + post-generation assertion. All 7 key cells verified non-zero.
- [x] Build failures fixed (2026-04-13) — 8 consecutive ERROR deployments resolved. AGP merged into UserPages.jsx. ShopPages.jsx restored. App.jsx fixed. Site live at dpl_FGrF9hyFp7pohHY9sbpGqJ5EtUHA.
- [x] Storage page 13-section upgrade content built and validated (2026-04-13) — merged into new UserPages.jsx. Pending manual upload by founder.
