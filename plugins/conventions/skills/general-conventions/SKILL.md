---
name: general-conventions
description: >-
  General engineering conventions shared by frontend and backend projects,
  covering safe results for fallible functions, validated environment
  configuration, and git commit discipline. Read when handling throwing
  operations, configuring environment variables, or changing git history.
---

# General conventions

## Fallible functions

- Define these helpers once in a shared `result.ts`.
- Use `safe` for synchronous functions that can throw, such as `JSON.parse`.
- Use `safeAsync` for asynchronous functions that can reject.
- Handle the returned result by checking `ok`. Do not write `try/catch` at call
  sites; only the shared helpers catch errors.

```ts
export type Result<T> =
  | { ok: true; data: T; error: null }
  | { ok: false; data: null; error: Error };

const toError = (error: unknown) =>
  error instanceof Error ? error : new Error(String(error));

export const safe = <T>(run: () => T): Result<T> => {
  try {
    return { ok: true, data: run(), error: null };
  } catch (error) {
    return { ok: false, data: null, error: toError(error) };
  }
};

export const safeAsync = async <T>(
  run: () => Promise<T>,
): Promise<Result<T>> => {
  try {
    return { ok: true, data: await run(), error: null };
  } catch (error) {
    return { ok: false, data: null, error: toError(error) };
  }
};
```

```ts
const parsed = safe(() => JSON.parse(value));
const response = await safeAsync(() => fetch(url));
```

## Environment variables

- Define environment variables in a dedicated `env.ts`, placed in the
  project's appropriate shared configuration or utilities directory.
- Always use Zod to coerce, apply defaults, and validate the runtime's
  environment source once. Export the parsed `env` object and use it throughout
  the application.
- Do not read `process.env`, `import.meta.env`, or another raw environment
  source outside `env.ts`.
- Do not add a `loadConfig()` wrapper unless it performs configuration work
  beyond returning the validated environment object.

```ts
import { z } from "zod";

const envSchema = z.object({
  NODE_ENV: z.enum(["development", "test", "production"]).default("development"),
  PORT: z.coerce.number().int().positive().default(3000),
});

export const env = envSchema.parse(process.env);
```

## Documentation

- `README.md` and files under `docs/` carry a **general overview** — what the
  project is, how to run it, where things live. Implementation detail belongs
  in the code or in a dedicated doc, not in the overview.
- **Never hardcode a configurable value** in prose — ports, hosts, URLs.
  Name the variable it comes from: "listens on the port given by `PORT`",
  not "listens at `http://127.0.0.1:3000`".

## Commits

- Commit **only what is already staged**. Never run `git add -A`, `git add .`,
  or `git commit -a` — unstaged changes are left unstaged on purpose.
- Keep commit messages as short and concise as possible.
- Never add a co-author trailer (no `Co-Authored-By:` lines).
- Never rewrite a commit that has already been pushed. No `reset`, `amend`, or
  force push on pushed history — fix it forward with a new commit.
