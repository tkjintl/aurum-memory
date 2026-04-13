# Open Items
> Last updated: 2026-04-13

## Active

- [2026-04-13] Website Agent — **Storage page 13-section upgrade**: UserPages.jsx Storage component is still the old thin version (6-tile grid + Malca-Amit description + fee table). The new 13-section expanded Storage page (from Brand Manager brief `20260413_brand_storage_page_content_v1.docx`) needs to replace it. The content for the expanded Storage also exists in what was accidentally uploaded as ShopPages.jsx — it has: BenefitTile, SectionLabel helpers, and the full 13-section Storage function. This is a follow-up polish task; build is working without it.

## Completed
- [x] Memory repo initialized (2026-04-12)
- [x] CFO xlsx failure root cause diagnosed (2026-04-12)
- [x] GC agent memory access confirmed (2026-04-12)
- [x] All 6 agents memory access verified (2026-04-12)
- [x] GC prompt updated: bash+git → GitHub MCP, fixed stale `memories/` path prefix
- [x] CFO xlsx verification gate tested and proven (2026-04-12) — cell-map pattern + float casting + post-generation assertion. All 7 key cells verified non-zero.
- [x] Build failures fixed (2026-04-13) — 8 consecutive ERROR deployments resolved. AGP merged into UserPages.jsx. ShopPages.jsx restored. App.jsx fixed. Site live at dpl_FGrF9hyFp7pohHY9sbpGqJ5EtUHA.
