# Spec: Month Picker Filter (Replace Date Range)

**Date**: 2026-07-05
**Author**: Greg, Senior Business Analyst
**Status**: Draft

---

## 1. Overview

The current filter bar uses two date pickers (from/to) for date range filtering. This is cumbersome — the user wants to pick a single month and see expenses for that month only, matching the Dashboard's monthly view paradigm.

---

## 2. User Story

**US-MF1: Filter expenses by month**
> As a user, I want to select a month from a simple dropdown or month picker so I can see all expenses for that specific month without fiddling with two date inputs.

---

## 3. Acceptance Criteria

| ID | Criterion | Priority |
|----|-----------|----------|
| MF1 | Replace the "From" and "To" date inputs with a single month/year selector | P0 |
| MF2 | The month selector uses a simple `<input type="month">` (renders as `YYYY-MM` in browsers that support it, or a text field as fallback) | P0 |
| MF3 | Selecting a month immediately filters the expense list to only that month | P0 |
| MF4 | When no month is selected (empty value), no date filtering is applied — all months show | P0 |
| MF5 | The combined filters (keyword + category + month) still work together | P0 |
| MF6 | The "Clear" button resets the month filter along with other filters | P1 |
| MF7 | The existing monthly grouping in the expenses list is preserved (naturally, since filtering by month means all results are in one group) | P1 |

---

## 4. UI/UX Design

### 4.1 Before (current)

```
[Search notes...] [All categories ▾] [2026-07-04] — [2026-07-20] [Clear]
```

### 4.2 After

```
[Search notes...] [All categories ▾] [2026-07 ▾] [Clear]
```

### 4.3 Behaviour

1. User types in search field → filters by keyword (as before)
2. User picks a category → filters by category (as before)
3. User picks a month (e.g. `2026-07`) → list shows only July 2026 expenses
4. User clears the month → all months visible again
5. All three filters combine — e.g. "July, Food category, contains 'dinner'"

---

## 5. Technical Approach

### 5.1 What to change

**HTML** — Replace the two date inputs and the separator with a single `<input type="month">`:

```html
<!-- Before -->
<input type="date" class="filter-input filter-date" id="filter-date-from" title="From date">
<span class="filter-sep">—</span>
<input type="date" class="filter-input filter-date" id="filter-date-to" title="To date">

<!-- After -->
<input type="month" class="filter-input" id="filter-month" placeholder="yyyy-mm">
```

**CSS** — The `.filter-input[type="date"]` specific width rule can be kept or simplified:

```css
.filter-input[type="month"] { width: 150px; }
```

Remove the `.filter-sep` rule if no longer needed elsewhere (check if used anywhere else — likely not).

**JS** — Replace the date-from/date-to filter logic with month-based filtering in `renderAllExpenses()`:

```javascript
// Before (date range logic):
if (dateFrom) {
  filtered = filtered.filter(e => e.date >= dateFrom);
}
if (dateTo) {
  filtered = filtered.filter(e => e.date <= dateTo);
}

// After (month logic):
if (monthFilter) {
  filtered = filtered.filter(e => e.date.startsWith(monthFilter));
}
```

**Event listeners** — Replace the from/to listeners with the month listener:

```javascript
// Remove:
document.getElementById('filter-date-from').addEventListener('change', renderAllExpenses);
document.getElementById('filter-date-to').addEventListener('change', renderAllExpenses);

// Add:
document.getElementById('filter-month').addEventListener('change', renderAllExpenses);
```

**clearFilters()** — Replace date-from/date-to reset with month reset:

```javascript
// Before:
document.getElementById('filter-date-from').value = '';
document.getElementById('filter-date-to').value = '';

// After:
document.getElementById('filter-month').value = '';
```

**renderAllExpenses()** — Replace the date-from/date-to read:

```javascript
// Before:
const dateFrom = document.getElementById('filter-date-from').value;
const dateTo = document.getElementById('filter-date-to').value;

// After:
const monthFilter = document.getElementById('filter-month').value;
```

### 5.2 `input type="month"` browser support

`<input type="month">` is supported in all modern browsers (Chrome, Firefox, Safari, Edge). In unsupported browsers it falls back to a plain text input where the user can type `YYYY-MM`. This is acceptable.

### 5.3 Edge cases

- **Empty month**: no date filtering, all months shown
- **Invalid month string**: the native month picker always outputs `YYYY-MM` format, matching our `monthKey()` helper and `e.date.slice(0, 7)` pattern exactly
- **Month with no expenses**: shows "No expenses match your filters" (existing empty state)

---

## 6. Files to modify

| File | What to change |
|------|----------------|
| `app/index.html` | Replace the two date inputs + separator with one `<input type="month">` |
| `app/index.html` | Update CSS: remove `.filter-input[type="date"]`, add `.filter-input[type="month"]`, remove `.filter-sep` if unused |
| `app/index.html` | Update JS: `clearFilters()`, `renderAllExpenses()` filter reads, event listeners |
| `output/feature-analysis.md` | Update the technical approach section to reflect the new month-based design |

---

## 7. Estimation

- **Implementation**: ~20 minutes
- **Testing**: ~10 minutes
- **Total**: ~30 minutes
- **Complexity**: Very low (purely removing/replacing existing code)
