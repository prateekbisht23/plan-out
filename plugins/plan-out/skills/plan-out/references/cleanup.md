# Cleanup mode

Use this when a docs folder has grown overlapping, stale or contradictory files and the user wants it consolidated into the plan-out structure.

This is the same problem the skill exists to prevent, so the same danger applies: if you "fix" cluttered docs by rewriting and merging them, you will invent detail and silently pick winners. Cleanup is therefore **extractive, not generative**.

- You move existing text, you do not rewrite it.
- You never decide which of two conflicting statements wins. The user does.
- Nothing is dropped unless the user approved dropping it.
- A script verifies all of the above at the end.

Work on a git branch (`git switch -c docs/plan-out-cleanup`) with one commit per step so everything is reversible. If the folder is not under git, tell the user and ask them to copy it somewhere first.

## Phases

Steps 1 to 5 are a **dry run**: they only read, and write a report plus the ledger under `.plan-out/`. Nothing in `docs/` changes until the user approves the mapping in step 5.

### 1. Inventory

List every file under the docs root (and any other doc-like files the user points to). Classify each as: spec, decision/ADR, diagram, plan/to-do, runbook/operations, or stale/duplicate. Note format, size, last git change.

Several things cannot be guessed and are cheap to ask. Ask them now, in one batch with the facts you observed, rather than assuming:

- What does a folder like `target/` mean here (target-state design, build output, something else)?
- Are `.html` files hand-written, or generated from sibling `.md` files? (If generated, they are derived, not sources.)
- Are two ADR folders the same set of decisions, or different ones?
- Which files, if any, are known to be obsolete? Which are known to be authoritative?
- Is anything consumed by tooling (links from code, a docs site config, CI) that must keep its path?

A wrong guess here poisons the whole migration.

### 2. Extract claims

Go file by file. For each, extract the **atomic claims**: individual facts, rules, decisions and constraints, each as the original verbatim text (a sentence, a list item, a table row). Skip pure navigation, headings with no content, and boilerplate.

Write each to the ledger as you go, not at the end, so a long folder does not overflow your context. `.plan-out/ledger.json`:

```json
{"claims": [
  {"id": "C-001", "source": "docs/api.md:42", "text": "A single file may be at most 25 MB."},
  {"id": "C-002", "source": "docs/adr/007-limits.md:9", "text": "Uploads are limited to 10 MB."}
]}
```

Never paraphrase `text`. If you cannot extract it verbatim, it is not a claim you can safely move.

### 3. Cluster and detect conflicts

Group claims by topic (by the module they would belong to). Within each group look for:

- **Contradictions**: two claims that cannot both be true (the 25 MB and 10 MB above)
- **Duplicates**: the same fact stated more than once, possibly in different words
- **Stale items**: references to things that no longer exist (check the code), superseded plans, finished to-do lists
- **Gaps**: a topic touched in some files and left undecided

Mechanical help: grep for repeated headings and defined terms; compare numbers and names across files. Semantic contradictions need your reading, but treat a suspected conflict as a question for the user, never as something to resolve yourself.

### 4. Conflict review with the user

Present conflicts in small batches, one topic at a time, in plain words:

> **Upload size limit.** Two files disagree.
> A. 25 MB, in `docs/api.md` line 42 (edited 3 weeks ago)
> B. 10 MB, in `docs/adr/007-limits.md` line 9 (edited 5 months ago)
> The code currently enforces 25 MB (`upload.ts:31`). Which is right?

Dates, git history and what the code does are **evidence to show**, not authority. The user decides. If they say "newer wins" or "you decide", apply that rule openly and log each decision with `basis: delegated` or `basis: code`. If they skip, log the evidence-backed option with `basis: default` and say so in the summary.

Each resolution becomes a decision entry: the winner `active`, the loser `superseded` with `superseded_by`, so the history is kept.

### 5. Propose the mapping and get approval

Produce `.plan-out/report.md` and show the user a short version:

- Proposed modules (names, scopes, owned facts) using the rules in `planning.md` section 3
- An old-file to new-location map: which claims from which files land in which module, which become decisions, which tasks
- What would be deleted
- Counts: claims total, per disposition, conflicts resolved, anything still open

Do not touch `docs/` until the user says go.

### 6. Migrate

Now write the new structure. Rules:

- **Move text verbatim** into the owning module section. The only text you may add is structure: headings, the frontmatter, and short connective lines that state no facts. If a sentence needs rewording to fit (for example a pronoun with no antecedent), keep the original and ask the user, or mark the claim `"reworded": true` in the ledger with their approval.
- Each fact appears once. Duplicates elsewhere are replaced by a link to the owner, and the duplicate's claim is recorded against the owner's location.
- Losing sides of conflicts go in the decision log as `superseded`, not into modules.
- Create decision entries, then modules, citing decisions with `[D-nnn]`, using the templates in `assets/templates/`.
- **Diagrams and HTML.** Generated files (derived from `.md`) are not sources: regenerate or drop them with the user's say-so. Hand-written diagrams are kept under `docs/assets/` and linked from the module that owns what they show.
- **Old plans and to-do lists.** If work remains, convert it to a task file linked to the module sections. If the work is done, the claims go to modules and the plan itself is dropped.
- Keep paths that tooling depends on (asked in step 1) or leave a one-line pointer file there.

For each claim, set its ledger fields as you place it:

```json
{"id": "C-001", "source": "...", "text": "...", "disposition": "module", "target": "file-uploads#validation"}
{"id": "C-002", "source": "...", "text": "...", "disposition": "decision", "target": "D-002"}
{"id": "C-017", "source": "...", "text": "...", "disposition": "dropped", "reason": "obsolete: service removed in v3", "approved": true}
```

`approved: true` on a drop means the user said yes to dropping that specific claim (or that class of claim). Never set it yourself.

### 7. Verify

Run both:

```
python <skill>/scripts/check.py ledger .plan-out/ledger.json --docs docs
python <skill>/scripts/check.py check --docs docs
```

The ledger check fails when any claim has no disposition, a target that does not exist, text that is not found verbatim in its target, or a drop without approval. Fix every failure at the source; do not edit the ledger to make it pass. A claim you cannot place is a question for the user.

### 8. Remove the old files

Once both pass and the user has looked at the result, **delete the old files** and rely on git history. A leftover `legacy/` folder recreates the clutter and the contradictions. Then delete `.plan-out/`, regenerate the index (`check.py index --force` replaces an old hand-written README after the user agrees), and give the user the summary: modules created, decisions logged (flag those with `basis: default` or `delegated`), claims moved, claims dropped with approval, and how to recover anything from git.

## Pitfalls

- **Don't start merging early.** Extraction and conflict review exist to stop you from deciding things silently. The temptation to fix as you read is the failure mode.
- **Don't trust file dates blindly.** A recently touched file can be a reformat; an old ADR can still be true. Show dates as evidence only.
- **Don't let context overflow.** Process one file or one topic at a time, persist to the ledger, and re-read from disk rather than from memory.
- **Don't clean up the user's code or comments.** Scope is documentation only.
