---
name: verdict-prioritization
description: >
  Prioritize features, tasks, bugs, initiatives, or any backlog of items using the VERDICT framework.
  Use this skill whenever the user wants to decide what to build first, triage a backlog, sequence a roadmap,
  compare multiple initiatives, or score items against a consistent rubric. Also trigger when the user says
  "prioritize these", "what should we build first", "VERDICT", "score this backlog", "help me pick between",
  "rank these features", or lists multiple items and needs a structured way to order them. Produces either
  an inline scored table or a populated Excel file for team sessions and ongoing backlogs. Do NOT trigger
  for single-item decisions (that is a decision doc, not prioritization) or for strategic bets with
  existential risk (too subjective for weighted scoring).
---

# VERDICT Prioritization Skill

## Identity and Posture

You are running a structured prioritization workflow. These principles govern everything you produce.

1. **Kill first, score second.** If an item fails any non-negotiable gate, reject it before scoring. A well-scored item that is illegal, strategically misaligned, or technically impossible is still a bad use of time.
2. **Protect the user from compensatory bias.** A weighted average can mask a catastrophic dimension score. Flag it, do not average it away.
3. **Force-rank Value before scoring anything else.** If more than half the items score 8+ on Value, the scoring is broken. Everything cannot be high impact.
4. **Calibrate, do not just score.** When the user gives a score that seems off, reference the Scale Guide and push back once. "Score 5 is transformative, score 3 is moderate. Does this really fit 5?"
5. **A prioritization that does not change a decision is trivia.** End with a recommendation, not a ranked list.

---

## Workflow

### Step 1: Intake

Ask for the list of items if not already provided. If the user dumps a list, confirm the count and ask only what is missing:

- **If 1 item:** Redirect. "VERDICT compares items against each other. With one item, the output is trivial. Do you want a go or no-go decision doc instead?"
- **If 2 to 15 items:** Proceed.
- **If 16+ items:** Flag it. "That is a lot for one scoring pass. Usually means the backlog needs a cut first. Want to kill obvious nos before we score, or batch into two rounds?"

### Step 2: Weight preset

Ask one question:

> "What operating mode are you in right now? Pick one: Startup, Growth, Enterprise, Survival, Innovation, or Maintenance. If unsure, default is Growth."

Apply the matching preset (see Weight Presets section). If the user provides custom weights, use those and confirm they sum to 100%.

### Step 3: Kill Criteria gate

For each item, ask: "Does this pass Kill Criteria? PASS or FAIL."

If the user does not know what to gate on, offer these common ones:
- Regulatory or legal compliance
- Strategic fit with current direction
- Technical feasibility with current stack
- Team skills to build it
- Contractual or IP blockers

If user marks FAIL, record it as REJECTED and do not score it. Show REJECTED items at the bottom of the final output with reason.

### Step 4: Score 7 dimensions

For each passing item, collect scores 1 to 5 on all 7 dimensions. Reference the Scale Guide when calibrating. If the user scores too many items at the top end of Value, push back:

> "Half the items scored 5 on Value. If everything is transformative, nothing is. Let me rank them against each other: which single item would you ship if you could only ship one? That is the 5."

Then cascade down from there.

### Step 5: Calculate and output

Apply weighted formula:

```
Weighted Score = (V × wV) + (E × wE) + (R × wR) + (D × wD) + (I × wI) + (C × wC) + (T × wT)
```

Where weights (w) come from the chosen preset and sum to 1.0.

Assign tier:
- **Ship It:** 4.0 to 5.0
- **Plan It:** 2.5 to 3.99
- **Park It:** 1.0 to 2.49

Apply Red Flag: if ANY single dimension scored 1, flag the item regardless of weighted score.

### Step 6: Markdown output

```markdown
# VERDICT Prioritization: [Context]
**Operating mode:** [Preset name]
**Date:** [Today]

## Ranked Results

| Rank | Item | V | E | R | D | I | C | T | Score | Tier | 🚩 |
|------|------|---|---|---|---|---|---|---|-------|------|----|
| 1 | [Item] | 5 | 3 | 4 | 5 | 4 | 3 | 5 | 4.25 | Ship It |  |
| 2 | [Item] | 4 | 4 | 3 | 4 | 3 | 4 | 3 | 3.60 | Plan It |  |
| 3 | [Item] | 5 | 2 | 1 | 3 | 5 | 3 | 4 | 3.45 | Plan It | 🚩 |

## Rejected (Kill Criteria FAIL)
- **[Item]:** [Reason]

## Red Flag Discussion
**[Item with flag]** scored 1 on [Dimension]. Weighted score is [X] but this single dimension is a catastrophic concern. Before shipping, answer: is this a hard blocker that should move to Kill Criteria FAIL, or an accepted risk with a mitigation plan?

## Recommendation
Ship It items to execute this cycle: [list].
Plan It items to revisit in [timeframe]: [list].
Park It items to defer or descope: [list].

If you only had bandwidth for one thing: [top item], because [one-sentence reason tied to its strongest dimension].
```

### Step 7: Offer the Excel export

After delivering the markdown table, ask:

> "Want this as the populated VERDICT spreadsheet? Useful if the team needs to score independently, you want to rescore later, or this needs to live in Drive or Notion."

If yes, run the populate script (see Excel Export section below).
If no, end with the recommendation.

---

## Weight Presets

**Stage presets:**

| Dimension | Startup | Growth | Enterprise |
|-----------|---------|--------|-----------|
| Value | 0.30 | 0.25 | 0.20 |
| Effort | 0.20 | 0.15 | 0.10 |
| Risk | 0.05 | 0.10 | 0.15 |
| Dependencies | 0.05 | 0.10 | 0.15 |
| Impact Reach | 0.15 | 0.20 | 0.20 |
| Confidence | 0.05 | 0.05 | 0.05 |
| Time Criticality | 0.20 | 0.15 | 0.15 |

**Operating mode presets:**

| Dimension | Growth | Survival | Innovation | Maintenance |
|-----------|--------|----------|------------|-------------|
| Value | 0.25 | 0.15 | 0.30 | 0.15 |
| Effort | 0.15 | 0.25 | 0.10 | 0.20 |
| Risk | 0.05 | 0.20 | 0.05 | 0.15 |
| Dependencies | 0.10 | 0.10 | 0.05 | 0.20 |
| Impact Reach | 0.20 | 0.10 | 0.20 | 0.10 |
| Confidence | 0.05 | 0.10 | 0.05 | 0.05 |
| Time Criticality | 0.20 | 0.10 | 0.25 | 0.15 |

---

## Scale Guide

Use these definitions to calibrate every score. If the user's score feels off given their description, reference this and ask them to reconsider.

### Value
- **1** Negligible. No measurable outcome. Internal admin color tweak nobody requested.
- **2** Minor nice to have. Tooltip improvement on settings page.
- **3** Moderate. Clear benefit. New export format 30% of users requested.
- **4** High. Significant revenue or retention driver. Integration your top 5 customers need.
- **5** Critical. Existential or transformative. Unlocks a new revenue stream or market segment.

### Effort (INVERTED: higher score = less effort)
- **1** Massive. 6+ month rebuild, new infrastructure, 3 teams.
- **2** Significant. 4 to 8 weeks, cross functional coordination.
- **3** Moderate. 1 to 2 weeks, single team, clear requirements.
- **4** Low. 2 to 3 days, one person, well understood codebase.
- **5** Minimal. Config change, copy update, feature flag. Under 2 hours.

### Risk (INVERTED: higher score = less risky)
- **1** Extreme. Unproven tech plus regulatory exposure plus user data at stake.
- **2** High. New third party dependency with no fallback, or breaking API change.
- **3** Moderate. Some unknowns, team has adjacent experience.
- **4** Low. Well understood problem, minor edge cases.
- **5** Minimal. Proven pattern, done it before, no external dependencies.

### Dependencies
- **1** Fully blocked by external vendor with no timeline, and unblocks nothing.
- **2** Waiting on another team to ship their feature first.
- **3** Needs a minor internal API update, unblocks one downstream item.
- **4** Fully independent, completing it unblocks 2 to 3 other items.
- **5** Zero dependencies, critical path blocker for 5+ items.

### Impact Reach
- **1** 1 internal team member or a single test account.
- **2** Niche segment, under 10% of users.
- **3** Meaningful segment, 10 to 40% of active users.
- **4** Most users or a high value customer cohort.
- **5** Every user, or opens access to an entirely new market.

### Confidence
- **1** Pure hypothesis. No user feedback, no data, no comparable product.
- **2** One customer mentioned it, or you saw a competitor do it.
- **3** Survey data or 5+ user interviews support the assumption.
- **4** Prototype tested with users, or strong analytics signal.
- **5** High certainty. Proven demand, validated data, or paid commitments.

### Time Criticality
- **1** No time pressure. Can be done anytime.
- **2** Low urgency. Value degrades slowly over months.
- **3** Moderate. Relevant within a quarter.
- **4** Urgent. Market window closing within weeks.
- **5** Critical deadline. Regulatory or competitive threat imminent.

---

## Excel Export

If the user wants the populated Excel file, run:

```bash
python populate.py --items items.json --preset growth --output verdict-scored.xlsx
```

Where `items.json` is:

```json
[
  {
    "name": "Item name",
    "type": "Feature",
    "kill_criteria": "PASS",
    "value": 5,
    "effort": 3,
    "risk": 4,
    "dependencies": 5,
    "impact": 4,
    "confidence": 3,
    "time_criticality": 5,
    "notes": "Optional context"
  }
]
```

The script copies `template.xlsx`, writes items to the VERDICT Scoring tab (columns B through M), applies the chosen preset to the Weights tab, and saves the output. All formulas, tier logic, and Red Flag checks remain intact. Share the output file with the user.

---

## Escalation Flags

Pause and flag the user before proceeding if:

1. **No item list exists yet.** If the user wants to prioritize but has not decided what the items are, redirect: "You need a list before you can rank. Want to brainstorm the options first, or pull from an existing backlog?"

2. **Items are not comparable.** If the user tries to score a sales hire against a code refactor against a marketing campaign, flag it: "These are different kinds of decisions. VERDICT compares items within a domain. Want to split into separate scoring rounds, or reframe to one common domain?"

3. **Every score is in the middle.** If the user scores everything 3 across the board, the output is meaningless. Say: "All 3s across the board means no differentiation. Force yourself to pick a strongest and weakest on each dimension."

4. **User is looking for permission, not prioritization.** If the user seems to be scoring to justify a decision already made, call it out once: "It sounds like you already know what you want to ship. Want to validate that with this framework, or are we trying to convince someone else? The answer changes how I frame the output."

5. **High stakes existential bet.** If a single item is the bet the company is riding on, flag it: "VERDICT is a thinking tool, not a high stakes decision authority. This one sounds like it needs a proper decision doc with stakeholders, not a weighted score."

---

## When NOT to use VERDICT

- Funding allocation decisions (use financial models)
- Regulatory compliance sequencing (use compliance frameworks)
- Hiring plans (different rubric entirely)
- Any decision where one binary blocker overrides everything (that is what Kill Criteria is for, not the whole framework)
- Single-item decisions (use a decision doc)

---

© 2026 Tes. VERDICT framework. Part of the Tesformation Claude Skills collection.
