---
name: drizzle
description: >-
  Set up, configure, or use Drizzle ORM and Drizzle Kit. Read before changing
  Drizzle schemas, queries, database connections, configuration, or migrations.
---

# Drizzle

Start with Drizzle's current documentation index:

- [Drizzle LLM index](https://orm.drizzle.team/llms.txt)

Treat it as an index, not as the implementation guide. Follow only the official
links relevant to the task after identifying:

- the installed Drizzle version, if the project already uses Drizzle;
- the SQL dialect;
- the database driver and runtime;
- whether the database and project are new or existing.

Do not transfer APIs, imports, configuration, or migration commands between
dialects, drivers, or major versions without verifying them in the applicable
official page. If the project does not reveal a choice that materially changes
the setup, ask before selecting one.

Preserve the project's package-manager, environment-validation, file-layout,
and generated-code conventions. Do not apply migrations to a shared or
production database unless the user explicitly asks for that external change.
