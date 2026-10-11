---
name: plan-out
description: Plan big features as small, scoped spec modules with a decision log and a separate task list, keep those docs consistent when plans change, and clean up docs folders that are cluttered or contradict themselves. Use this skill whenever the user wants to plan, spec, design or break down a feature or system ("plan this feature", "let's grill through the design", "write the architecture docs", "make an implementation plan"), wants to update, change or extend an existing plan or its docs, or complains that their docs are messy, duplicated, outdated or contradict each other ("clean up our docs", "consolidate the ADRs", "which doc is right?"). Use it even when the user never says "plan-out" or "module", including for a vague idea like "I want to add X" when the feature is big enough to need more than one planning conversation.
---

# plan-out

Planning docs written over several sessions drift apart: session 3 changes a decision from session 1, nothing updates session 1's file, and both files now claim to be true. Asking an AI to reconcile them makes it worse, because it has to guess which statement is current and fills gaps with invented detail.

This skill prevents that with structure instead of vigilance. Decisions are small and authoritative, specs are split into modules with exactly one owner per fact, tasks are derived and disposable, and a script (not your impression) decides whether the docs are consistent.

The goal of the finished docs is twofold: a plan an AI agent can implement without asking anyone anything, and lasting documentation a new engineer can learn the codebase from.

## Rules that apply in every mode

1. **One owner per fact.** Each fact (a schema, a flow, a limit, a policy) is stated in exactly one module. Every other module links to it and never restates it. Restating is how two files end up disagreeing.
2. **Decisions have ids and a status.** Each decision is an entry in `decisions.md` with an owner section and a status of `active` or `superseded`. Changing a decision means adding a new entry that supersedes the old one, never editing the old one's meaning. This is what lets anyone, including you in a later session, tell the current truth from history.
3. **Never assume.** Every unknown becomes a question to the user or a logged default (see `references/questioning.md`). A silent assumption is a future contradiction.
4. **Edit surgically.** Change the smallest span that needs changing. Do not rewrite or "tidy" whole files, and do not merge two positions into a blend nobody chose. Rewrites are where invented detail enters.
5. **Specs and tasks stay separate.** Modules describe what the system is and are meant to last. Tasks describe work to do, link one-way to module sections (`implements: module#section`), and are archived when finished. Modules never list their tasks.
6. **The checker is the arbiter.** After any change to the docs, run `scripts/check.py` and fix what it reports at the source. Do not tell the user the docs are consistent until it passes.

## Talking to the user

Write in plain language and short sentences, in whatever language the user writes in. Many users are not specialists in the area being planned. Prefer "where should uploaded files be kept?" over "specify the persistence layer". Explain why you are asking only when it is not obvious. Keep each message small enough to answer from a phone.

## Doc layout

All paths are relative to the docs root (default `docs/`; ask if the project clearly uses something else).

```
docs/
  README.md        generated index (scripts/check.py index); do not hand-edit
  modules/         one file per module: the lasting spec
  decisions.md     decision log
  tasks/           active task lists, one per feature
    archive/       finished task lists, frozen history, ignored by checks
```

Templates for each file are in `assets/templates/`. Read the one you are about to write so the format matches what the checker parses.

## Step 0: pick the mode

Look at what already exists, then at what the user wants.

| Situation | Mode |
| --- | --- |
| `docs/modules/` and `docs/decisions.md` exist and the user wants to change, fix, extend or re-decide something | **Update**: read `references/update-flow.md` |
| The user wants to plan a new feature or system (vague or detailed), with or without existing plan-out docs | **Plan**: read `references/planning.md` |
| Docs exist but are not in this structure and look overlapping, stale or contradictory, and the user asks to clean up or consolidate | **Cleanup**: read `references/cleanup.md` |
| Same unstructured docs exist and the user only asked to plan something new | Offer cleanup first (one short message: what you saw, why it matters, that nothing changes until they approve). If they decline, plan the new feature in the new structure and do not treat legacy docs as authority |

Always read `references/questioning.md` as well. It governs every question you ask in every mode.

## Using the script

`scripts/check.py` is plain Python with no dependencies. Run it from the project root; `--docs` points at the docs root.

| Command | Use |
| --- | --- |
| `python <skill>/scripts/check.py check --docs docs` | Full consistency check. Run after every docs change. Add `--strict` to treat warnings as errors |
| `... impact <module>#<section>` | Before changing a fact: lists the decisions, tasks and linking modules that depend on it |
| `... archive <feature>` | When all tasks in `tasks/<feature>.md` are done: archives them, marks modules `implemented`, regenerates the index, re-checks |
| `... index` | Regenerates `docs/README.md` from module frontmatter |
| `... ledger <file.json>` | Cleanup only: verifies that every extracted claim was accounted for |

`<skill>` is the folder containing this SKILL.md. Errors mean the docs contradict themselves or point at nothing; fix them. Warnings (unresolved questions, sections no task covers, placeholders) are things to either fix or tell the user about.

## Finishing any task

Run the checker, then give the user a short summary: what changed, the decisions added or superseded, anything still open, and what to review first. If the checker fails and you cannot fix it without a decision from the user, say so plainly and ask. Do not paper over it.
