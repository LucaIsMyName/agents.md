# scaffolding-a-react-app.md

## Purpose

This file defines how to create a **clean, modern, and production-ready way to scaffold a React app** using best practices.
It ensures consistency, scalability, and maintainability from the very beginning.

---

## React App Scaffolding

Scaffold in this order. All tools are required unless marked optional.

---

### Setup

```bash
npm create vite@latest my-app -- --template react-ts
npm install -D tailwindcss @tailwindcss/vite
npm install clsx tailwind-merge
npm install react-router
npx shadcn@latest init
```

---

## Vite + TypeScript

- Use `@/` as a path alias mapped to `src/` in both `vite.config.ts` and `tsconfig.json`.
- `tsconfig.json` must include:
  - `"strict": true`
  - `"noUnusedLocals": true`
  - `"noUnusedParameters": true`

### Rules

- Never use `any`
  - Use `unknown` and explicitly narrow it

- Avoid non-null assertions (`!`)
  - Prefer optional chaining (`?.`) or guards

👉 Type safety is not optional — it prevents bugs early.

---

## Tailwind

- Import using:
  - `@tailwindcss/vite` plugin
  - `@import "tailwindcss"` in `src/index.css`

### Rules

- Always compose classes using `cn()` from `@/lib/utils`
  - (combines `clsx` + `tailwind-merge`)

- Never use inline styles (`style={{}}`) for things Tailwind supports, only for setting dynamic values when needed
- Follow **mobile-first design**
  - Base styles = mobile
  - Use `sm:`, `md:`, `lg:` for larger screens

👉 Consistency in styling = faster development + cleaner UI.

---

## ShadCN + Base-UI

- `components.json` must live in the **project root**
- Add components using:

  ```bash
  npx shadcn@latest add <name>
  ```

- Generated components go into:

  ```
  src/components/ui/
  ```

### Rules

- Never rebuild components that already exist in ShadCN
- Use Base-UI only for **unstyled primitives** not covered by ShadCN:

  ```bash
  npm install @base-ui-components/react
  ```

👉 Prefer composition over reinvention.

---

## React Router

- Define all routes in:

  ```
  src/app/router.tsx
  ```

  using `createBrowserRouter`

### Rules

- Use layout routes with `<Outlet />`
- Do NOT scatter `<Route>` definitions across the app
- Use URL/search params for:
  - filters
  - pagination

- Lazy-load heavy pages:

  ```ts
  React.lazy(() => import("@/modules/..."));
  ```

👉 Routing should be centralized and predictable.

---

## TanStack Query (optional — for server data)

```bash
npm install @tanstack/react-query
```

### Setup

- Wrap app in `<QueryClientProvider>` inside `src/main.tsx`

### Rules

- `queryKey` must always be an **array**
- Never fetch data with `useEffect` + `useState`
  - Use `useQuery` instead

- Use `useMutation` for writes
  - Invalidate queries in `onSuccess`

👉 Server state ≠ UI state. Treat it differently.

---

## TanStack Virtual (optional — large lists)

```bash
npm install @tanstack/react-virtual
```

### Rules

- Use `useVirtualizer`
- Scroll container must have:
  - fixed height
  - `overflow: auto`

- Do NOT add unless needed (100+ items)

👉 Optimize only when necessary.

---

## TanStack Table (optional — complex tables)

```bash
npm install @tanstack/react-table
```

### Rules

- Define `columns` **outside** the component
- Only include row models you actually use:
  - `getSortedRowModel`
  - `getPaginationRowModel`
  - etc.

👉 Avoid unnecessary computation and re-renders.

---

## React Helmet Async (optional — SEO)

```bash
npm install react-helmet-async
```

### Setup

- Wrap app in `<HelmetProvider>` (above router)

### Rules

- Every public page must include:
  - `<title>`
  - `<meta name="description">`

- Skip for:
  - dashboards
  - internal tools
  - non-indexed apps

👉 SEO only matters when content is public.

---

## Provider Order (`src/main.tsx`)

```
StrictMode > HelmetProvider > QueryClientProvider > RouterProvider
```

---

## Basic Folder Structure

```
/
├── src/                    # Application source code
│   ├── app/                # App bootstrap / main wiring (routing, providers, entry setup)
│   ├── modules/            # Feature modules grouped by business domain
│   │   └── <feature>/      # Feature-local components, logic, tests
│   ├── components/         # Reusable UI/presentation components
│   ├── services/           # External integrations (APIs, storage, third-party services)
│   ├── lib/                # Shared utilities/helpers (pure, reusable)
│   ├── config/             # Runtime config parsing, env mapping, constants
│   ├── styles/             # Global styles/design tokens
│   ├── assets/             # Static assets imported by code
│   └── types/              # Shared type/domain definitions when needed
│
├── tests/                  # Integration/e2e/high-level test suites (if separate from src)
├── scripts/                # Build/release/maintenance scripts
├── public/                 # Static public files
├── docs/                   # Architecture notes, ADRs, runbooks
├── .env.example            # Required environment variables (no secrets)
├── README.md               # Setup, commands, architecture, conventions
└── agents.md               # This file
```

---

## Final Principles

- Start simple, scale intentionally
- Prefer conventions over custom setups
- Avoid premature optimization
- Keep everything predictable and centralized
- Do not use `purple`, `indigo` or `violet` as brand/primary/accent color when setting up taiwlind/CSS, prefer `blue`, `sky`, `teal`, `red`, `orange` - or simply ask the user - when picking a brand color at the beginning