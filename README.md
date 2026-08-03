# MBK Financial Advisory Dashboard

A client-facing financial reporting dashboard built for MBK Financial Services — a lightweight, purpose-built replacement for paying for a full CRM platform (Go High Level) just to get a presentable financial report out to clients.

**Live demo:** https://blkstar.github.io/mbk-financial-dashboard/
**Product requirements:** see [PRD.md](./PRD.md)

## What it does

MBK receives a customer's monthly financials as an Excel workbook. This dashboard turns that workbook into:

- An internal working view MBK can review, score against industry benchmarks, and annotate.
- A clean, presentable report she can hand to that customer — either as the live dashboard link or a browser-printed PDF.

## Features

- **Multi-view dashboard** — Dashboard, Performance, Goals & Benchmarks, Cash & Debt, Recommendations, Advisor Notes, Client Intake, Industry Benchmarks.
- **Excel intake** — drag-and-drop `.xlsx` upload that parses MBK's workbook template (Settings, Monthly Data, Dashboard, Goals & Benchmarks, Recommendations, Cash & Debt sheets) and builds a client record automatically. Parsing happens entirely in the browser — no file is uploaded anywhere.
- **Multi-client support** with a client switcher.
- **KPI scorecard** against built-in industry benchmark targets (liquor store, retail, medical practice, law firm, chiropractic, contractor/trades, professional services, accounting/tax).
- **Performance charts** — revenue/expense/profit trend, KPI gauges vs. target, operating-expense-mix breakdown, monthly detail table.
- **Goals & Benchmarks** — actual-vs-target chart plus coaching guidance from MBK's targets playbook.
- **Recommendations** — prioritized, categorized action items with an at-a-glance insight summary.
- **Cash & Debt register** — add/remove debt rows, weighted APR, debt-service-to-revenue tracking.
- **Mobile navigation** — hamburger menu with a slide-in sidebar for use on a phone.

## Tech stack

- Single self-contained HTML file (`MBK_Financial_Dashboard.html`) — no build step, no backend, no database.
- [Chart.js](https://www.chartjs.org/) for charts.
- [SheetJS (xlsx)](https://sheetjs.com/) for client-side Excel parsing.
- Hosted as a static site on GitHub Pages.

## Repo contents

```
MBK_Financial_Dashboard.html   the app
mbk-logo.png                   sidebar logo asset (referenced by relative path — keep alongside the HTML file)
index.html                     redirect to MBK_Financial_Dashboard.html so the root Pages URL works
PRD.md                         product requirements document
README.md                      this file
```

## Using it

Open the [live link](https://blkstar.github.io/mbk-financial-dashboard/) and either:
- Drop a client's `.xlsx` workbook into the **Client Intake** view, or
- Add a client manually from the same view,

then switch clients from the sidebar and browse the reporting views. Use the **Print** button (or your browser's print dialog) to save a PDF snapshot of the current view for a client.

## Status

v1 is live and in front of the client for feedback. See [PRD.md](./PRD.md) for what's shipped vs. planned (PDF report export, cash flow/forecasting, design polish pass, animated chart transitions).

## Updating the dashboard

This is a static site — there's no build step. Edit `MBK_Financial_Dashboard.html` directly and push to `main`; GitHub Pages redeploys automatically. Keep `mbk-logo.png` in the same folder since the HTML references it by relative path.
