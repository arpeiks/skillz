---
name: general-conventions
description: >-
  General engineering conventions that apply to every project and language,
  covering git commit discipline: what may be staged, commit message style,
  trailers, and rules about rewriting history. Read before staging,
  committing, or changing git history.
---

# General conventions

## Commits

- Commit **only what is already staged**. Never run `git add -A`, `git add .`,
  or `git commit -a` — unstaged changes are left unstaged on purpose.
- Keep commit messages as short and concise as possible.
- Never add a co-author trailer (no `Co-Authored-By:` lines).
- Never rewrite a commit that has already been pushed. No `reset`, `amend`, or
  force push on pushed history — fix it forward with a new commit.
