---
name: refine-conventions
description: >-
  Record or amend a convention in the shared conventions plugin. Use when the
  user corrects a style choice, states a preference worth keeping, or asks to
  refine their conventions. Also runs on demand as /refine-conventions.
---

# Refine the conventions

These skills are distributed as a plugin, so the installed copy under
`~/.claude/plugins/` is a **cache**. Editing it changes nothing for anyone and
is lost on the next update.

Edit the source clone of the `skillz` repo instead — ask the user for its path
if it is not obvious. Changes reach the team when they are pushed and each
member updates the marketplace.

## Steps

1. **Read the target skill first.** Never write it blind; it accumulates
   across projects and people.

2. **Pick the right skill.** Language- and stack-specific rules go in
   `frontend-conventions`. Rules that hold everywhere — git, process,
   review — go in `general-conventions`.

3. **Identify the candidate convention** from what actually happened: a
   correction the user made, a preference stated, a pattern repeated across
   files. Quote the evidence.

4. **Filter.** Only record it if it is:
   - **General** — applies beyond the current project. A one-off decision is
     not a convention.
   - **Not already enforced by tooling.** If prettier or pyramid-sort fixes it
     automatically, no rule is needed.
   - **Not already present.** If it refines an existing rule, edit that rule
     in place rather than appending a near-duplicate.

5. **Show the exact diff** and the evidence that prompted it. Wait for
   confirmation before writing.

6. **Write in the file's existing voice** — imperative, concrete, short code
   example where it clarifies. The file must stay short enough to be read.

7. **Bump the `version` in both** `.claude-plugin/marketplace.json` and
   `plugins/conventions/.claude-plugin/plugin.json` so teammates see an update.

8. **Follow `general-conventions` when committing** — stage nothing wholesale,
   keep the message short, no co-author trailer.

## Removing conventions

A convention contradicted twice is a bad convention. Propose deleting or
rewriting it rather than layering exceptions on top.
