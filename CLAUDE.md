# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Static React analytics dashboard for lead distribution pipeline metrics — visualizes acquisition, quality control, delivery, and sales data across sources, clients, and verticals with financial analytics.

## Commands

- **Dev server:** `npm run dev`
- **Build:** `npm run build`
- **Lint:** `npx eslint .`
- **Preview prod build:** `npm run preview`

No test framework is configured.

## Tech Stack

React 19, Vite 8, Tailwind CSS 4 (via `@tailwindcss/vite` plugin), Recharts 3.8 for charts. JavaScript only (no TypeScript). Deployed to GitHub Pages with base path `/lead-distribution-model/`.

## Architecture

Single-page dashboard with no routing. All application logic lives in three files:

- **src/main.jsx** — React entry point (StrictMode, createRoot)
- **src/App.jsx** — Renders `LeadStreamDashboard`
- **src/Dashboard.jsx** — Monolithic component (~720 lines) containing all UI, data, state, and visualization logic

### Dashboard.jsx internals

**Data:** Static `DATA` constant embedded at top of file with `totals`, `daily`/`weekly`/`monthly` timeseries, and `DD` (daily detail) containing per-source/client/vertical metrics keyed by date.

**State:** React hooks only (useState, useMemo). Key state: `activeTab` (dashboard/sources/clients/verticals), `chartRange` (today/week/month/quarter/year), `pipelineTab` (inbound/outbound/both), `hovTip` (tooltip).

**Data flow:** `chartRange` selection → `useMemo` chains aggregate/filter/compute → components render from memoized data. No API calls or server communication.

**Sub-components defined inline:** `Sparkline`, `CustomTooltip`, `Badge`, `SortableTable` (paginated, sortable, 15 rows/page).

**Visualizations:** Recharts LineChart, ComposedChart, BarChart, plus a custom SVG Sankey diagram for the lead pipeline flow.

**Key functions:** `aggDD()` aggregates daily detail by date range/entity, `bucketKey()` groups by month/week/day, `fmt()`/`fmtNum()`/`fmtFull()` format numbers.

## Conventions

- Tailwind utility classes for all styling — no custom CSS files
- Abbreviated naming: `src` (source), `cli` (client), `vert` (vertical), `pct` (percent), `DD` (daily detail)
- Constants uppercase: `COLORS`, `TYPE_COLORS`, `VERT_COLORS`
- Conditional tab rendering: only the active tab's content is in the DOM
- ESLint flat config (v9+) with react-hooks and react-refresh plugins
