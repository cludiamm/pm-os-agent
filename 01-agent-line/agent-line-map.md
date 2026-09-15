# Agent Line Map: Cortex PM Chief-of-Staff Agent

> Module 1 · The Agent Line
>
> ✅ **What this validates:** every risky action has a clear owner, by the end you'll have proven an above/below-the-line map with HITL checkpoints, scored on reversibility, blast radius, and measurability.

## The workflow, decision by decision

List every discrete decision or action in your agent's workflow, then score each one and place it **above** the line (a human owns it) or **below** (the agent owns it). Borderline calls get an HITL checkpoint.

| Decision / action | Reversibility (H/M/L) | Blast radius (H/M/L) | Measurability (H/M/L) | Above / Below | HITL? |
|---|---|---|---|---|---|
| Pull project state + activity | H | L | H | Below | · |
| Decide relevant context | H | H | L | Above | · |
| Draft the update | H | L | H | Below | · |
| Decide tone/commitment level | L | H | H | Above | · |
| Flag at-risk/escalation | H | M | H | HITL | Cortex flags, human confirms |
| Choose what to escalate | H | H | H | HITL | Cortex proposes, human approves |
| Propose a story batch (capped) | H | L | H | Below | · |
| Post an update / approve a company-wide one | L | H | H | Above | required |

## Agent anatomy (sketch)

- **Model:** Cheap/fast tier by default for every action. Escalates to a frontier model specifically for the HITL actions (flag at-risk/escalation, choose what to escalate) when the project is flagged as higher-risk/higher-visibility, since those calls need more judgment before they even reach a human.
- **Tools:** project + activity lookup (read) · past-update search · roadmap · team norms · story proposal (capped) …
- **Memory:** Roadmap, past updates/decisions, and team norms all persist across runs, none are purged, so Cortex has continuity on tone/precedent and doesn't rediscover context from scratch every time.
- **Loop:** _placeholder, defined in M2 loop-spec.md_
- **Bounds:** _placeholder, defined in M5 bounds-and-evals.md_
- **Evals:** _placeholder, defined in M5 bounds-and-evals.md_

## The golden rule, applied

- **Pull project state + activity** (Below): Sits below the line because it's easy to reverse (just re-run it), has a low blast radius (read-only, nothing shared or committed), and is highly measurable — deciding factor: blast radius.
- **Decide relevant context** (Above): Sits above the line because a wrong choice can quietly steer the whole update (high blast radius) and the failure is hard to catch after the fact since you'd need to already know the right context to notice an omission — deciding factor: measurability.
- **Draft the update** (Below): Sits below the line because it's easy to reverse (just redraft), has a low blast radius (a draft alone commits nobody), and is highly measurable by a reviewer — deciding factor: blast radius.
- **Decide tone/commitment level** (Above): Sits above the line because a stated commitment is hard to reverse once stakeholders act on it, and the blast radius is high enough to put the whole team at risk — deciding factor: reversibility.
- **Flag at-risk/escalation** (HITL): Sits at a HITL checkpoint because reversibility and measurability are both high, but blast radius is medium — false positives cause alert fatigue and erode trust — deciding factor: blast radius, resolved by Cortex flagging and a human confirming.
- **Choose what to escalate** (HITL): Sits at a HITL checkpoint because although easy to reverse and measure, escalating to the wrong people wastes higher-stakes attention (high blast radius) — deciding factor: blast radius, resolved by Cortex proposing and a human approving.
- **Propose a story batch (capped)** (Below): Sits below the line because it's easy to reverse (rerun prioritization), has a low blast radius (nothing committed until approved), and is highly measurable — deciding factor: reversibility.
- **Post update / approve company-wide** (Above): Sits above the line because it's irreversible once sent and has the widest possible blast radius, potentially including sensitive information — deciding factor: reversibility.

## Hardest call

The hardest calls were **flagging at-risk items** and **choosing what to escalate** (actions 5 and 6). For both, **blast radius** was the axis that ultimately settled it — even with high reversibility and measurability, the risk of false positives (alert fatigue) or escalating to the wrong people was enough to keep a human in the loop, landing both at HITL rather than a clean below/above split. Working through that also raised a further question worth flagging: could **"decide relevant context"** and **"decide tone/commitment level"** also be HITL — Cortex drafts a proposal, a human confirms — rather than fully above the line where a human does them from scratch? (Share this in `#cohort-channel`.)
