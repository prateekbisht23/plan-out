# Plan mode

Turn a feature idea, vague or detailed, into modules, a decision log and a task list. One pipeline handles both cases; how much you ask depends on how much the user already said.

1. Intake and playback
2. Clarify
3. Propose modules
4. Write
5. Verify and hand over

Read `questioning.md` first if you have not; it governs steps 1 and 2.

## 1. Intake and playback

Read everything the user gave you, plus any existing docs and relevant code. Then judge the input:

- **Vague**: a sentence or two, no flows, no data, no constraints ("I want users to upload files and have an AI summarise them").
- **Detailed**: covers most of what is being built, where it lives, the main flows, data, and constraints.

Either way, say back what you understood **before anything else**, in plain language and no more than about eight short lines: who it is for, what they can do, what happens behind the scenes in simple terms, and what is explicitly not included. Then ask: "Is this right? What did I get wrong or miss?"

Why first: if the understanding is wrong, every question and document built on it is wasted. The user may also have worded something badly; this is their chance to fix it cheaply. Do not proceed until they confirm or correct. If they correct, play the corrected version back briefly.

## 2. Clarify

Find the gaps by walking this checklist against what is confirmed. Skip what does not apply.

- Users and permissions: who does what, who must not
- Core flows: the happy path and the main alternatives, step by step
- Data: what is stored, where, how long, who may see it
- Interfaces: screens, APIs, events, integrations with other systems
- Failure and edge cases: what goes wrong, what the user sees, what is retried
- Scale and performance: volumes, latency expectations, growth
- Security and privacy: sensitive data, abuse, audit needs
- Operations: configuration, observability, rollout, migration of existing data
- Out of scope: what this deliberately does not do

For a vague request expect many rounds (one topic at a time). For a detailed one, ask only about real gaps and about anything in the input that seems to contradict itself; point out the contradiction instead of choosing a side.

Check the existing codebase for constraints that narrow the choices (framework, database, auth already in place). Propose within them.

Keep a running list of decisions as the user answers. Do not write files yet; boundaries first.

## 3. Propose modules

Propose how to split the feature, and get approval before writing. For each module give: name, one-line scope, what it owns, what it deliberately does not, and what it depends on.

A good split:

- Each module has **one reason to change**. If a change to "how files are stored" and a change to "who may upload" would hit the same file, they are probably tangled.
- Each fact has a natural owner. When two modules both seem to need the same fact, either one owns it and the other links, or the fact deserves its own module. Never let both describe it.
- Interfaces between modules are owned by the **provider**; consumers link to them.
- A module is readable in about five minutes. If it passes roughly 300 lines, split it. Most features land between three and eight modules.
- Cross-cutting concerns (auth, errors, logging) get their own module when several modules depend on them, so the rule is stated once.

Show the proposal as a short list, not prose. Invite the user to merge, split, rename or move things. Reuse existing modules instead of creating near-duplicates when `docs/modules/` already exists; link to what is there.

## 4. Write

Order matters because later files cite earlier ones.

1. **Decisions first.** Create the entries for everything decided so far in `decisions.md`, with ids, owners (the module section that will state them), dates (today), `basis`, and `why`. Use `assets/templates/decisions.md`.
2. **Modules.** One file per approved module from `assets/templates/module.md`. Write each as a description of the system as it will be, in the present tense. State each owned decision where it belongs and cite it (`[D-003]`). Where a module depends on another's fact, link to the owner's section and say nothing more about it. No checklists and no "we will". Put definitions in `## Glossary`, and only for terms this module owns.
3. **Tasks.** Generate `tasks/<feature>.md` from the modules (`assets/templates/tasks.md`). Walk each module section and ask what has to be built or verified for it to be true. Each task:
   - links to what it implements: `implements: module#section`
   - is small enough to finish and verify in one sitting
   - ends with a `done when:` line that is observable (a test, a response code, a visible result), so no human judgement is needed
   - includes tests, migrations, configuration and rollout work, not just the happy-path code
   - is ordered so that dependencies come first; say so in the title or a `depends on: T-00x` line when order is not obvious

   The feature sections of the modules (flows, validation, data, interfaces) should each be hit by at least one task; the checker warns about sections no task covers.
4. Set every new module's `status` to `planned`; change it to `in-progress` when work starts.

## 5. Verify and hand over

**Autonomy test.** Read each module and the task list as the engineer who has to implement them with nobody to ask. Write down every question you would have to ask. Each one is a gap: take it back to the user (batched, with recommendations) and resolve it before finishing, or record it under `## Open questions` and tell the user it blocks implementation. The aim is that the whole feature can be built without a human in the loop.

Then:

1. Run `scripts/check.py check` and fix everything it reports at the source.
2. Run `scripts/check.py index` to generate `docs/README.md`.
3. Give the user a summary: the modules and one line each, the number of decisions (call out any with `basis: default` or `delegated`, since the user did not choose them), the number of tasks, anything open, and which module to read first.

Invite review of the **modules**, since that is where the content lives. The task list is derived and can be regenerated.
