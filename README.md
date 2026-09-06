# skillz

Shared engineering conventions, distributed as a Claude Code plugin so every
project picks them up without per-project setup.

| Skill | Covers |
|---|---|
| `general-conventions` | Git commit discipline — staging, messages, history |
| `frontend-conventions` | Stack defaults, folder structure, naming, formatting, import order |
| `refine-conventions` | How to add or amend a convention here |

## Install

```
/plugin marketplace add arpeiks/skillz
/plugin install conventions@skillz
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

## Changing a convention

Edit this repo, not the installed copy under `~/.claude/plugins/` — that is a
cache and is overwritten on update. Bump the version in
`.claude-plugin/marketplace.json` and `plugins/conventions/.claude-plugin/plugin.json`,
then push.
