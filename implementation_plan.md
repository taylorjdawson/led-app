# LED Power Planner – Detailed Implementation Plan

This document breaks the project down into **phases (_Pxx_)** and **granular tasks (_Pxx-Syy_)**. Each task is represented as a Markdown checkbox. When a task is finished, check it off (☑︎ or ✔︎).

---

## Legend

- **Pxx** – Phase identifier (two-digit)
- **Syy** – Step identifier within the phase (two-digit)
- **ID** – Combined identifier `Pxx-Syy`
- ⚡ – Task can run in parallel with other ⚡ tasks in the same phase

---

## Phase Dependencies & Parallelization

Below is a high-level view of how phases depend on each other and which can run in parallel. Use this to coordinate work-streams and scheduling.

Legend

- → must finish before the next phase begins
- ⇄ can overlap / run in parallel once minimal prerequisites are met

```
P00  Bootstrapping & CI
        ↓
P01  Repo & tooling hygiene   ⇄
P02  Core UI skeleton         ⇄── (these three start together)
P03  ShadCN setup             ⇄
        ↓  (contracts agreed: Peripheral, CalcSnapshot)
P04  Peripheral CRUD          ⇄
P05  Metrics & charts         ⇄── (overlap once data contract frozen)
        ↓  (core-calc API drafted)
P06  Calc engine & persistence ⇄  (continues alongside late P04/P05)
        ↓  (vertical slice ready)
P07  Testing & quality gates  (continuous, ramps up after P04+P05 beta)
        ↓  (feature-complete baseline)
P08  Polish & performance     (final hardening; some audits can start early)
```

In plain terms:

1. **P00 must finish first.**
2. **P01, P02, and P03** run in parallel once P00 lands.
3. **P04 and P05** overlap after we freeze shared data contracts.
4. **P06** can proceed while P04/P05 wrap up once the core-calc API is drafted.
5. **P07** testing is continuous but intensifies once CRUD + charts are usable.
6. **P08** polish largely comes last, though accessibility/perf audits can begin earlier.

---

## Phase Overview

| Phase | ID  | Goal                                                   |
| ----- | --- | ------------------------------------------------------ |
| 0     | P00 | Project bootstrapping & CI baseline                    |
| 1     | P01 | Repository & tooling hygiene                           |
| 2     | P02 | Core UI layout skeleton (Next.js App Router)           |
| 3     | P03 | ShadCN component scaffolding                           |
| 4     | P04 | Peripheral CRUD flow with stubbed calc engine          |
| 5     | P05 | Metrics & graph panel (mocked data)                    |
| 6     | P06 | State management, persistence, calculation integration |
| 7     | P07 | Testing & quality gates                                |
| 8     | P08 | Polishing, accessibility & performance                 |

---

# Tasks by Phase

## Phase 0 – Project Bootstrapping (ID: **P00**)

> Objective: ensure the development environment is reproducible and automated.

- [ ] **P00-S01** – Verify Node.js \>= 20.x and npm \>= 10.x installed on dev machine.
- [ ] **P00-S02** – Run `npm ci` (clean install) and commit updated `package-lock.json`.
- [ ] **P00-S03** ⚡ – Configure **Turbo** remote caching (if using Vercel/Turbo Cloud).
- [ ] **P00-S04** ⚡ – Set up **Husky** pre-commit with _eslint_ + _prettier_.
- [ ] **P00-S05** ⚡ – Add **GitHub Actions** CI workflow: _npm ci_, _turbo run lint test build_.

## Phase 1 – Repo & Tooling Hygiene (ID: **P01**)

> Objective: enforce consistent code style and cross-workspace TypeScript settings.

- [ ] **P01-S01** ⚡ – Audit `packages/eslint-config` for latest rules; bump to latest ESLint + plugins.
- [ ] **P01-S02** ⚡ – Enable **import-order** and **unused-imports** plugins.
- [ ] **P01-S03** ⚡ – Extend `packages/typescript-config` with strict settings (`noUncheckedIndexedAccess`, etc.).
- [ ] **P01-S04** ⚡ – Configure **VSCode settings.json** (workspace) to auto-fix on save.
- [ ] **P01-S05** ⚡ – Add _editorconfig_ to align trailing newline & indent style across IDEs.

## Phase 2 – Core UI Layout Skeleton (ID: **P02**)

> Objective: lay down routing and top-level layout so pages render placeholder content.

- [ ] **P02-S01** ⚡ – Implement `/layout.tsx` with `<ProjectSidebar>`, `<RightMetricsPanel>` placeholders.
- [ ] **P02-S02** ⚡ – Add _TopNav_ component with theme toggle (ShadCN `DropdownMenu`, `Switch`).
- [ ] **P02-S03** ⚡ – Define page routes: `/projects`, `/projects/[id]`, `/projects/[id]/settings`.
- [ ] **P02-S04** ⚡ – Create responsive CSS grid (desktop three-column, mobile stacked).
- [ ] **P02-S05** ⚡ – Stub placeholder components (`<SidebarPlaceholder />`, `<MetricsPlaceholder />`).

## Phase 3 – ShadCN Component Scaffolding (ID: **P03**)

> Objective: install ShadCN/ui and scaffold reusable components.

- [ ] **P03-S01** – Run `npx shadcn-ui@latest init` in `apps/web`.
- [ ] **P03-S02** ⚡ – Add **Button**, **Card**, **Dialog**, **Sheet**, **Tabs**, **Switch** via shadcn CLI.
- [ ] **P03-S03** ⚡ – Create `packages/ui` re-export barrel to share ShadCN components.
- [ ] **P03-S04** ⚡ – Verify dark-mode styling using `next-themes` provider.

## Phase 4 – Peripheral CRUD Flow (ID: **P04**)

> Objective: deliver **PeripheralCard** list, **AddPeripheralModal**, and local state reducer with fake data.

- [ ] **P04-S01** – Define TypeScript `Peripheral` interface (id, type, volts, amps, etc.).
- [ ] **P04-S02** ⚡ – Build `<PeripheralCard>` (ShadCN `Card` + `Switch`).
- [ ] **P04-S03** ⚡ – Implement drag-and-drop reorder using `@dnd-kit` (`SortableContext`).
- [ ] **P04-S04** ⚡ – Create `<AddPeripheralModal>` (multi-step form, `zod` validation, stepper UI).
- [ ] **P04-S05** ⚡ – Stub `useCalcSnapshot()` hook returning fake metrics per peripheral.
- [ ] **P04-S06** ⚡ – Compose `ProjectProvider` with `useReducer` for add/update/toggle actions.
- [ ] **P04-S07** – Wire FAB → open modal → dispatch addPeripheral → re-render list.

## Phase 5 – Metrics & Graph Panel (ID: **P05**)

> Objective: render charts using mocked data to visualise runtime, voltage drop, power.

- [ ] **P05-S01** – Add ShadCN **charts** via `npx shadcn-ui@latest add charts` (installs Recharts under the hood).
- [ ] **P05-S02** ⚡ – Build `<MetricsTabs>` using ShadCN `Tabs` component.
- [ ] **P05-S03** ⚡ – Implement `VoltageDropChart`, `RuntimeChart`, `PowerHistogram` using ShadCN chart primitives (e.g. `<AreaChart>`, `<BarChart>`) with placeholder series.
- [ ] **P05-S04** ⚡ – Connect charts to `calcOutputs` from context (currently stubbed).
- [ ] **P05-S05** ⚡ – Add spec-limit overlays (e.g. ≤0.5 V red line) to charts.

## Phase 6 – State, Persistence & Calc Engine (ID: **P06**)

> Objective: integrate real calculation engine, local persistence, and Supabase sync.

- [ ] **P06-S01** – Create `packages/core-calc` with pure functions + full test coverage.
- [ ] **P06-S02** – Replace stubbed `useCalcSnapshot` with real memoized selector.
- [ ] **P06-S03** ⚡ – Set up **Dexie.js** (IndexedDB) with `use-live-query` for offline-first storage.
- [ ] **P06-S04** ⚡ – Build Supabase Prisma schema and sync logic (optional for initial MVP).
- [ ] **P06-S05** ⚡ – Implement anonymous session fallback; prompt sign-in for cloud backup.

## Phase 7 – Testing & Quality Gates (ID: **P07**)

> Objective: automated confidence via unit, property, and E2E tests.

- [ ] **P07-S01** – Add **Vitest** config with `happy-dom` for React Testing Library.
- [ ] **P07-S02** ⚡ – Write property tests for `core-calc` (fast-check).
- [ ] **P07-S03** ⚡ – Configure **Playwright** E2E: _add strip → expect runtime chart value_.
- [ ] **P07-S04** ⚡ – Integrate **Chromatic** visual regression for Storybook components.
- [ ] **P07-S05** ⚡ – Gate `main` branch merges via **GitHub Checks**.

## Phase 8 – Polish, Accessibility & Performance (ID: **P08**)

> Objective: refine UX, ensure accessibility compliance, and optimise performance.

- [ ] **P08-S01** ⚡ – Audit with **Lighthouse** (PWA, performance, a11y, best practices).
- [ ] **P08-S02** ⚡ – Add ARIA labels, keyboard navigation, focus traps in dialogs.
- [ ] **P08-S03** ⚡ – Optimise bundle size – code-split ShadCN charts (Recharts), use dynamic imports.
- [ ] **P08-S04** ⚡ – Implement **Sentry** for error tracking (browser + node Lambda if PDF export).
- [ ] **P08-S05** ⚡ – Prepare Vercel preview deploy & production pipeline.
