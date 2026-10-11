# How to ask questions

The docs are only as good as the answers behind them, and people stop answering when questioning feels like an interrogation. These rules keep it thorough without exhausting the user.

## Before asking anything

- **Look first.** If the answer is in the repo, the existing docs, or something the user already said, use it. Asking what you could have found out wastes trust. When you take an answer from the code, say so and record it with `basis: code` so the user can confirm it.
- **Ask only what changes the implementation.** If every answer leads to the same build, do not ask.

## Batches

- Group questions by module or topic, **3 to 5 per batch**, most blocking first. Finish a topic before opening the next so the user keeps one thing in their head.
- Each question has a **recommended answer** that you mark clearly, so the user can accept it in one word ("ok", "yes", "1"). A recommendation is a real position with a reason, not a coin flip. If you have no basis for a recommendation, say so and ask openly.
- Where the choices are discrete, list 2 to 4 options. Use the environment's structured question tool if it has one (tappable options work well on mobile); otherwise use a numbered list.
- One sentence of why when it is not obvious ("this decides whether we need a background job").

Example batch:

> Uploads, part 1 of 2. Say "ok" to accept all my suggestions, or answer by number.
> 1. Biggest file allowed? **Suggest 25 MB** (covers images and PDFs; larger files need a different upload method).
> 2. Who can upload? **Suggest any logged-in workspace member.** Or: only admins.
> 3. When someone deletes a file, is it gone at once or recoverable for a while? **Suggest recoverable for 30 days.**

## Answers become decisions

Every answer ends up as an entry in `decisions.md` with a `basis`:

| Situation | `basis` | What to log |
| --- | --- | --- |
| The user chose or accepted a suggestion | `user` | The decision and their reason if they gave one |
| The user skipped the question or said nothing | `default` | The recommended default, the fact it was not chosen by the user, and that it is safe to revisit |
| The user said "you decide" | `delegated` | Your choice and the reasoning, so nobody has to guess later |
| You read it from code and the user confirmed | `code` | What the code does and where |

Never let a skipped question vanish. A silent assumption is the origin of later contradictions, and a logged default can be found and challenged.

## Fatigue

- If the user accepts everything for three batches in a row, offer to apply defaults to the remaining low-impact questions in one go. List them in a compact form so they can veto any. They are still logged as decisions.
- Never batch away a question that has real consequences (data loss, money, security, public API shape). Those are always asked on their own.
- If the user gives a long free-form answer that settles several questions, say which ones you consider settled and carry on; do not re-ask.

## Playback before questions

When you have understood a request, say it back in a few plain sentences and ask whether it is right and what you missed. Do this before the clarifying questions, because every later question builds on that understanding. Details are in `references/planning.md`.

## Open questions in the docs

If a question cannot be answered yet (waiting on a third party, a spike, a stakeholder), record it under `## Open questions` in the owning module as `- [ ] ...`. The checker warns about these, which keeps them visible. Delete the line when answered and record the answer as a decision. A feature with open questions that block implementation is not ready for task generation; tell the user which ones block.
