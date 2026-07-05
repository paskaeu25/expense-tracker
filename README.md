# Expense Tracker

A minimal expense tracking web app with local storage, category management, and spending insights.

## Features

- Add expenses with amount, date, category, and notes
- Attach receipt images (up to 3 per expense)
- Dashboard with monthly spending summary, doughnut chart, and top categories
- Custom categories with color coding
- CSV export
- Data import/export as JSON for cross-device transfer
- Full offline support (localStorage)
- Responsive mobile layout

## Usage

Open `index.html` in a browser or visit the [live deployment](https://app-ten-sage-zu9xxqf68o.vercel.app/). All data stays in your browser — no server, no database.

### Sync between devices

1. **Export** from device A (Import tab → Download JSON)
2. Transfer the file to device B
3. **Import** on device B (Import tab → upload JSON)

## Tech

Vanilla HTML, CSS, JavaScript. Chart.js for the pie chart. No build step, no dependencies to install.
