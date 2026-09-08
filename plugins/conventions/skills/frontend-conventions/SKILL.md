---
name: frontend-conventions
description: >-
  Frontend conventions for JavaScript and TypeScript projects: default stack
  when initializing a project, which UI component library to reach for, folder
  structure, naming of files, components and functions, exports, internal
  import aliases, import ordering (pyramid sort), the formatting toolchain, and
  how generated code is kept out of it and extended instead. Read before
  writing or scaffolding frontend code.
---

# Frontend conventions

## Project initialization

When asked to initialize a frontend project, default to:

- **TanStack Start**
- **TanStack Router**
- **TanStack Query** (integrated)
- **Tailwind CSS 4**
- **coss ui**

## Package manager

Always **Bun** — never npm, yarn, or pnpm. That covers every form:

| Instead of | Use |
| --- | --- |
| `npm install` | `bun install` |
| `npm i <pkg>` | `bun add <pkg>` |
| `npm run <script>` | `bun run <script>` |
| `npx <cmd>` | `bunx <cmd>` |

Docs for a library will almost always show the npm form. Translate it rather
than pasting it — a stray `npx` writes a `package-lock.json` and splits the
lockfile.

## UI library

Default to **coss ui** whenever a React project needs a component library.
Reach for shadcn, Material UI, Chakra, or a hand-rolled set only when the user
asks for one by name.

Installing the components is half the job — also install the coss skills into
the project, so the agent works from the real component APIs instead of
guessing at them:

```bash
bunx skills add cosscom/coss
```

That skill carries the imports and composition rules for every primitive, the
Tailwind 4 token and `data-slot` conventions, and the shadcn/Radix → coss
migration rules (`asChild` → `render`, `onSelect` → `onClick`, Select
items-first, ToggleGroup `type` → `multiple`, Slider scalar values).

## Generated code is never edited and never formatted

Anything a registry CLI writes — `shadcn add` output, `routeTree.gen.ts` — is
not project source. Leave it byte-for-byte as generated:

- Never hand-edit it.
- Never format it.

The next `add` overwrites the file, so an edit or a reformat is churn that is
silently lost, and it turns every routine update into a spurious diff.

Excluding it takes **two** mechanisms, because the toolchain is asymmetric.
Prettier reads `.prettierignore`. pyramid-sort has no ignore mechanism at all
— it takes a single path and honours only `.gitignore`, and generated code is
tracked — so it must be handed authored files explicitly:

```sh
# scripts/format.sh — "format": "sh scripts/format.sh"
authored() {
  find src -path 'src/components/ui' -prune -o \
    -type f \( -name '*.ts' -o -name '*.tsx' -o -name '*.css' \) -print
}

prettier --write . --log-level warn
authored | while IFS= read -r f; do pyramid-sort "$f" >/dev/null; done
prettier --write . --log-level warn
```

Keep the prune list and `.prettierignore` in sync. If a format-on-write editor
hook is configured, give it the same exclusions.

## Extending a generated component — the pseudo component

A generated component that needs different behaviour or styling is never
edited in place. Wrap it in a **pseudo component** at
`src/components/<name>.tsx` that extends the generated original.

It must be a **drop-in replacement**: same exported name, same props, same
`ref` behaviour, so switching a call site is a one-line import change and
nothing else has to move.

```tsx
import { cn } from '#/lib/utils.ts'
import type { ComponentProps } from 'react'
import { Button as ButtonPrimitive } from '#/components/ui/button.tsx'

type ButtonProps = ComponentProps<typeof ButtonPrimitive> & { pill?: boolean }

export const Button = ({ pill, className, ...props }: ButtonProps) => (
  <ButtonPrimitive className={cn(pill && 'rounded-full', className)} {...props} />
)
```

Call sites import `#/components/<name>.tsx`; the generated original keeps its
own name at `#/components/ui/<name>.tsx`.

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

## Naming — never prefix with the feature

Components, files, and functions are named for what they are and nothing more.
The folder already carries the feature or module, so repeating it in the symbol
is noise.

```
container/landing/about.tsx     → About
container/router/shell.tsx      → Shell
container/router/not-found.tsx  → NotFound
container/router/component.tsx  → Component
```

Not `LandingAbout`, `RouterShell`, `RouterNotFound`, or `RouterComponent`.
Components stay PascalCase, files stay kebab-case.

The **only** reason to prefix is a real name conflict with another file,
component, or function. Not a hypothetical one, and not because the bare name
reads thin on its own — `Component` is a fine export from
`container/router/component.tsx`. Add the prefix when the collision actually
happens, and only to the symbol that has to move.

## Exports

Prefer const arrow functions — `export const About = () => {}`, not
`export function About() {}`. Use a default export only where a
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

Then `import { Shell } from '#/container/router/shell'`.

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
"format": "sh scripts/format.sh"
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

Generated files are excluded from both tools — see **Generated code is never
edited and never formatted**.
