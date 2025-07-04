LED Power Planner – UI & Feature Specification (Draft v0.1)

1. Product Vision

A responsive web application that lets makers plan complex LED-lighting builds. Users log in, create projects, add hardware peripherals (LED strips, batteries, converters, wiring), tweak parameters (length, brightness, duty-cycle), and instantly see power-budget graphs, voltage-drop simulations, and BOM recommendations – all backed by a rigorously-tested calculation engine.

⸻

2. Core User Flow
	1.	Sign In / Sign Up (email + magic-link or passkey)
	2.	Project Dashboard – list of existing projects; button ➜ New Project
	3.	Project Workspace
	1.	Add Peripheral (modal on desktop, drawer on mobile)
	2.	Configure Peripheral (LED count, voltage, current/LED, brightness %, colour temp, diffuser factor …)
	3.	Repeat for batteries, converters, fuses, wire gauges
	4.	Toggle enable/disable peripherals to model variants
	5.	Graphs & Metrics Panel auto-refreshes (runtime, total load, wire loss, brightness)
	4.	Export / Share – PDF report & parts list (future milestone)

⸻

3. High-Level Layout

┌──────────┬───────────────────────────────────────────────┐
│ Project  │  Header (Project name · Save status · Theme)  │
│ Sidebar  ├───────────────────────────────────────────────┤
│ (List of │                                               │
│ configs) │   Config Canvas (scrollable)                  │
│          │   • Peripheral cards (sortable)               │
│          │   • “Add Peripheral” FAB                      │
│          │                                               │
└──────────┴───────────┬───────────────────────────────────┘
                       │ Right Panel – Metrics            │
                       │ ─ Tabs: [Overview] [Voltage drop]│
                       │   [Runtime] [Heat]               │
                       └───────────────────────────────────┘

Mobile: Sidebar collapses into a hamburger sheet; Metrics panel becomes bottom-sheet tabs.

⸻

4. Key Components (ShadCN / Lucide)

Component	UI Element	Notes
ProjectSidebar	Sheet (shadcn/ui)	Shows projects & active configs. Supports drag-n-drop reorder.
PeripheralCard	Card + Switch	Displays summary (type, volts, amps, runtime). Toggle enables/disables in calc engine.
AddPeripheralModal	Dialog (desktop) / Sheet (mobile)	Multi-step form; dynamic fields based on peripheral type; Zod validation.
MetricsTabs	Tabs + CardContent	Wraps charts (runtime curve, V-drop over length, power histogram).
Graph	Recharts LineChart / BarChart	Pure presentational – gets pre-computed data from core-calc.
TopNav	Navbar	Theme switch, account menu.


⸻

5. State & Data Flow
	•	Core-calc package exposes pure functions.
	•	React Context (ProjectProvider) holds projectState:

{
  peripherals: Peripheral[],
  enabledIds: string[],
  calcOutputs: CalcSnapshot // derived via useMemo
}


	•	Mutations (add, update, toggle) dispatched via useReducer actions; triggers re-compute.
	•	Persistence: IndexedDB via use-live-query, synced to Supabase when authenticated.

⸻

6. Routing (Next 14 App Router)

/ (redirect→/projects)
/projects
/projects/[id]
/projects/[id]/settings

Dynamic segments use React Server Components; calc engine & charts run on client.

⸻

7. Graph Definitions & Samples

Tab	X-Axis	Y-Axis	Notes
Voltage drop	Run length (m)	ΔV (V)	Highlight spec-limit (e.g. ≤0.5 V)
Runtime	Brightness (%)	Hours	Shade region where battery SOC <20 %
Power	Peripheral	Watts	Stacked bar (on/off reflects enable state)


⸻

8. Testing Strategy
	•	core-calc: Vitest unit + property tests.
	•	E2E: Playwright scenarios (mobile & desktop) – add strip ➜ expect accurate runtime.
	•	Visual: Chromatic for component regressions.

⸻

9. Open Questions
	1.	Should we allow anonymous projects (local-only) pre-sign-in?
	2.	Do we store peripheral templates server-side so users can pick from a catalog?
	3.	PDF export pipeline – serverless (Puppeteer) or client pdfmake?

⸻

Next milestone: build PeripheralCard, AddPeripheralModal, and wire them to a stubbed core-calc hook so we can exercise the layout with fake data.