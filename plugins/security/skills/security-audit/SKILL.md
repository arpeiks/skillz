---
name: security-audit
description: >-
  Audit this project against current published security guidance and
  advisories. Runs the project's own checks, fetches live advisories for its
  dependencies, reviews the implementation, and reports concrete changes. Use
  when asked to run a security audit or check for security updates.
---

# Security audit

Answer from fetched sources, never from memory. Every advisory and guideline
below moves faster than any model's knowledge cutoff, and stale security
guidance is worse than none — it reads as current.

Work through the sections in order. Say plainly when one produces nothing.

## 1. The project's own checks

Look for an audit or security script in `package.json` and run it. Run the
dependency audit for the package manager in use — `bun audit`, and prefer
`--audit-level=high` to keep the signal high.

A failing check is a regression, not a recommendation. Report those first and
separately from everything below.

## 2. Advisories for this stack

Check published advisories for each dependency in the manifest, plus the
runtime itself. `gh api /repos/<owner>/<repo>/security-advisories` where the
repository is known; the GitHub Advisory Database otherwise.

Then check the CISA Known Exploited Vulnerabilities catalog for anything in the
stack. A KEV entry means confirmed exploitation in the wild — it outranks any
severity score and is acted on immediately.

## 3. Current guidance

- OWASP API Security Top 10 for an API — this is a different list from the web
  Top 10, and its top entries are authorization failures, not injection
- OWASP Cheat Sheet Series pages covering whatever changed since the last audit
- Mozilla Web Security Guidelines for transport and header posture
- WebKit and Chromium blogs for cookie and browser policy changes — these break
  working authentication with no code change on the project's side

## 4. Review the implementation

Read the code. Do not assume it still matches the last audit.

- Response headers, and whether they reach every response — error paths and
  mounted sub-handlers included, not just matched routes
- Environment and input validation: bounds, coercion, anything trusted unparsed
- Authorization per object, not per endpoint. The top OWASP API risks are all
  authorization failures, and no header, CORS policy, or framework setting
  prevents one
- Anything cacheable: confirm the response is identical for every caller before
  a shared cache is allowed to hold it
- Secrets and tokens in logs, error bodies, URLs, and referrers

## 5. Respect the project's recorded decisions

Before reporting, read `docs/security.md` for decisions already made deliberately
— headers omitted because nothing serves HTML, a CORS layer absent because
everything is same-origin, TLS handled at a proxy. That file is the record; a
README is an overview and is not where these live.

Do not re-flag those. Automated scanners will, because they grade every service
as though it were an HTML site. Flag one only when its premise no longer holds,
and say which premise changed.

If the file is missing or a decision is undocumented, note it and propose the
entry. An undocumented omission and a deliberate one look identical from the
outside, and the difference is worth writing down once.

## Report

Rank by severity. For each finding give `file:line`, what an attacker gains,
and the concrete change. Keep confirmed problems in this code separate from
hardening suggestions; conflating them buries the ones that matter.
