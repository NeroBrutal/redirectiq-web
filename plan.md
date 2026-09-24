# PLAN.md — RedirectIQ Web (Frontend)

This file is the single source of truth for building the RedirectIQ dashboard.
It is written for Claude (or any AI coding assistant) and for the developer.

The backend lives in a separate repo, `redirectiq-api`, with its own plan.md.
This repo builds **only the frontend**. Never add backend code here.

---

## How to Use This Plan (Read First, Claude)

- Work on **one phase at a time**. Never start the next phase unless asked.
- Each phase lists the **API phase it depends on**. If that API phase is not done yet,
  build against mock data that matches the API's response shapes, and mark it in the Progress Log.
- At the start of a phase, restate its goal and task list, then build it step by step.
- At the end of a phase, check every acceptance criterion, run lint and tests, and **stop**.
  Summarize what was built and anything left open.
- Do not add libraries that are not listed in this plan without asking.
- Prefer simple, readable components. Add short comments explaining *why* for non-obvious choices.
- Update the **Progress Log** at the bottom when a phase is complete.

Example prompts:

```text
Read plan.md. Start Phase W0.
Read plan.md. Continue Phase W3 from task 2.
```

---

## 1. Project Summary

The RedirectIQ web dashboard. Users sign in, manage short links, view analytics,
watch live clicks, and ask an AI assistant questions about their traffic.
All data comes from `redirectiq-api`.

---

## 2. Tech Stack

| Area | Choice |
|---|---|
| Framework | Astro (output: `server` or `static` + client-side islands, decided in W0) |
| Language | TypeScript (strict mode) |
| Styling | Tailwind CSS |
| Interactive UI | React islands |
| Data fetching | TanStack Query |
| Charts | Apache ECharts (`echarts-for-react`) |
| API types | `openapi-typescript` generated from the API's `/openapi.json` |
| API client | `openapi-fetch` (typed fetch using the generated types) |
| Forms | React Hook Form + Zod |
| Package manager | pnpm |
| Lint / format | ESLint + Prettier |
| Tests | Vitest (units), Playwright (end-to-end, from W2) |

---

## 3. How the Frontend Talks to the API

- Base URL comes from `PUBLIC_API_URL` (e.g. `http://localhost:8000`).
- Auth uses the API's HTTP-only session cookie. Every request uses `credentials: "include"`.
  The frontend never stores tokens in localStorage.
- Send the CSRF token header on all POST/PUT/PATCH/DELETE requests (as the API defines it).
- Types are **generated, never hand-written**: run `pnpm gen:api` after any API change.
- On a 401 response, redirect to `/login`.
- Show API errors using the shared shape `{ "error": { "code", "message" } }`.

---

## 4. Repository Structure

```text
redirectiq-web/
├── plan.md
├── README.md
├── .env.example
├── astro.config.mjs
├── package.json
├── src/
│   ├── pages/
│   │   ├── index.astro          # landing page
│   │   ├── login.astro
│   │   ├── signup.astro
│   │   └── app/                 # authenticated area
│   │       ├── index.astro      # overview
│   │       ├── links/
│   │       ├── campaigns/
│   │       ├── live.astro
│   │       └── assistant.astro
│   ├── layouts/                 # PublicLayout, AppLayout
│   ├── components/
│   │   ├── ui/                  # buttons, inputs, tables, modals
│   │   ├── links/
│   │   ├── analytics/
│   │   └── assistant/
│   ├── lib/
│   │   ├── api/                 # client + generated schema.d.ts
│   │   ├── query.ts             # TanStack Query client
│   │   └── format.ts            # number/date helpers
│   └── styles/
└── tests/
```

---

## 5. Global Rules

1. TypeScript strict; no `any` unless justified in a comment.
2. All server data goes through TanStack Query (no manual fetch in components).
3. Every data view has loading, empty, and error states.
4. Responsive: works on mobile widths.
5. Support light and dark mode with Tailwind's `dark:` classes.
6. Accessible: labels on inputs, keyboard-usable modals and menus, sufficient contrast.
7. Never render user-provided URLs or titles as raw HTML.
8. Keep components small; move shared UI into `components/ui/`.

---

## 6. Phases

### Phase W0 — Project Setup
Depends on: API Phase 0.

Tasks:
1. Create the Astro project with TypeScript strict, Tailwind, and the React integration.
2. ESLint, Prettier, and Vitest configured; `pnpm lint` and `pnpm test` scripts.
3. `.env.example` with `PUBLIC_API_URL` and `PUBLIC_REDIRECT_URL`.
4. `pnpm gen:api` script generating `src/lib/api/schema.d.ts` from the API.
5. Typed API client (`openapi-fetch`) with `credentials: "include"` and error handling.
6. TanStack Query provider set up for React islands.
7. A page that calls the API's `/health` and shows its status.

Acceptance criteria:
- [ ] `pnpm dev` runs and the health page shows the API is up.
- [ ] Lint and tests pass.

---

### Phase W1 — Auth Pages and App Layout
Depends on: API Phase 1.

Tasks:
1. Landing page (simple hero, features, call to action).
2. Signup and login forms with validation and error messages.
3. `AppLayout`: sidebar navigation, top bar with user menu, organization name, logout.
4. Route guard: unauthenticated users visiting `/app/*` are sent to `/login`.
5. Light/dark mode toggle.

Acceptance criteria:
- [ ] A user can sign up, log in, see the app shell, and log out.
- [ ] Refreshing the page keeps the user logged in (cookie session).

---

### Phase W2 — Link Management
Depends on: API Phases 1–2.

Tasks:
1. Links table: slug, short URL, destination, title, status, created date; pagination and search.
2. Create link modal: destination, optional custom slug, title, expiration.
3. Edit and delete (with confirmation).
4. Copy short link button.
5. Hide create/edit/delete actions for `viewer` role.
6. First Playwright test: log in → create link → see it in the table.

Acceptance criteria:
- [ ] Full create, edit, delete flow works against the real API.
- [ ] Validation errors from the API show on the right form fields.

---

### Phase W3 — Analytics Dashboard
Depends on: API Phase 4.

Tasks:
1. Date range picker (last 24h, 7d, 30d, custom) shared across the page via URL query params.
2. Summary cards: total clicks, unique visitors, bot share.
3. Clicks-over-time line chart (ECharts).
4. Breakdown panels: countries, devices, browsers, OS, referrers, UTM.
5. Toggle to include or exclude bots.
6. Link detail page reusing the same components filtered to one link.
7. Organization overview page at `/app`.

Acceptance criteria:
- [ ] Numbers match the API responses.
- [ ] Charts handle empty data and large numbers cleanly.

---

### Phase W4 — Link Features
Depends on: API Phase 5.

Tasks:
1. Tags and campaigns: create, assign, filter links by them.
2. UTM builder form with live URL preview.
3. QR code preview and PNG/SVG download.
4. Password protection and expiration settings on links.
5. Bulk CSV upload with a per-row result report.
6. Campaign comparison view (side-by-side charts).

Acceptance criteria:
- [ ] All features work end to end against the API.

---

### Phase W5 — Live Analytics
Depends on: API Phase 6.

Tasks:
1. Live page connecting to the SSE endpoint with `EventSource` (with credentials).
2. Live counter and a scrolling feed of recent clicks.
3. Reconnect automatically and show connection status.
4. Close the connection when leaving the page.

Acceptance criteria:
- [ ] Clicking a short link updates the live page within a few seconds.

---

### Phase W6 — AI Chat Assistant
Depends on: API Phase 7.

Tasks:
1. Chat page with message list and input.
2. Stream the assistant's answer token by token.
3. Show which data tools were used under each answer ("Used: compare_periods").
4. Suggested starter questions.
5. Conversation history sidebar.
6. Friendly message when the daily AI limit is reached.

Acceptance criteria:
- [ ] Asking "Why did clicks drop yesterday?" returns a streamed, data-backed answer.

---

### Phase W7 — Anomalies and Polish
Depends on: API Phase 8.

Tasks:
1. Mark anomalies on time-series charts with tooltips.
2. Anomalies list on the overview page, with an "Ask AI why" button.
3. Polish pass: consistent spacing, empty states, loading skeletons, mobile layout.
4. Lighthouse check; fix major performance and accessibility issues.

Acceptance criteria:
- [ ] Synthetic spikes from the API seed script appear as anomalies on charts.

---

## 7. Progress Log

| Phase | Status | Date | Notes |
|---|---|---|---|
| W0 | Not started | | |
| W1 | Not started | | |
| W2 | Not started | | |
| W3 | Not started | | |
| W4 | Not started | | |
| W5 | Not started | | |
| W6 | Not started | | |
| W7 | Not started | | |
