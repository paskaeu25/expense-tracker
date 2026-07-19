# Business Requirements Document — Expense Tracker

## 1. Executive Summary

A personal expense tracking application that allows users to log, categorize, and analyze their spending. The goal is to provide a simple, fast way to understand where money goes without the complexity of full-featured accounting software.

---

## 2. Business Objectives

- Reduce time spent on manual expense logging by 50% vs spreadsheet tracking
- Provide actionable spending insights with < 3 taps from open
- Support both individuals and small business owners tracking reimbursable expenses

---

## 3. Scope

### In scope

- Manual expense entry (amount, category, date, notes)
- Expense categorization with custom categories
- Monthly and yearly spending summaries
- Export to CSV
- Receipt image attachment (camera upload)

### Out of scope (v1)

- Bank feed / automatic transaction import
- Multi-currency support
- Recurring expense automation
- Team/shared expense tracking
- Budget alerts

---

## 4. User Personas

| Persona | Needs | Pain points |
|---------|-------|-------------|
| Freelancer | Track billable expenses per client; export for tax filing | Manual spreadsheet upkeep; lost receipts |
| Budget-conscious individual | Monthly spending breakdown by category | No visibility into where money goes |
| Small business owner | Categorize reimbursable vs operational costs | Mixing personal and business expenses |

---

## 5. Functional Requirements

### FR-01: Add expense
- **Description**: User can create an expense with amount, category, date, and optional note
- **Acceptance criteria**:
  - Amount field accepts numbers with up to 2 decimal places
  - Category is selected from a predefined or custom list
  - Date defaults to today, editable
  - Note is optional, max 500 chars
- **Priority**: P0

### FR-02: Categorize expenses
- **Description**: User can assign a category to each expense and manage categories
- **Acceptance criteria**:
  - Default categories: Food, Transport, Utilities, Entertainment, Health, Shopping, Other
  - User can add, rename, or delete custom categories
  - Deleting a category prompts re-assignment of its expenses
- **Priority**: P1

### FR-03: View summary
- **Description**: Dashboard showing spending grouped by month and category
- **Acceptance criteria**:
  - Pie chart by category for selected month
  - Total spending vs previous month comparison
  - Top 3 spending categories highlighted
- **Priority**: P0

### FR-04: Export data
- **Description**: User can export expenses as CSV
- **Acceptance criteria**:
  - Export includes all fields: date, amount, category, note
  - Date range filter available before export
  - File downloads in standard CSV format
- **Priority**: P1

### FR-05: Attach receipt
- **Description**: User can take or upload a photo of a receipt and attach it to an expense
- **Acceptance criteria**:
  - Camera and gallery access on mobile
  - Image stored and viewable from expense detail screen
  - Max 3 images per expense
- **Priority**: P2

---

## 6. Non-Functional Requirements

| Requirement | Target |
|-------------|--------|
| **Performance** | Expense creation < 1s; dashboard loads < 2s |
| **Offline support** | Full functionality without internet; sync when online |
| **Data retention** | Expenses stored locally; optional cloud backup |
| **Security** | No plain-text storage; biometric lock option |
| **Platform** | iOS & Android (React Native) |

---

## 7. Assumptions & Constraints

- Users will enter expenses manually (no bank API integration in v1)
- Receipt images stored on-device with optional iCloud/Google Drive backup
- App is free with no subscription model in initial release

---

## 8. Open Questions

- Should categories be hierarchical (e.g., Food > Groceries, Food > Dining)?
- What is the target minimum deployment OS version?
- Do we need a web companion, or is mobile-only sufficient?

---

## 9. Glossary

| Term | Definition |
|------|-----------|
| P0 | Must-have for launch |
| P1 | Important, can ship shortly after |
| P2 | Nice-to-have, future iteration |
| BRD | Business Requirements Document |
