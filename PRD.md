# Product Requirements Document
## MBK Financial Advisory Dashboard

| | |
|---|---|
| **Owner** | Andre (developer/consultant) |
| **Client** | MBK Financial Services |
| **Status** | In active development — v1 shipped, iterating on feedback |
| **Last updated** | August 2026 |
| **Live demo** | https://blkstar.github.io/mbk-financial-dashboard/ |
| **Repo** | https://github.com/blkstar/mbk-financial-dashboard |

---

## 1. Problem Statement

MBK Financial Services is an accounting/advisory firm that manages financial reporting for a portfolio of small-business clients (e.g. Alky's LLC). To do this, MBK has been paying for **Go High Level**, a general-purpose CRM/marketing platform, purely to get a client-facing financial dashboard.

This is a poor fit on both cost and function:

- Go High Level is priced and packaged around its full CRM/marketing/funnel suite. MBK only needs a narrow slice of that — a way to turn a client's monthly financials into a clean report — but pays for the whole platform to get it.
- Even with the subscription, Go High Level "could barely achieve" what MBK actually needs: a presentable, custom financial report built from a client's own numbers.
- From the client interview: **"I really need a client-facing dashboard with custom reports."** This is the core, unprompted need MBK articulated — not a CRM, not marketing automation, just a dashboard she can put in front of her own clients.

MBK's actual workflow is spreadsheet-driven: she receives or prepares an Excel workbook of a customer's monthly financials (income, COGS, expenses, debt) and needs to turn that into (a) an internal working view she can review and annotate, and (b) a polished, presentable output she can hand to that customer.

## 2. Goals

1. Replace Go High Level for this specific use case with a purpose-built tool that costs nothing to run and does exactly what MBK needs.
2. Give MBK a **client-facing dashboard** — something she can share a link to, or export from, and hand directly to her customers.
3. Support **custom reports** per client: cash flow, forecasting, KPI scorecards, goals vs. benchmarks, recommendations — assembled from that client's own Excel data, not a generic template.
4. Keep data entry low-friction: drop in an Excel file exported from MBK's existing workbook template and have the dashboard populate itself.
5. Produce a clean, shareable **digital PDF report** as the polished deliverable MBK sends to her customers, separate from the interactive dashboard she uses internally.
6. Look and feel custom-built and modern — not like an off-the-shelf SaaS template — reinforcing MBK's own brand.

## 3. Non-Goals

- Not a general CRM, marketing, funnel, or scheduling tool — explicitly the opposite of what Go High Level tried to be for this use case.
- Not a replacement for MBK's actual bookkeeping/accounting system (QuickBooks, etc.) — it's a reporting layer on top of numbers MBK already has.
- No user accounts / login system in v1 — client data lives in a workbook upload and a shareable link, not a multi-tenant SaaS product (may be revisited later, see §9).
- No real-time bank-feed integrations in v1. Data enters via Excel upload only.

## 4. Users

| User | Role | What they need |
|---|---|---|
| **MBK (the client)** | Advisor / primary operator | Uploads customer workbooks, reviews KPI scorecards, logs advisor notes, decides what goes in front of her customer |
| **MBK's customers** (e.g. Alky's LLC) | Report recipients | View a clean summary of their own business performance — revenue, expenses, profit trends, goals vs. benchmarks, recommendations — without needing to interpret raw spreadsheets |
| **Andre (dev)** | Builder / maintainer | Ships features, keeps the design polished, iterates with MBK on which features are still being defined |

## 5. Current State (shipped)

The v1 dashboard is live and in front of MBK for feedback. It currently includes:

- **Multi-view dashboard**: Dashboard, Performance, Goals & Benchmarks, Cash & Debt, Recommendations, Advisor Notes, Client Intake, Industry Benchmarks.
- **Excel intake**: drag-and-drop `.xlsx` parsing (client-side, via SheetJS) that reads MBK's existing workbook template (Settings, Monthly Data, Dashboard, Goals & Benchmarks, Recommendations, Cash & Debt sheets) and populates a client record automatically.
- **Manual client intake** as a fallback when no workbook is available yet.
- **Multi-client support** with a client switcher, so MBK can hold more than one customer's data in the same tool.
- **KPI scorecard** against industry-specific benchmark targets (8 industry profiles built in: liquor store, retail, medical practice, law firm, etc.), with on-target/needs-attention status.
- **Performance charts**: revenue/expense/profit trend, KPI gauges vs. target, operating-expense-mix breakdown, monthly detail table — using real general-ledger-level data, not just summary numbers.
- **Goals & Benchmarks view**: actual-vs-target bar chart plus coaching guidance pulled from MBK's own targets playbook.
- **Recommendations view**: prioritized, categorized action items with an "insight" summary banner.
- **Cash & Debt register** with add/remove debt rows, weighted APR, and debt-service-to-revenue tracking.
- **Mobile-responsive navigation** (hamburger menu + slide-in sidebar) so the dashboard is usable on a phone, not just desktop.
- **Hosting**: static, single-file HTML/CSS/JS app (plus one logo asset) with no backend, deployed via GitHub Pages for a shareable public link.

## 6. Planned / In-Progress Features

### 6.1 PDF report export — *client-facing deliverable*
This is the direct answer to "I really need a client-facing dashboard with custom reports." Two tiers, in order of effort:

- **Near-term (recommended first pass):** a dedicated `@media print` stylesheet plus the existing Print button, so MBK can generate a clean, paginated PDF straight from the browser's native "Print → Save as PDF." This produces real, selectable/searchable text (not a screenshot), keeps file size small, needs no new dependencies or backend, and works today on the static GitHub Pages hosting.
- **Later, if needed:** a headless-render pipeline (Puppeteer/Playwright) for a fully branded, multi-page report with cover page, headers/footers, and page numbers beyond what CSS print alone gives. This is a bigger step — it requires a small server or build step, which the current architecture (static site, no backend) doesn't have yet.
- **Explicitly avoid** the html2canvas + jsPDF "screenshot into a PDF" pattern for the primary report: it rasterizes everything into an image, so recipients can't select, search, or copy text, and it doesn't hold up as a "clean, presentable" client deliverable at print resolution.

### 6.2 Cash flow & forecasting
MBK's stated need includes forward-looking numbers, not just historical actuals. Scope (trend projection vs. simple run-rate vs. scenario inputs) is still being defined with MBK — see Open Questions.

### 6.3 Animated charts & motion polish
Add restrained, purposeful motion to chart reveals and view transitions (respecting `prefers-reduced-motion`) so the dashboard feels more premium in front of a client, without tipping into decorative gimmicks.

### 6.4 Visual/icon design refresh
MBK has signed off on the current design direction. Next pass:
- Replace the current inline-SVG icon set with a more refined, consistent icon system.
- Run the interface through the **impeccable-design-taste** review pass (contrast, color-strategy discipline, motion curves, anti-"AI slop" checks) to sand down anything that still reads as template-default.
- Pull component/interaction references from **21st.dev** for buttons, cards, and nav patterns to modernize specific UI pieces without a full redesign.

## 7. Technical Approach

- **Architecture**: single self-contained HTML file (styles + logic inline) plus one image asset, no build step, no backend, no database. State lives in-memory in the browser per session; persistence is the Excel workbook itself, not the app.
- **Charting**: Chart.js.
- **Spreadsheet parsing**: SheetJS (`xlsx`), client-side only — no file ever leaves the browser during import.
- **Hosting**: GitHub Pages (public repo, static site) — free, zero-maintenance hosting that matches the "no unnecessary subscriptions" goal driving this whole project.
- **Design system**: OKLCH-based green theme matching MBK's brand, restrained color usage, no gradients/glow/glassmorphism (per the impeccable-design-taste ruleset already applied in v1).

## 8. Roadmap

| Phase | Scope | Status |
|---|---|---|
| Phase 1 | Core dashboard, Excel intake, KPI scorecard, charts, mobile nav | ✅ Shipped |
| Phase 2 | Client feedback round, icon refresh, impeccable-design-taste + 21st.dev polish pass | 🔜 Next |
| Phase 3 | PDF report export (print-stylesheet tier) | 🔜 Next |
| Phase 4 | Cash flow / forecasting module | ⏳ Scoping with client |
| Phase 5 | Headless-render PDF pipeline (if print-CSS isn't sufficient) | ⏳ Backlog |

## 9. Open Questions / Client Collaboration

These are the items still being worked out directly with MBK and shouldn't be built ahead of that conversation:

- What exactly should the forecasting/cash-flow feature calculate — trailing run-rate, seasonal trend projection, or scenario/what-if inputs MBK enters herself?
- Should the client-facing PDF report be a fixed template for every customer, or configurable per-industry (mirroring the existing industry benchmark profiles)?
- Does MBK want her customers to ever see the live interactive link, or should customers only ever receive the static PDF (privacy/positioning question)?
- Multi-client scale: at what point does MBK need real persistence (a backend/database) instead of re-uploading a workbook each session?
- Repo visibility: currently public (required for free GitHub Pages hosting); revisit once client feedback is in, per prior conversation.

## 10. Risks & Constraints

- **No backend today** — anything requiring server-side rendering (headless PDF, persistent multi-client storage, auth) is a real architecture change, not a small addition.
- **Public repo/hosting** — acceptable for the feedback stage, but a confidentiality concern once real customer financial data is involved long-term.
- **Manual workbook-template dependency** — the Excel parser expects MBK's specific sheet layout; any drift in that template will silently fail to populate fields.
