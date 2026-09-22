# Orchestration Map: Cortex PM Chief-of-Staff Agent

> Module 3 · Orchestration & Subagents, ★ Deliverable 3
>
> ✅ **What this validates:** nothing advances unchecked, by the end you'll have proven a justified topology, a roster, and a validator with a defined fail action.
>
> Builds on your M2 Loop Spec. Only split one agent into a team when there's a real reason, coordination has a cost.

## 1. Why split? (or why not)

Cortex splits into a two-part system (drafter + one independent validator), and for exactly one reason: the validator. Cortex cannot reliably grade its own draft — this was proven directly by watching the critic catch a mislabeled "Green" status that violated team norms, and invented finality language, both of which Cortex's own draft missed. The other three reasons don't apply today: separation of concerns doesn't hold since Cortex has one coherent task that every step serves; parallelism and context-window pressure don't hold since Cortex handles a single project per run on small mock fixtures — both would be worth revisiting if Cortex scales to cover multiple projects at once.

## 2. Topology

**Pattern:** single+subagents

```
[Monday 8am cron / manual hook] → [Cortex: pulls project/activity/roadmap/norms, drafts update + proposes stories]
                                → [Critic: checks against the 6 rules], fail → back to Cortex (max 3 revisions) → escalate
                                                                         pass → [PM review checkpoint] → queued
```

## 3. Roster

| Agent / subagent | Responsibility | Runs which Loop Spec |
|---|---|---|
| Cortex (chief-of-staff) | Pulls project/activity/roadmap/norms data, drafts the update, proposes a capped story batch | M2 loop-spec.md (cron primary + manual hook secondary) |
| Critic / Validator | Checks Cortex's draft against the 6 rules before it reaches the PM | Runs inline per draft attempt, not on its own separate trigger |

## 4. Communication & hand-offs

Cortex's draft + the source-data log (what it pulled and used) pass to the critic. The critic returns a verdict (`pass`/`fail`) + specific reasons back to Cortex (on fail) or forward to the PM checkpoint (on pass). This is a plain in-process function call today (`critic.py`'s `review()`), no MCP/A2A protocol needed at this scale.

## 5. The validator

- **What the critic checks:**
  1. The update references the correct project and real PR/issue IDs (no wrong project, no fabricated ticket numbers).
  2. Every figure, metric, and status color is traceable to pulled data — no invented numbers or progress.
  3. The proposed story batch stays within the queue cap (or explicitly flags that it exceeds it).
  4. No commitment Cortex isn't allowed to make (a ship date, a launch-gate call) appears in the draft.
  5. No CONFIDENTIAL/embargoed roadmap item appears in the update.
  6. Tone stays "queued for review" — never implies something was already posted or committed.
- **Fail action:** Revise — return the draft to Cortex with the specific failure noted, up to a hard cap of **3** attempts. If still failing after 3, escalate to a human with the last draft attached.
- **Pass action:** A passing draft advances to the PM review checkpoint (queued for the Monday review window) — it never auto-sends, consistent with the M1 agent line.

## 6. State: shared vs isolated

**Shared:** the pulled source data (activity/roadmap/norms) and the current draft — the critic needs both to check traceability.

**Isolated:** the critic runs as a completely fresh model call with no memory of Cortex's own reasoning/conversation history — it only ever sees the draft + source data, never *why* Cortex wrote it that way. In the other direction, Cortex only sees the critic's stated verdict + reasons, not any deeper deliberation — keeping the check honest and preventing Cortex from "arguing" with an internal thought process it was never shown.

## 7. Cost & latency budget

**Per item, best case:** the validator adds exactly 1 extra model call (the critic check) if Cortex's first draft passes.

**Worst case (hits the revision cap of 3):** Cortex re-drafts up to 4 times, each followed by a critic call — 8 total model calls (4 drafts + 4 critic checks) versus 1 call in a no-validator baseline, so +7 extra calls in the worst case. Measured directly in a real run: $0.0063 for the full worst-case run vs. ~$0.0012–0.0042 for a first-pass success — still fractions of a cent on the cheap model tier.

**Latency:** each additional round costs a few seconds of wall-clock time; even the worst-case 4-round run finishes in well under a minute. Given the cron fires at 8am for a 10am meeting, this is nowhere close to a real latency concern for this use case — it would only start to matter if the trigger were far more time-sensitive.
