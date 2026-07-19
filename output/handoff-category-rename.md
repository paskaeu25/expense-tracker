# Handoff: Category Updates (Groceries + Travel)

**From**: Greg, Business Analyst  
**To**: Johnson, Developer  
**Date**: 2026-07-05  

---

## Summary

Two changes needed:
1. Rename "Food" → "Groceries" and add "Eating Out" as default (code already done, export needs update)
2. Add a new "Travel" default category

---

## Part 1 — Already done (code pushed)

`app/index.html` — `DEFAULT_CATEGORIES` updated to:

```js
{ name: 'Groceries', color: '#ff9500' },
{ name: 'Eating Out', color: '#ff6b35' },
{ name: 'Transport', color: '#007aff' },
{ name: 'Utilities', color: '#5856d6' },
{ name: 'Entertainment', color: '#ff2d55' },
{ name: 'Health', color: '#34c759' },
{ name: 'Shopping', color: '#af52de' },
{ name: 'Other', color: '#8e8e93' },
```

Missing "Travel" — needs to be added (see Part 2).

**Export file** — `inputs/expense-tracker-data-2.json` needs `"Food"` → `"Groceries"` renamed:

```bash
sed -i '' 's/"category": "Food"/"category": "Groceries"/g' inputs/expense-tracker-data-2.json
```

---

## Part 2 — Add "Travel" default category

### Code change

In `app/index.html`, in the `DEFAULT_CATEGORIES` array, insert **Travel** between "Shopping" and "Other" (or after Transport — your call on ordering):

```js
{ name: 'Travel', color: '#ff3b30' },
```

Suggested color: `#ff3b30` (red) — matches the red/green spend indicators theme. Alternatively `#00c7be` (teal) for a distinct travel vibe.

The full array should look like:

```js
const DEFAULT_CATEGORIES = [
  { name: 'Groceries', color: '#ff9500' },
  { name: 'Eating Out', color: '#ff6b35' },
  { name: 'Transport', color: '#007aff' },
  { name: 'Utilities', color: '#5856d6' },
  { name: 'Entertainment', color: '#ff2d55' },
  { name: 'Health', color: '#34c759' },
  { name: 'Shopping', color: '#af52de' },
  { name: 'Travel', color: '#ff3b30' },
  { name: 'Other', color: '#8e8e93' },
];
```

### Export file

The user's export (`inputs/expense-tracker-data-2.json`) doesn't have any "Travel" entries yet since it's new — no changes needed there for Travel.

---

## Files to modify

| File | Change |
|------|--------|
| `app/index.html` | Add `{ name: 'Travel', color: '#ff3b30' }` to `DEFAULT_CATEGORIES` |
| `inputs/expense-tracker-data-2.json` | `sed` replace `"category": "Food"` → `"category": "Groceries"` |

---

## Re-import instructions for user

1. Open the app in browser
2. **Import** tab → **Clear All Data**
3. Refresh the page
4. **Import** tab → choose the modified `expense-tracker-data-2.json` → click **Import**
