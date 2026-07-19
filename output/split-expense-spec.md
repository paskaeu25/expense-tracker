# Spec: Split Expense (½ Toggle)

**Date**: 2026-07-05  
**Author**: Greg, Senior Business Analyst  
**Status**: Draft  

---

## 1. Overview

Users sharing a joint account often need to record only their portion of an expense. The current amount field only accepts a literal number, forcing the user to manually divide before entering. A "Split" toggle lets the user halve the entered amount with one tap.

---

## 2. User Story

**US-SP1: Split an expense in half**
> As a user with a joint account, I want to tap a button next to the amount field so the expense I enter is automatically halved, without having to do mental math.

---

## 3. Acceptance Criteria

| ID | Criterion | Priority |
|----|-----------|----------|
| SP1 | A "½" toggle button appears next to the Amount field in the Add/Edit form | P0 |
| SP2 | Tapping "½" divides the amount currently in the field by 2 and rounds to 2 decimal places | P0 |
| SP3 | Tapping "½" again reverts the amount back to the original value entered before the split | P0 |
| SP4 | The "½" button has a visual active state (filled/highlighted) when split is on | P0 |
| SP5 | The toggle resets to inactive when the form is submitted, cancelled, or cleared | P1 |
| SP6 | The toggle works on Edit too — reopening an existing expense resets the toggle to inactive | P1 |
| SP7 | If the amount field is empty, tapping "½" does nothing (no alert needed) | P2 |

---

## 4. UI/UX Design

### 4.1 Layout

```
┌──────────────────────────────────┐
│  Amount (€)     [ 6.00 ] [½]    │
│  Date           [2026-07-05]    │
│  Category       [Food ▾]        │
│  ...                             │
└──────────────────────────────────┘
```

### 4.2 States

| State | Visual |
|-------|--------|
| **Default** | `[½]` — outlined button, same style as `.btn-outline` |
| **Active (split on)** | `[½]` — filled button, e.g. `background: #1d1d1f; color: #fff;` |

### 4.3 Behaviour

1. User enters `6` in the amount field
2. User taps `[½]` → field shows `3.00`, button becomes active/filled
3. User taps `[½]` again → field reverts to `6.00`, button returns to outline
4. User submits → expense is saved with the currently displayed amount
5. On form reset / cancel / submit → toggle resets to inactive

---

## 5. Technical Approach

### 5.1 What to change

**HTML** — Add the `[½]` button inside the amount's `.form-group`, next to the `<input>`:

```html
<div class="form-group">
  <label>Amount (€)</label>
  <div style="display:flex;gap:0.5rem;align-items:center;">
    <input type="number" id="input-amount" step="0.01" min="0.01" required placeholder="0.00" style="flex:1;">
    <button type="button" class="btn btn-outline btn-sm" id="btn-split" title="Split in half" style="font-size:1rem;padding:0.4rem 0.7rem;">½</button>
  </div>
</div>
```

**CSS** — Add a `.btn-split-active` class or toggle the existing `.btn-outline` class off:
```css
.btn-split-active {
  background: #1d1d1f !important;
  color: #fff !important;
  border-color: #1d1d1f !important;
}
```

**JS** — Track split state and wire toggle:

```javascript
let splitActive = false;
let splitOriginalValue = null;

document.getElementById('btn-split').addEventListener('click', function() {
  const input = document.getElementById('input-amount');
  const val = parseFloat(input.value);
  if (!val || val <= 0) return;

  if (!splitActive) {
    splitOriginalValue = input.value;
    input.value = (val / 2).toFixed(2);
    splitActive = true;
    this.classList.add('btn-split-active');
    this.classList.remove('btn-outline');
  } else {
    input.value = splitOriginalValue;
    splitActive = false;
    this.classList.remove('btn-split-active');
    this.classList.add('btn-outline');
  }
});
```

**Reset hook** — Add to `cancelEdit()` and `addExpense()` (after submit):

```javascript
function resetSplitButton() {
  splitActive = false;
  splitOriginalValue = null;
  const btn = document.getElementById('btn-split');
  btn.classList.remove('btn-split-active');
  btn.classList.add('btn-outline');
}
```

### 5.2 Files to modify

| File | Change |
|------|--------|
| `app/index.html` | Lines 274–278 (Amount form group): replace with flex row containing input + button |
| `app/index.html` | CSS: add `.btn-split-active` class |
| `app/index.html` | JS: add `splitActive` / `splitOriginalValue` variables |
| `app/index.html` | JS: add click handler for `#btn-split` |
| `app/index.html` | JS: add `resetSplitButton()` calls in `cancelEdit()` and `addExpense()` |

### 5.3 Edge cases

- **Empty field**: tap ½ → nothing happens (no error)
- **Zero**: tap ½ → nothing happens (0/2 = 0, but it's a no-op)
- **Decimal**: `3.99 / 2 = 2.00` (rounded to 2 decimal places via `toFixed(2)`)
- **Edit mode**: when editing an expense, the split button starts inactive regardless (no stored split state)
- **Tapping ½ multiple times**: works as toggle: split → original → split → original

---

## 6. Estimation

- **Implementation**: ~30 minutes
- **Testing**: ~15 minutes
- **Total**: ~45 minutes
- **Complexity**: Low
