---
name: frontend-conventions
description: >-
  Frontend conventions for JavaScript and TypeScript projects: default stack
  when initializing a project, folder structure, file naming, import ordering
  (pyramid sort), formatting toolchain, component naming, exports, and
  internal import aliases. Read before writing or scaffolding frontend code.
---

# Frontend conventions

## Project initialization

When asked to initialize a frontend project, default to:

- **TanStack Start**
- **TanStack Router**
- **TanStack Query** (integrated)
- **Tailwind CSS 4**

Always use **Bun**.

## Folder structure

```
src/
├── components/
└── container/
    └── router/
        ├── shell.tsx
        ├── component.tsx
        └── not-found.tsx
```

- `shell.tsx` — the base router HTML and scripts.
- `component.tsx` — contains the outlet.
- `not-found.tsx` — 404, redirects to the root route.

`components/` is for shared, feature-agnostic UI. `container/<feature>/` is
feature UI split into small files. Third-party wiring (providers, devtools)
goes in `integrations/<library>/`.

## File naming

Always use lowercase kebab-case.

## Component naming

Components are PascalCase and prefixed with their container, so the symbol
reads unambiguously at the import site: a component in `container/router/` is
`RouterShell`, `RouterComponent`, `RouterNotFound`.

## Exports

Prefer const arrow functions — `export const RouterShell = () => {}`, not
`export function RouterShell() {}`. Use a default export only where a
library's contract demands one.

Arrow functions are not hoisted, so a component must be declared before
anything references it. See **Route files**.

When the body is a single expression, drop the block and the `return` and keep
it on one line. Prettier wraps it if it passes 80 columns.

```tsx
const Home = () => null
const double = (n: number) => n * 2
const Badge = () => <span className="badge" />
```

## Route files

Keep route files thin: define the component in the same file, unexported, then
export the route below it. The component comes first because a `const` is not
hoisted — referencing it above its declaration throws at module evaluation.

```tsx
const Home = () => <div className="p-8">…</div>

export const Route = createFileRoute('/')({ component: Home })
```

## Internal imports

Use the package's subpath alias rather than deep relative paths. Declare it in
`package.json` so the runtime honors it, not just tsconfig:

```json
"imports": { "#/*": "./src/*" }
```

Then `import { RouterShell } from '#/container/router/shell'`.

## Formatting is done by tools, never by hand

Write roughly-right code and let the toolchain settle it. Two tools run
together, in this order:

1. `prettier --write` — whitespace, quotes, semicolons, wrapping
2. `pyramid-sort` — import ordering
3. `prettier --write` again

The third step is not redundant: pyramid-sort emits semicolons that prettier
strips, so without it the file needs a second pass to stabilise.

```bash
bun add -d prettier pyramid-sort
```

```json
"format": "prettier --write . && pyramid-sort . --sort-all && prettier --write ."
```

Prettier settings — no semicolons, single quotes, trailing commas, 80 columns:

```json
{
  "semi": false,
  "singleQuote": true,
  "trailingComma": "all",
  "printWidth": 80,
  "tabWidth": 2
}
```

## Import order — pyramid

Imports are sorted **by line length, shortest first**, so the block forms a
pyramid. Blank lines between imports are removed; the whole import header is
one block. Do not group by origin (external vs local).

```ts
import { defineConfig } from 'vite'
import tailwindcss from '@tailwindcss/vite'
import viteReact from '@vitejs/plugin-react'
import { devtools } from '@tanstack/devtools-vite'
import { tanstackStart } from '@tanstack/react-start/plugin/vite'
```

`.pyramidsortrc.json`:

```json
{
  "imports": { "groupByEmptyRows": false, "groupExternalLocal": false },
  "attributes": { "groupByEmptyRows": false },
  "types": { "groupByEmptyRows": false },
  "objects": { "groupByEmptyRows": false },
  "css": { "groupByEmptyRows": false }
}
```

Side-effect imports (`import './styles.css'`) keep their position —
reordering them changes module evaluation order.

Add generated files (e.g. `routeTree.gen.ts`) to `.prettierignore`.
