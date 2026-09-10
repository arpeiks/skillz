# skillz

Shared engineering conventions and workflows, distributed as Claude Code
plugins so every project picks them up without per-project setup.

## conventions

| Skill | Covers |
|---|---|
| `general-conventions` | Environment configuration and git commit discipline |
| `frontend-conventions` | Stack defaults, folder structure, naming, formatting, import order |
| `refine-conventions` | How to add or amend a convention here |

## security

| Skill | Covers |
|---|---|
| `security-audit` | Advisories for the stack, current OWASP and browser guidance, and a review of the implementation against both |

## drizzle

| Skill | Covers |
|---|---|
| `drizzle` | Current official setup and API guidance for Drizzle ORM and Drizzle Kit |

## Install

```
/plugin marketplace add arpeiks/skillz
/plugin install conventions@skillz
/plugin install security@skillz
/plugin install drizzle@skillz
```

If the summary says `Run /reload-plugins to activate.`, run that. Skills are
enumerated at session start, so otherwise start a new session.

## Getting updates

```
/plugin marketplace update skillz
```

## Making the conventions apply automatically

Claude decides when to load a skill from its description. To make it
unconditional, add these lines to your own `~/.claude/CLAUDE.md`:

```
Before writing, generating, or refactoring code, read the
`frontend-conventions` and `general-conventions` skills and follow them.
They override framework scaffolding defaults.
```

`security-audit` is left on its description — it runs on request, not on every
task.

## Changing a skill

Edit this repo, not the installed copy under `~/.claude/plugins/` — that is a
cache and is overwritten on update. Bump the version in
`.claude-plugin/marketplace.json` and the plugin's own
`plugins/<plugin>/.claude-plugin/plugin.json`, then push.
