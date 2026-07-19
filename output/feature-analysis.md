# Feature Analysis — Expense Tracker

**Date**: 2026-07-05  
**Author**: Greg, Senior Business Analyst

---

## 1. Current State Review

### 1.1 What works well

| Area | Assessment |
|------|-----------|
| **UI/UX** | Clean iOS-style cards, tabs, FAB — polished and familiar |
| **Core CRUD** | Add, edit, delete expenses with receipt attachments |
| **Dashboard** | Monthly summary cards + doughnut chart + top categories |
| **Categories** | Custom categories with colour coding; rename/delete support |
| **Data portability** | CSV export, JSON import/export for cross-device sync |
| **Offline** | Fully client-side via `localStorage`, no backend dependency |

### 1.2 Pain points & paper cuts

| Issue | Severity | Notes |
|-------|----------|-------|
| No search or filter | Medium | Becomes tedious once >50 expenses exist |
| Receipts use `blob:` URLs | Medium | Lost on page refresh — images don't persist |
| No budget visibility | Medium | App shows what was spent, not what was planned |
| Add tab hidden by design | Low | FAB-only entry; intentional but may confuse new users |
| No spending trends | Low | Only current month view; no month-over-month line chart |

---

## 2. Next Feature: Search & Filter

### 2.1 Rationale

As the expense log grows, finding a specific expense becomes increasingly tedious — the user has to scroll through months of entries. A search and filter bar lets users quickly locate expenses by keyword (note text), category, or date range. It's the highest-value quality-of-life improvement for daily use.

### 2.2 Scope

**In scope:**
- Text search across expense notes
- Filter by category (dropdown or tag selector)
- Filter by date range (from/to)
- Real-time filtering as the user types/selects
- Clear/reset filters button

**Out of scope (this iteration):**
- Search by amount
- Saved searches or filter presets
- Full-text search across all fields (just notes + category names)

### 2.3 User Stories

**US-S1: Search expenses by keyword**
> As a user, I want to type a keyword and see only expenses whose notes contain that word, so I can quickly find a specific expense.

**US-S2: Filter by category**
> As a user, I want to select a category from a dropdown and see only expenses in that category, so I can review my spending in one area.

**US-S3: Filter by date range**
> As a user, I want to pick a start and end date and see only expenses in that period, so I can analyse spending over a custom timeframe.

**US-S4: Combined filters**
> As a user, I want to combine keyword search, category filter, and date range together, so I can narrow down to exactly the expenses I need.

### 2.4 Acceptance Criteria

| ID | Criterion | Priority |
|----|-----------|----------|
| S1 | Text input at the top of the Expenses tab filters the list in real time as the user types | P0 |
| S2 | Category dropdown filter shows "All categories" plus each existing category | P0 |
| S3 | Date range inputs (from/to) filter expenses within the selected range | P0 |
| S4 | All filters combine: results match text AND category AND date range | P0 |
| S5 | A "Clear filters" button resets all filters and shows all expenses | P1 |
| S6 | Empty state shows "No expenses match your filters" when filters yield no results | P1 |
| S7 | Filters only affect the Expenses list view (Dashboard is unchanged) | P0 |
| S8 | The monthly grouping in the Expenses list is preserved when filters are applied (only matching months shown) | P1 |

### 2.5 Technical Approach

**UI placement:**
- Filter bar sits inside the Expenses tab card, above the expense list
- Layout: text input + category select + date range (from/to) on one row (stack on mobile)
- Clear filters button at the end

**Implementation:**
- Add a `renderFilteredExpenses()` function that reads filter values and calls `renderAllExpenses()` with a filtered dataset
- Wire `input`, `change` events on filter elements to re-render
- No new `localStorage` keys needed — filters are ephemeral (in-memory)

**CSS additions:**
- `.filter-bar` — flex row wrapping the filter controls
- `.filter-input` — styled inline inputs that match the existing form styles

**Estimated effort:** ~50–70 lines of JS, ~20 lines of CSS. No new dependencies.

### 2.6 Alternative Features Considered

| Feature | Effort | Impact | Reason deprioritised |
|---------|--------|--------|----------------------|
| Monthly Budget Tracking | Medium | High | Valuable but user chose search/filter first |
| Yearly spending trends (line chart) | Medium | Medium | Better once there are several months of data |
| Dark mode | Low | Low | Cosmetic only — no functional value |
| Recurring expenses | High | Medium | Complex edge cases; not yet needed |
| Receipt persistence (Base64/IndexedDB) | Medium | High | Important but independent concern |

---

## 3. Decision

**Proceed with Search & Filter (US-S1 through US-S4)** as the next feature, per user directive. This addresses the most immediate usability pain point as the expense dataset grows.

---

*Document prepared by Greg, Senior Business Analyst*
