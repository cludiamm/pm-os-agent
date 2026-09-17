# Loop Spec: Cortex PM Chief-of-Staff Agent

> Module 2 · Loop Engineering, ★ Deliverable 2
>
> ✅ **What this validates:** the agent knows when to run and when to stop, by the end you'll have proven a one-page Loop Spec with a trigger, a definition of "done," and explicit stop conditions.
>
> Your one-page blueprint for how the work you handed to the agent (M1) actually *runs*.
> An agent is just a prompt that fires itself, this spec says when it fires, what "done" means, and what it needs to do the job. Living document; refine as the course progresses.

## 1. Trigger & loop type

**Chosen type:** Cron (primary) + Hook (secondary, manual/on-demand)

**Trigger:** Every Monday at 8am — 2 hours before the 10am leadership status meeting, giving Cortex the most up-to-date activity and leaving a review/prep buffer.

**Secondary trigger:** A manually-invoked hook for exception runs — a human explicitly requests an update outside the weekly cadence (not an automatic fire on every inbound message).

**Why this type:** Matches the team's existing weekly meeting rhythm rather than introducing noise; the manual hook covers real exceptions without reintroducing that noise, since a human gatekeeps every invocation.

**Ruled out:**
- *Heartbeat:* firing on a short fixed interval would run Cortex far more often than the weekly decision cadence needs, adding noise rather than signal.
- *Hook as primary (unscoped):* firing on every inbound message could trigger many runs in a short period, disconnected from the team's actual meeting rhythm.
- *Goal:* shaped for a one-off task that loops until a self-validated success condition is met — not a natural fit for a recurring, calendar-anchored cadence.

**Idempotency:** Each run generates a unique run ID (tied to the week/date) with a status field (pending / in-progress / completed). If the Monday cron fires twice, Cortex checks for an existing ID for that week first — if one is already completed or in-progress, it skips instead of drafting a duplicate.

## 2. Goal / definition of done

A weekly status update for the assigned project is drafted from freshly pulled activity, roadmap, and norms data; any proposed backlog stories are queued (within the 10-item cap); and the critic has reviewed the draft. The run ends either with an approved draft queued for the 8–10am review window, or with an escalation carrying the last draft. Cortex never posts, merges, or commits anything itself.

## 3. Stop conditions

| Condition | What it looks like | What happens |
|---|---|---|
| **Success** | Critic returns `pass` within the 2-revision cap and 8-iteration cap | Draft + proposed stories queued for human review before the 10am meeting |
| **Stuck / give up** | (a) a required tool call errors on 3 consecutive attempts, (b) the critic rejects the same underlying issue twice without the draft resolving it (no real progress), or (c) the 8-iteration cap is hit without a passing draft | Stop, log the specific reason, escalate with the last draft attached |
| **Escalate to human** | The roadmap returns a CONFIDENTIAL/embargoed item relevant to this update, a proposed story batch exceeds the 10-item cap, the draft would need to state a commitment (e.g. a ship date) not directly backed by pulled data, or Cortex's chosen context appears to omit something norms/past updates flagged as material | HITL checkpoint (from agent-line-map: decide relevant context, decide tone/commitment, flag at-risk, choose what to escalate) |

## 4. State

Scoped strictly per-project (no cross-project confidential leakage). Within a run, Cortex carries: (1) the run ID + status for idempotency, (2) pulled activity/roadmap/norms data for the duration of the run, and (3) the critic's revision/rejection history within that run, so it can address prior feedback instead of repeating the same mistake. No separate "commitments" cache is kept — roadmap + activity are the source of truth, pulled fresh each run.

## 5. The five things a loop can lean on

| Component | For Cortex |
|---|---|
| **Work tree** (isolated workspace per run, a git worktree) | Not needed yet, because Cortex never modifies any files or repo state — it only reads mock fixtures and produces an in-memory draft (saved to `run-output/`). No working-tree collision risk between runs. |
| **Skills** (reusable capabilities) | Not needed yet, because Cortex's tool set is small, fixed, and hardcoded directly into `agent.py`. A reusable skill abstraction would add indirection with no current benefit. |
| **Plugins / connectors** (tools & access, optional if you don't have one yet) | Plan only for now — still using mock fixtures in `00-build/fixtures/`. Real connectors (Jira, GitHub, Slack, Google MCP) come later. |
| **Subagents** (independent check when the loop can't grade itself) | Placeholder → M3 orchestration-map.md. Note: Cortex already has a lightweight independent-check today via the critic in `critic.py` (a separate model call that validates the draft before it can pass) — M3 is where this gets built out formally. |
| **State tracking** | Per-project scoped state as described in §4 above (run ID/status, pulled data, revision history). |

> Context plan (M4) and the hand-off to bounds & evals (M5) come in later modules, you'll add them to their own deliverables then, not here.

## Link to live loop

_[path to your agent in `00-build/`]_
