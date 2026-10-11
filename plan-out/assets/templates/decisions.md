# Decision log

Every decision lives here once. Modules state the decision in their own words and
cite it by id (`[D-001]`); this file records who owns it and whether it still holds.
Never edit a decision to change its meaning. Add a new one that supersedes it.

<!--
Entry format (fenced so tools ignore it):

```
## D-001: Short title
- status: active            (active | superseded)
- owner: module#section     (the one place that states this decision)
- date: YYYY-MM-DD
- basis: user               (user | default | delegated | code)
- decision: One or two sentences, unambiguous.
- why: The reason, so nobody re-litigates it blindly.
- supersedes: D-000         (only on the replacing decision)
- superseded_by: D-002      (only on the old decision, together with status: superseded)
```

basis meanings:
  user       the user chose it
  default    the user skipped the question; the recommended default was applied
  delegated  the user said "you decide"; the reasoning is in `why`
  code       taken from the existing implementation, confirmed by the user
-->
