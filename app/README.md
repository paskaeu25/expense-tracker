# Expense Tracker

A minimal expense tracking web app — works in the browser and as an Android APK (Capacitor). All data stays on your device.

## Features

- **Add expenses** with amount, date, category, and notes via a FAB (+) button
- **Edit/delete** individual expenses (✎ button per row)
- **Receipt images** — attach up to 3 per expense, thumbnail previews
- **½ split toggle** — halves the entered amount for joint expenses
- **Dashboard** — monthly total, vs-last-month change, transaction count, average per transaction, doughnut chart, top categories
- **Categories** — 9 defaults (Groceries, Eating Out, Transport, Utilities, Entertainment, Health, Shopping, Travel, Other), customizable colors, add/delete
- **Search & filter** — keyword search + category, year/month, and date-range dropdowns
- **Month navigation** — prev/next arrows or tap month label for a date picker
- **Swipe between tabs** (Dashboard, List, Categories, Import)
- **CSV export** — downloads a CSV of all expenses
- **JSON export** — downloads the data file; on Android APK saves directly to Documents folder, then opens the share sheet
- **JSON import** — upload a previously exported file, validates and merges with existing data
- **Works fully offline** — all data in browser `localStorage`
- **Responsive mobile layout** — safe-area-aware, status bar styling on Android

## Android APK

- Data survives reinstalls via Android Auto Backup (Google Drive), Capacitor Preferences, and external storage fallback
- JSON export requires `@capacitor/filesystem` and `@capacitor/share` plugins
- Chart.js is guarded against CDN failure (`typeof Chart` check)

## Usage

Open `index.html` in a browser or visit the [live deployment](https://app-ten-sage-zu9xxqf68o.vercel.app/). No server, no database.

### Sync between devices

1. **Export** — Import tab → Download JSON. On Android, the file saves to your Documents folder and the share sheet opens.
2. Transfer the `.json` file to another device.
3. **Import** — Import tab → choose the file → Import.

## Tech

Vanilla HTML, CSS, JavaScript. [Chart.js](https://www.chartjs.org/) for the doughnut chart. [Capacitor](https://capacitorjs.com/) wraps it as an Android app. No build step — just edit `index.html` and push.
