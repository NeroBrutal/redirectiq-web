# RedirectIQ Web

**Dashboard for RedirectIQ: manage short links, explore analytics, and ask questions about your traffic.**

This repository contains the RedirectIQ web dashboard. It talks to the backend in **[redirectiq-api](https://github.com/<your-username>/redirectiq-api)**.

> 🚧 **Work in progress.** A learning project built phase by phase. See [plan.md](plan.md) for the roadmap.

---

## ✨ Features

- Sign up, log in, and manage your organization
- Create, edit, tag, and organize short links
- Campaigns, UTM builder, QR code downloads, and bulk CSV upload
- Analytics dashboard with charts and breakdowns by country, device, browser, referrer, and UTM
- Live click feed powered by Server-Sent Events
- AI chat assistant for questions like "Why did clicks drop yesterday?"
- Anomaly highlights on charts

---

## 🧰 Tech Stack

| Area | Technology |
|---|---|
| Framework | Astro |
| Language | TypeScript |
| Styling | Tailwind CSS |
| Interactive UI | React islands |
| Data fetching | TanStack Query |
| Charts | Apache ECharts |
| API types | Generated from the API's OpenAPI schema |
| Tooling | pnpm, ESLint, Prettier, Vitest, Playwright |

---

## 🚀 Getting Started

> Full setup instructions will be added once Phase W0 is complete.

Requirements: Node.js 20+, pnpm, and a running [redirectiq-api](https://github.com/<your-username>/redirectiq-api).

```bash
git clone https://github.com/<your-username>/redirectiq-web.git
cd redirectiq-web
cp .env.example .env
pnpm install
pnpm dev
```

The app runs at http://localhost:4321.

### Environment variables

| Variable | Description | Example |
|---|---|---|
| `PUBLIC_API_URL` | Base URL of redirectiq-api | `http://localhost:8000` |
| `PUBLIC_REDIRECT_URL` | Base URL used to display short links | `http://localhost:8001` |

### Syncing API types

With the API running, regenerate TypeScript types whenever the API changes:

```bash
pnpm gen:api
```

---

## 📁 Project Structure

```text
redirectiq-web/
├── src/
│   ├── pages/        # Astro routes
│   ├── layouts/      # page shells
│   ├── components/   # React islands and Astro components
│   ├── lib/          # API client, generated types, helpers
│   └── styles/
├── tests/
├── astro.config.mjs
├── plan.md
└── package.json
```

---

## 🗺️ Roadmap

| Phase | Focus | Status |
|---|---|---|
| W0 | Project setup | ⏳ Planned |
| W1 | Auth pages & app layout | ⏳ Planned |
| W2 | Link management | ⏳ Planned |
| W3 | Analytics dashboard | ⏳ Planned |
| W4 | Campaigns, UTM, QR, bulk upload | ⏳ Planned |
| W5 | Live analytics | ⏳ Planned |
| W6 | AI chat assistant | ⏳ Planned |
| W7 | Anomalies & polish | ⏳ Planned |

---

## 🔗 Related

- **[redirectiq-api](https://github.com/<your-username>/redirectiq-api)**: the RedirectIQ backend

## 📄 License

MIT