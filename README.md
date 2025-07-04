# LED Power Planner

A modern web application that helps makers design complex LED-lighting projects. Add LED strips, batteries, step-down converters, wire gauges, and instantly visualize voltage-drop, runtime estimates and overall power budget. The app is **offline-first**, responsive, and built around a rigorously-tested calculation engine.

---

## 🎯 Key Features

| Area               | Highlights                                                                               |
| ------------------ | ---------------------------------------------------------------------------------------- |
| Planning Canvas    | • Sortable peripheral cards • One-click enable/disable for what-if analysis              |
| Metrics Panel      | Live charts for voltage-drop, runtime curves, power histogram (ShadCN Charts / Recharts) |
| Calculation Engine | Pure TypeScript package (`packages/core-calc`) with 100% test coverage                   |
| Offline-first      | Local IndexedDB storage via Dexie; optional Supabase sync when signed-in                 |
| Component Library  | Shared UI components via ShadCN/UI and Tailwind, exported from `packages/ui`             |
| Monorepo Tooling   | Turborepo, TypeScript project-references, ESLint/Prettier, Vitest, Playwright            |

---

## 🗂️ Monorepo Layout

```
led-app/
├─ apps/
│  └─ web/              # Next.js 14 App Router frontend
├─ packages/
│  ├─ ui/               # ShadCN component library (import from @workspace/ui)
│  ├─ core-calc/        # Pure calculation engine (added in Phase P06)
│  ├─ eslint-config/    # Centralised ESLint rules
│  └─ typescript-config/# Shared tsconfig bases
├─ implementation_plan.md  # Phase/task tracker with check-boxes
└─ …
```

Path aliases are defined in the root `tsconfig.json`:

```jsonc
"paths": {
  "@/*": ["./apps/web/*"],
  "@/packages/*": ["./packages/*"]
}
```

---

## 🚀 Getting Started (Development)

### 1. Prerequisites

- Node.js ≥ 20.x (includes npm ≥ 10.x)
- macOS, Linux or WSL2

### 2. Install dependencies

```bash
npm ci
```

### 3. Launch all dev targets (with Turbo)

```bash
npm run dev
```

This runs:

- `next dev` for `apps/web`
- Tailwind watch
- Type safety in-watch mode

Open http://localhost:3000 to view the app.

### 4. Lint & Tests while you code

```bash
npm run lint   # ESLint + Prettier
npm test       # Vitest unit tests
```

---

## 🏗️ Production Build

```bash
npm run build          # Turbo builds all packages & apps
npm run start-web      # Starts Next.js in production mode (PORT=3000)
```

Static assets are served from `.next` and can be deployed to Vercel or any Node host.

---

## 🧩 Adding ShadCN Components

Inside `apps/web` run:

```bash
npx shadcn-ui@latest add button card dialog sheet tabs switch charts -c apps/web
```

Components are generated into `packages/ui/src/components` and re-exported for app usage:

```tsx
import { Button } from "@workspace/ui/components/button"
```

---

## 🧪 Testing Strategy

| Layer    | Tool                            | Notes                                  |
| -------- | ------------------------------- | -------------------------------------- |
| Unit     | Vitest + @testing-library/react | Runs in`npm test`                      |
| Property | fast-check                      | Used inside `core-calc`                |
| E2E      | Playwright                      | Scenarios recorded in `apps/web/tests` |
| Visual   | Chromatic (Storybook)           | Ensures UI regressions are caught      |

Run **all** quality gates:

```bash
npm run check   # lint + unit tests + type-check
```

---

## 📦 Scripts (root `package.json`)

| Script      | Purpose                                         |
| ----------- | ----------------------------------------------- |
| `dev`       | Turbo pipeline: build packages then `next dev`  |
| `lint`      | ESLint + Prettier                               |
| `test`      | Vitest (unit/property)                          |
| `check`     | Combination of `lint` + `test` + `tsc --noEmit` |
| `build`     | Production build across monorepo                |
| `start-web` | Start Next.js production server                 |

---

## 🤝 Contributing Workflow

1. Review `implementation_plan.md` and pick an unchecked ⚡ task within your phase.
2. Create a feature branch (if using Git) or work directly (if file-based workflow).
3. When done, mark the task `[x]` with your identifier comment.
4. Ensure `npm run check` passes.
5. Submit your changes for review (PR or diff).

---

## 📄 License

MIT © 2024 LED-App Contributors
