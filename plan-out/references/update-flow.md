# Update mode

Use this when the plan changes after the docs exist: a decision is reversed, a requirement is added, a mistake is found, a detail is refined. Updates are where docs usually start contradicting themselves, so the order of operations matters more than the edits.

The principle: **find the owner of the fact, change it there, and let everything else follow by link.** Because other modules link instead of restating, a correct update touches few places.

## 1. Understand the change

Restate the change in plain language and confirm it, the same way as in planning ("So the upload limit goes from 10 MB to 25 MB, and nothing else about uploads changes. Right?"). If the request is vague, ask using `questioning.md` until the change is exact. An update that is itself a new feature (new behaviour, not a change to existing behaviour) is planning: use `planning.md` and add modules.

## 2. Find the owner and the blast radius

1. Find the decision or section that currently states the fact. Search `decisions.md` and the modules (grep for the term, not just the filename). If the fact has **no owner** or appears in several places, stop: that is already a contradiction or a gap. Tell the user, ask which version is right, and fix the ownership as part of this update.
2. Run `scripts/check.py impact <module>#<section>` for each section involved. It lists:
   - decisions owned there
   - active tasks implementing it
   - other modules that link to it or cite its decisions
3. Show the user a short impact summary ("this touches storage#retention, one decision, two open tasks, and file-uploads links to it") and ask any questions the change raises. A change that does not fit an existing module's scope means a new module may be needed; propose it and get approval before creating it.

## 3. Apply, in this order

1. **Decision.** Add a new entry for the new decision. On the old one set `status: superseded` and `superseded_by: D-new`; on the new one set `supersedes: D-old`. Never edit the old entry's `decision` text. If the change is only a clarification that does not alter the meaning, you may edit the entry's wording but record that in a line under `why`.
2. **Owner section.** Edit the smallest span in the owning module, and change its citation to the new id. Do not reflow, reorder or rephrase the rest of the file, because every extra changed line is a place for an error and makes review harder.
3. **Dependents.** Look at each place `impact` listed. Because they link rather than restate, most need no edit. If one restates the old fact, that is a violation of one-owner-per-fact: replace the restatement with a link instead of updating the copy.
4. **Tasks.**
   - If an active task file covers the affected sections, add or edit tasks there and add `review: spec changed by D-new` to tasks that are now questionable (the checker warns until they are resolved).
   - If the module is `implemented` and its tasks are archived, **do not reopen the archive**. Create a new small `tasks/<feature>-<short-change-name>.md` containing only the work the change requires, and set the module's status back to `in-progress`.
5. **Status.** Update module `status` if it changed meaning (`implemented` to `in-progress` when new work is needed).

Make edits with targeted replacements, not by regenerating files.

## 4. Verify

1. Run `scripts/check.py check`. Typical failures after an update: a module still cites the superseded decision (`stale-decision`), the new decision's owner section does not cite it, a link points to a renamed heading. Fix at the source.
2. If you renamed a heading, every link and `implements:` pointing at it breaks; `impact` before renaming prevents surprises. Prefer not renaming.
3. Regenerate the index if module statuses changed: `scripts/check.py index`.

## 5. Report

Tell the user, briefly: the decision added and which it superseded, the sections changed, tasks added or flagged for review, and anything you could not settle. Offer to show the diff.

## When all tasks are done

When every task in `tasks/<feature>.md` is checked off, run `scripts/check.py archive <feature>`. It refuses if anything is open, moves the file to `tasks/archive/<feature>-<date>.md`, marks modules `implemented` when no other open task points at them, regenerates the index and re-runs the check. Archived tasks are frozen history and are ignored by every check.
