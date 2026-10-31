---
name: skills
description: How to install, update, create and modify agent skills, and what belongs in a skill. Load when the user wants to install, change, write or ask about skills.
---

# Skills

Every skill is a git repository checked out as a folder in the agent skills directory, and has at minimum a `SKILL.md` and a `README.md`. Check whether the environment has an addendum skill that defines the hosting location, repository naming and essential skills, and load it alongside this one.

**Read `SKILL.md` first, not `README.md`.** Make agent-facing changes in `SKILL.md`; `README.md` is human-facing setup docs.

**In some harnesses, like Copilot, the skill tool's "Related files" list can omit `SKILL.md` even though the injected content is SKILL.md's.** Don't infer which file was loaded from that list — if in doubt, read both files with your file-viewing tool and compare against what was actually injected.

## Installing and updating

Install by cloning the skill repository into the skills directory, in a folder named after the skill. Update by moving to the default branch and pulling.

## Making changes

Move to the default branch and sync first, then make the change on a branch and open a pull request with the `gh` CLI. Send the user the pull request link and let them review it.

## What qualifies as a skill?

- Reusable knowledge or workflow conventions
- Caveats that agents repeatedly get wrong

## What does NOT belong in a skill?

- User-specific config, paths, or directory structures
- Things a model already knows
- Content based on what the model assumes to be true — base it on actual observed context
- Concrete real-world examples (specific GUIDs, emails, URLs, IDs) — state the rule itself, not a sample value
- A note that a problem used to exist or is now solved ("X no longer happens"), unless it changes what to do — skills are read cold, so keep the remedy and drop the status update.
- Version, dates or maintainer footers in `SKILL.md` — git history is the audit log, and the footer only costs context on every load
- CLI command examples (PowerShell, Bourne shell, etc.) — LLMs know these, so keep the skill shell- and platform-agnostic. Only include an example when it is really needed, which is rarely; otherwise describe the intent and state only what the model doesn't know, e.g. that `gh repo list` has no `--hostname` flag and needs the `GH_HOST` environment variable instead.

## Writing SKILL.md content: one or two sentences per point, no more

This applies to every addition to `SKILL.md` — a gotcha bullet, a workflow note, a structural fact — not only tool-quirk entries.

"Keep it short" means it, not "keep it shortish." Per point: one sentence for the fact, one for what to do about it. Nothing else.

Cut on sight:
- Any explanation of *why* or *how* something renders/fails/behaves that way — an LLM can already reason that out once it knows the fact.
- A description of what the broken/wrong output looks like.
- Code or markup snippets that only illustrate generic, well-known syntax (e.g. plain HTML tags, common Markdown). Only include a snippet if the exact literal string/value/parameter itself is the non-obvious part.
- Restating the same point twice in different words ("sticky" + "does not switch back" + "retrying doesn't help" is one idea, not three).

Before (too long — mechanism, symptom, and generic markup, all things the model already knows):
> `create_work_item` sets `System.Description`'s format to html regardless of the requested format. Passing Markdown into an html-typed field doesn't get converted — it's stored as literal text (`##`, backticks, `**` show up unrendered, `\n` is ignored since HTML needs `<p>`/`<br>`). This is sticky: retrying with format Markdown later does not switch it back — resend real HTML (`<h2>`, `<p>`, `<code>`, `<ul><li>`) with format Html instead.

After (the two facts that are actually surprising, nothing else):
> `create_work_item` does not default `System.Description` to Markdown despite the tool schema's claim — pass `format: "Markdown"` explicitly. The stored format is sticky: once a field is `html`, a later update with `format: "Markdown"` won't convert it back — check `multilineFieldsFormat` and match your content to it.

If an entry doesn't fit in 2-3 lines, it's not being terse enough — cut, don't reformat into a longer paragraph.
