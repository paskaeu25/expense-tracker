# Handoff: Category Sync via JSON Export/Import

**From:** Greg, Business Analyst  
**To:** Johnson, Developer  
**Date:** 2026-07-06  
**Priority:** Medium  

---

## Summary

Currently, JSON export only includes expenses — custom categories added on one device are **lost** when importing on another device. Expenses referencing those categories still render with a grey fallback color and aren't filterable properly.

This spec extends the export/import flow to include the categories array so that custom categories and their colors sync across devices.

---

## Current Behavior (the gap)

- **Export** → `JSON.stringify(state.expenses, null, 2)` — categories array is not included
- **Import** → reads a JSON array of expenses only; validates `amount`, `date`, `category`; appends to `state.expenses`; does **not** touch `state.categories`
- Result: Custom category added on phone → export → import on computer → category name exists in expenses but is absent from the global category list → renders grey, can't re-select in dropdown

---

## Requirements

### R1 — Export includes categories

The exported JSON file should be an object containing both the expenses and the categories:

```json
{
  "version": 1,
  "expenses": [ ... ],
  "categories": [ ... ]
}
```

- `"version"` field allows future format evolution
- `"categories"` must be a full snapshot of `state.categories` at export time
- Backward compatibility: new export format replaces the old flat-array format

### R2 — Import handles the new format

The import function must:

1. **Detect the format**: if the top-level JSON is an **object** with an `"expenses"` key, treat it as the new format; if it's a plain **array**, treat it as the legacy format (backward compatible)
2. **Merge categories**: for each category in the imported data:
   - If the category name **does not exist** in `state.categories` → **add it** (name + color)
   - If the category name **already exists** in `state.categories` → **overwrite the color** with the imported value
3. **Merge expenses**: same as today — append all valid expenses to `state.expenses`
4. **Re-validate**: expenses whose `category` string doesn't match any (merged) category should still be accepted (defensive), but we should warn the user

### R3 — Backward compatibility with legacy export files

Users who previously exported a flat array of expenses should still be able to import those files. The handler must detect the difference and process accordingly.

### R4 — No breaking changes to CSV export

CSV export remains unchanged (it doesn't include categories, and that's fine for its spreadsheet use-case).

---

## Acceptance Criteria

| # | Scenario | Expected Result |
|---|----------|----------------|
| AC1 | Export with default categories only | File contains `{"version":1,"expenses":[...],"categories":[...]}` with all 9 defaults |
| AC2 | Export after adding a custom category | Custom category is present in the `categories` array of the export |
| AC3 | Import new-format file on another device | Custom categories from the file are added to `state.categories`; expenses with those categories render with correct color |
| AC4 | Import file where category name already exists locally | The local category's **color is overwritten** by the imported value |
| AC5 | Import legacy flat-array file | No crash; expenses are appended; categories are **not** touched (preserves backward compat) |
| AC6 | Import file with invalid entries | Same as today — valid entries are imported, errors are reported |
| AC7 | Export → Clear All → Re-import same file | Everything is restored (since clear resets categories to defaults, and import adds custom ones back) |

---

## Edge Cases

- **Category deleted locally, but exists in import** → re-added (covered by R2 merge logic)
- **Import file has no `categories` key but is an object** → import expenses only, leave categories untouched
- **Import file has an empty `categories` array** → no categories added (harmless)
- **Category name collision with different case** ("Groceries" vs "groceries") → treat as the same name (case-insensitive match, keep the local casing)
- **Color in import is missing or invalid** → fall back to default grey `#8e8e93`
- **Multiple imports from the same phone** → categories are re-merged each time; no duplicates (matched by name)

---

## Technical Approach

### Export change (~5 lines)

In the `btn-export-data` click handler, replace:
```js
const blob = new Blob([JSON.stringify(state.expenses, null, 2)], ...);
```
with:
```js
const exportData = {
  version: 1,
  expenses: state.expenses,
  categories: state.categories
};
const blob = new Blob([JSON.stringify(exportData, null, 2)], ...);
```

### Import change (~20 lines)

In `handleImport()`, after `JSON.parse(e.target.result)`:

```js
let data = JSON.parse(e.target.result);
let expensesToImport = data;
let categoriesToImport = null;

// Detect format
if (data && typeof data === 'object' && !Array.isArray(data)) {
  // New format — object with expenses/categories keys
  expensesToImport = data.expenses || [];
  categoriesToImport = data.categories || null;
}
// else: legacy format — data is already the expenses array, handled below

// Merge categories first
if (categoriesToImport && Array.isArray(categoriesToImport)) {
  categoriesToImport.forEach(importedCat => {
    if (!importedCat || !importedCat.name) return;
    const existing = state.categories.find(
      c => c.name.toLowerCase() === importedCat.name.toLowerCase()
    );
    if (existing) {
      // Overwrite color only
      existing.color = importedCat.color || '#8e8e93';
    } else {
      // Add new category
      state.categories.push({
        name: importedCat.name,
        color: importedCat.color || '#8e8e93'
      });
    }
  });
  save(); // persist merged categories before saving expenses
}
```

Continue with existing expense validation and append logic.

---

## Files to modify

| File | Change |
|------|--------|
| `app/index.html` | Modify export handler (lines ~956-963) |
| `app/index.html` | Modify `handleImport()` function (lines ~900-923) |

Both are in the same single file — no other files affected.

---

## Out of scope

- CSV export changes
- Cloud sync / auto-sync
- UI for resolving category conflicts interactively
- Multi-device merge conflicts (if two devices add different categories with the same name)
