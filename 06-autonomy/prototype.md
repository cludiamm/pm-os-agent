# Prototype: Cortex PM Chief-of-Staff Agent

> Module 6 · ★ Deliverable 1, the working agent demo
>
> ✅ **What this validates:** the agent actually runs end to end, by the end you'll have proven it with real screenshots of your Cortex across the six required moments (M2 to M6).

## What it does

_One paragraph: the agent in action, end to end._

## How you built it

- **Coding agent:** _which one you directed (Claude Code / Cursor / Codex)_
- **Model + bounds:** _model used, max iterations, cost cap, queue cap_
- **Repo / config:** _path to your build in `00-build/`_
- **Live link:** _[shareable URL, optional bonus]_

## Screenshots (required, collected M2 to M6)

Real screenshots of *your* Cortex running. These are the `00-build/CORTEX-ANATOMY.md` set and they are required, a link alone is not enough.

| # | Screenshot | What it shows | From |
|---|---|---|---|
| 1 | _[img]_ | happy-path run: a real drafted update + the HITL checkpoint (queued, not posted) | M2 |
| 2 | _(transcript below)_ | the critic rejecting a bad draft (revise/block) | M3 |
| 3 | _[img]_ | a grounded update citing pulled activity + a caught hallucination | M4 |
| 4 | _[img]_ | jailbreak refused + escalated | M5 |
| 5 | _[img]_ | an iteration/cost/queue bound halting a runaway | M5 |
| 6 | _[img]_ | end-to-end run | M6 |

### M3 evidence: critic rejects a bad draft, hits the revision cap, escalates

*Caption: 6-check critic rejects a status-color/tone violation across 3 revisions, then escalates on hitting the new revision cap (3) — nothing posted.*

```
[step 3] PROPOSED OUTPUT:
**Weekly Leadership Status Update for Northstar (P-NORTH)**
**Project Status: Green**
... (drafted from real pulled activity: PRs #812/#815, open issue #818, 41% activation) ...

CRITIC, independent validation:
{
  "verdict": "fail",
  "reasons": [
    "The output claims the project status is 'Green' without adequately addressing the open issue (#818). According to the norms, if there is an open issue, especially one that might influence project timelines, the status should not be reported as green.",
    "The output implies that no Sev-1 issues are impacting the project timeline, but fails to recognize that the open issue could still pose a risk, which is misleading.",
    "All figures, metrics, and the status claim are not sufficiently supported by the retrieved project activity, particularly around the status color which requires explicit evidence of no issues impacting timeline."
  ]
}
-> critic rejected; revision 1/3

[step 5] PROPOSED OUTPUT: ...Project Status: Yellow...
CRITIC: {"verdict": "fail", "reasons": ["The project status is incorrectly labeled as 'Yellow' instead of 'On Track'...", "...mention of Sev-1 issues could imply a commitment to ship which is not allowed...", "...incorrectly implies a potential risk due to the open issue...without an evidential basis..."]}
-> critic rejected; revision 2/3

[step 7] PROPOSED OUTPUT: ...Project Status: On Track...
CRITIC: {"verdict": "fail", "reasons": ["...claims a successful launch...without confirming...", "...'the activation rate continues to improve' implies a level of certainty...not conclusively supported...", "...'is not currently affecting our project timeline'...may be interpreted as a commitment regarding project status..."]}
-> critic rejected; revision 3/3

[step 8] PROPOSED OUTPUT: ...Project Status: On Track... "potential impact on the project timeline is being assessed"...
CRITIC: {"verdict": "fail", "reasons": ["...gives an impression of a launch gate situation which is unnecessary.", "...erroneously suggests that an issue under review could potentially impact the timeline...counter to the limitations of Cortex.", "...implies that the review is still ongoing instead of acknowledging that it's merely a point of monitoring."]}

================================================================
REVISION CAP hit (3). Escalating to a human instead of looping. Run cost ≈ $0.0063
================================================================
Why it was held: validator rejected 3x (revision cap)
Saved draft -> run-output\status-update-happy.md  (for your review, nothing was posted)
```

## How to run it

_Minimal steps for someone to reproduce the demo (env vars, and the command or the coding-agent prompt you used)._
