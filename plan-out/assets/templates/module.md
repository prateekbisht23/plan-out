---
module: <slug-matching-the-file-name>
status: planned
summary: <one line a newcomer can read in the index>
---
# <Module title>

<!--
Rules for this file (delete this comment when writing a real module):
- Describe the system as it WILL be (present tense). No checklists, no "we will".
- Every fact lives in exactly one module. If another module owns it, link to it
  ([storage rules](storage.md#object-storage)); never restate it.
- State each decision where this module owns it and cite it: "... [D-003]".
- Keep headings unique. Tasks point at these headings, so renaming one means
  running `check.py impact` first.
- Sections that need no implementing task (e.g. background) can carry <!-- no-task -->
  on the line after the heading.
-->

## Overview
What this module is and why it exists, in a few sentences a new engineer can follow.

## Scope
**Owns:** the facts and behaviour this module is the single source of truth for.

**Does not own:** things that belong elsewhere, each with a link to the owner.

## <Behaviour section, e.g. Upload flow>
Plain description of the behaviour. Cite decisions inline [D-001].

## <Another behaviour section>
...

## Interfaces
Contracts this module provides to others (APIs, events, data shapes). Consumers link here.

## Glossary
- **Term**: definition. A term is defined in exactly one module.

## Open questions
- [ ] <Anything unresolved. Delete the line once answered and record it as a decision.>

## Related modules
- [other-module](other-module.md#section): why it relates
