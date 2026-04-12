# CFO Domain Notes
> Last updated: 2026-04-12

## Known Issue: Excel Zero-Output Bug
- **Problem**: CFO agent produces correct chat summary but Excel analysis cells = 0
- **Root cause**: Formula cell references don't match where data was placed (layout shift)
- **Contributing**: recalc.py doesn't detect valid-but-wrong zeros; recalc step may be skipped
- **Fix**: Add mandatory post-generation verification (read back with data_only=True, assert key cells ≠ 0)
- **Status**: Fix proposed, not yet applied to CFO prompt

## Live Data Sources
- Gold spot: GoldAPI (env: GOLD_API_KEY) or JM Bullion scrape as fallback
- FX rates: open.er-api.com (free, no key)
- Korean retail: web search (한국금거래소, 금시세닷컴, DailyLifeData)
