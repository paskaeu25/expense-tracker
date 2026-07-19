# Spec: Year + Month Dropdown Filter (Replace Month Input)

**Date**: 2026-07-05
**Author**: Greg, Senior Business Analyst
**Status**: Draft

---

## 1. Overview

The current filter uses a native `<input type="month">` which shows dashes ("---- --") when nothing is selected — this looks ugly and lacks clear UX. The user wants two separate dropdown selectors: one for year, one for month. Both default to an "All" option so no date filtering is applied unless explicitly chosen.

---

## 2. User Stories

**US-D1: Filter by year**
> As a user, I want to select a year from a dropdown and see only expenses from that year.

**US-D2: Filter by month**
> As a user, I want to select a month from a dropdown and see only expenses from that month (across all years, or combined with a selected year).

**US-D3: Combined year + month**
> As a user, I want to select both a year and a month to zoom in on a specific month in a specific year.

**US-D4: Default no filter**
> As a user, I want both dropdowns to default to "All years" / "All months" so the full expense list shows when I open the app.

---

## 3. Acceptance Criteria

| ID | Criterion | Priority |
|----|-----------|----------|
| D1 | Replace `<input type="month">` with two `<select>` dropdowns: Year and Month | P0 |
| D2 | Year dropdown has options: "All years" followed by years from 2020 up to and including the current year | P0 |
| D3 | Month dropdown has options: "All months" followed by January through December (with values `01`–`12`) | P0 |
| D4 | Both dropdowns default to the empty/"All" option — no date filtering on initial load | P0 |
| D5 | Selecting a year filters expenses by that year (`e.date.slice(0, 4)` match) | P0 |
| D6 | Selecting a month filters expenses by that month (`e.date.slice(5, 7)` match) | P0 |
| D7 | Selecting both year AND month filters expenses that match both | P0 |
| D8 | Year dropdown is dynamically populated on render (so new expenses in future years still work) | P1 |
| D9 | The "Clear" button resets both dropdowns to "All years" / "All months" | P0 |
| D10 | All filters (keyword + category + year + month) still combine together | P0 |

---

## 4. UI/UX Design

### 4.1 After

```
[Search notes...] [All categories ▾] [All years ▾] [All months ▾] [Clear]
```

### 4.2 Behaviour

| Action | Result |
|--------|--------|
| Open page | Both dropdowns show "All years" / "All months" — all expenses visible |
| Pick "2026" in Year | Only expenses from 2026 |
| Pick "July" in Month | Only expenses from July (any year) |
| Pick "2026" + "July" | Only expenses from July 2026 |
| Click Clear | Both reset to "All" — all expenses visible again |

---

## 5. Technical Approach

### 5.1 HTML changes

Replace the single month input with two selects:

```html
<!-- Before -->
<input type="month" class="filter-input" id="filter-month" placeholder="yyyy-mm">

<!-- After -->
<select class="filter-input" id="filter-year">
  <option value="">All years</option>
</select>
<select class="filter-input" id="filter-month">
  <option value="">All months</option>
  <option value="01">January</option>
  <option value="02">February</option>
  <option value="03">March</option>
  <option value="04">April</option>
  <option value="05">May</option>
  <option value="06">June</option>
  <option value="07">July</option>
  <option value="08">August</option>
  <option value="09">September</option>
  <option value="10">October</option>
  <option value="11">November</option>
  <option value="12">December</option>
</select>
```

### 5.2 CSS

No new CSS needed — the existing `.filter-bar select` and `.filter-input` rules already style `<select>` elements. Remove any leftover `.filter-input[type="month"]` rules.

Year dropdown width: Set a reasonable width (e.g. 110px) via a specific rule if needed:
```css
#filter-year { width: 110px; }
#filter-month { width: 120px; }
```

### 5.3 JS changes

**`renderYearFilter()`** — New function to populate the year dropdown dynamically:

```javascript
function renderYearFilter() {
  const sel = document.getElementById('filter-year');
  const currentVal = sel.value;
  const currentYear = new Date().getFullYear();
  let html = '<option value="">All years</option>';
  for (let y = currentYear; y >= 2020; y--) {
    html += `<option value="${y}">${y}</option>`;
  }
  sel.innerHTML = html;
  sel.value = currentVal;
}
```

Call `renderYearFilter()` from `renderAll()`.

**`renderAllExpenses()`** — Replace the month filter logic with year + month:

```javascript
// Read
const yearFilter = document.getElementById('filter-year').value;
const monthFilter = document.getElementById('filter-month').value;

// Apply
if (yearFilter) {
  filtered = filtered.filter(e => e.date.slice(0, 4) === yearFilter);
}
if (monthFilter) {
  filtered = filtered.filter(e => e.date.slice(5, 7) === monthFilter);
}
```

**`clearFilters()`** — Reset both dropdowns:

```javascript
document.getElementById('filter-year').value = '';
document.getElementById('filter-month').value = '';
```

**Event listeners** — Both dropdowns re-render on change:

```javascript
document.getElementById('filter-year').addEventListener('change', renderAllExpenses);
document.getElementById('filter-month').addEventListener('change', renderAllExpenses);
```

### 5.4 Edge cases

- **Future years**: the year dropdown goes up to the current year. When a new year begins, `renderYearFilter()` will include it automatically since it computes from `new Date().getFullYear()`.
- **Past years before 2020**: not listed. If the user has older expenses, manually type the year in the HTML or expand the range (low priority).
- **Month without year selected**: shows that month across all years (e.g. "July" shows July 2025, July 2026, etc.)
- **Year without month selected**: shows all months in that year

---

## 6. Files to modify

| File | Change |
|------|--------|
| `app/index.html` | Replace `<input type="month">` with two `<select>` dropdowns (year + month) |
| `app/index.html` | Remove `.filter-input[type="month"]` CSS rule, add `#filter-year` / `#filter-month` width rules |
| `app/index.html` | Add `renderYearFilter()` function; call it from `renderAll()` |
| `app/index.html` | Update filter reads/filtering in `renderAllExpenses()` |
| `app/index.html` | Update `clearFilters()` |
| `app/index.html` | Update event listeners |

---

## 7. Estimation

- **Implementation**: ~30 minutes
- **Testing**: ~10 minutes
- **Total**: ~40 minutes
- **Complexity**: Low
