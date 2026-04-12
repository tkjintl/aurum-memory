# CFO Domain Notes
> Last updated: 2026-04-12

## XLSX Output Pattern (MANDATORY)
Every Excel output MUST follow this pattern:

### 1. Cell-Map Dict
```python
S1 = {'spot_oz': 'C4', 'fx_krw': 'C5', ...}  # input sheet
S2 = {'spot_gram': 'C4', ...}                  # analysis sheet
```
All data writes AND formulas reference these dicts. Never hardcode cell addresses in formulas separately.

### 2. Explicit Float Casting
All API-sourced values: `float(response['price'])` before writing to cells.

### 3. Post-Generation Verification Gate
After recalc, read back with `data_only=True` and assert key output cells ≠ 0:
```python
wb = load_workbook('output.xlsx', data_only=True)
for cell in critical_cells:
    assert ws[cell].value not in (None, 0), f"FAIL: {cell}"
```
If any key cell = 0 or None → DO NOT DELIVER. Debug cell references first.

## Known Issue (RESOLVED)
- **Problem**: CFO agent produced correct chat summary but Excel analysis cells = 0
- **Root cause**: Formula cell references didn't match where data was placed (layout shift)
- **Contributing**: recalc.py doesn't detect valid-but-wrong zeros; recalc step was skipped
- **Fix**: Cell-map pattern + float casting + post-generation verification gate
- **Status**: ✅ FIXED and verified (2026-04-12)

## Live Data Sources
- Gold spot: GoldAPI (env: GOLD_API_KEY) or USA Today/JM Bullion scrape as fallback
- FX rates: open.er-api.com (free, no key)
- Korean retail: web search (중앙금거래소, 한국금거래소, 한국표준금거래소, 금시세닷컴)
