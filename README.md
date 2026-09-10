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

## better-auth

| Skill | Covers |
|---|---|
| `better-auth-best-practices` | Server and client configuration, adapters, sessions, plugins, and environment variables |
| `create-auth` | End-to-end Better Auth setup for new and existing applications |
| `email-and-password-best-practices` | Email verification, password reset, policies, and hashing |
| `organization-best-practices` | Organizations, teams, invitations, roles, and permissions |
| `two-factor-authentication-best-practices` | TOTP, OTP, backup codes, trusted devices, and 2FA sign-in flows |

The plugin adapts the [official Better Auth skills](https://github.com/better-auth/skills)
for Bun and includes Better Auth's `llms.txt` index for choosing documentation
that matches the installed release line.

## Install

```
/plugin marketplace add arpeiks/skillz
/plugin install conventions@skillz
/plugin install security@skillz
/plugin install drizzle@skillz
/plugin install better-auth@skillz
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
