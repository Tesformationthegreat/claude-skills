# VERDICT Prioritization Skill

A Claude skill for prioritizing features, tasks, or initiatives using the VERDICT framework. Works two ways: scored inline in chat for quick decisions, or as a populated Excel file for team sessions and long term backlogs.

## What VERDICT is

VERDICT scores items across 7 weighted dimensions and produces a clear tier: Ship It, Plan It, or Park It.

- **V** = Value
- **E** = Effort (inverted)
- **R** = Risk (inverted)
- **D** = Dependencies
- **I** = Impact Reach
- **C** = Confidence
- **T** = Time Criticality

It was designed to patch the blind spots in RICE (no risk, no dependencies, no time criticality) and WSJF (no dependency awareness, no confidence scoring).

Kill Criteria runs as a PASS/FAIL gate before scoring, so strategically or legally impossible items get rejected regardless of numerical score. A Red Flag check catches the compensatory model problem where a catastrophic score on one dimension gets mathematically averaged away by a strong score elsewhere.

---

## Two modes, one skill

### Mode 1: Inline in chat

Claude walks you through the Kill Criteria gate, prompts for scores on each dimension using the Scale Guide, and outputs a markdown table with tiers and Red Flags directly in the conversation. Nothing to download, nothing to open.

**Best for:**
- A founder sorting a backlog in 10 minutes
- Quick decisions during a working session
- Teaching VERDICT to someone new by running them through one live
- When you do not need the output to live anywhere after the conversation

### Mode 2: Populated Excel file

The skill generates a populated copy of the VERDICT template with your items, scores, weights, and Kill Criteria filled in. All original formulas, tier logic, and Red Flag checks remain intact, so the file stays dynamic. Change a score, weights re-apply, tiers update.

**Best for:**
- Team scoring sessions where multiple people score independently (Round 1 silent scoring)
- Quarterly backlog reviews that get rescored later
- Anything that needs to live in Drive, Notion, or a team workspace
- Handing off to a product owner or lead to review and finalize

At the end of an inline run, Claude asks: "Want this as the populated VERDICT spreadsheet?" If yes, you get a downloadable .xlsx. If no, the markdown table is all you need. You never pay the cost of file generation unless you actually want the file.

---

## How the flow runs

**1. Weight preset.** One question upfront: Startup, Growth, Enterprise, Survival, Innovation, or Maintenance. Default is Growth. Presets live on the Weights tab of the template.

**2. Kill Criteria per item.** PASS or FAIL. FAIL items are automatically rejected and skipped from scoring. Use for regulatory blockers, strategic misalignment, hard technical infeasibility, or contractual issues.

**3. Score 7 dimensions.** Score each passing item 1 to 5 across Value, Effort, Risk, Dependencies, Impact, Confidence, and Time Criticality. The Scale Guide is baked into the skill so Claude can help calibrate scores during the conversation.

**4. Weighted output.** The weights from your chosen preset multiply against your scores to produce a Weighted Score (0 to 5) and a Tier: Ship It (4.0 to 5.0), Plan It (2.5 to 3.9), Park It (1.0 to 2.4).

**5. Red Flag check.** Any item with a single dimension scoring 1 gets flagged for discussion. A high weighted score does not override a catastrophic dimension score.

**6. Optional Excel export.** Populated .xlsx generated on request.

---

## The 7 dimensions (scoring reference)

**Value** (1 to 5): Business and user value delivered. 1 is negligible. 5 is transformative.

**Effort** (1 to 5, INVERTED): Time, cost, resources. 1 is months of work with a large team. 5 is a few hours, quick win.

**Risk** (1 to 5, INVERTED): Technical, market, or security risk. 1 is extreme (unproven tech, regulatory exposure). 5 is minimal (proven pattern).

**Dependencies** (1 to 5): How blocked and how much it unblocks. 1 is fully blocked with external dependency. 5 is independent and unblocks multiple downstream items.

**Impact Reach** (1 to 5): How many users or customers affected. 1 is a handful. 5 is nearly all users or a new market.

**Confidence** (1 to 5): Certainty of estimates. 1 is speculation. 5 is validated data.

**Time Criticality** (1 to 5): Cost of delay. 1 is no urgency. 5 is critical deadline or imminent competitive threat.

---

## Weight presets

Stage presets:

| Dimension | Startup | Growth | Enterprise |
|-----------|---------|--------|-----------|
| Value | 30% | 25% | 20% |
| Effort | 20% | 15% | 10% |
| Risk | 5% | 10% | 15% |
| Dependencies | 5% | 10% | 15% |
| Impact Reach | 15% | 20% | 20% |
| Confidence | 5% | 5% | 5% |
| Time Criticality | 20% | 15% | 15% |

Operating mode presets (swap these based on current reality, not company size):

| Dimension | Growth | Survival | Innovation | Maintenance |
|-----------|--------|----------|------------|-------------|
| Value | 25% | 15% | 30% | 15% |
| Effort | 15% | 25% | 10% | 20% |
| Risk | 5% | 20% | 5% | 15% |
| Dependencies | 10% | 10% | 5% | 20% |
| Impact Reach | 20% | 10% | 20% | 10% |
| Confidence | 5% | 10% | 5% | 5% |
| Time Criticality | 20% | 10% | 25% | 15% |

---

## Files in this skill

```
verdict-prioritization/
├── SKILL.md         The workflow logic Claude follows
├── template.xlsx    The VERDICT scoring template (unmodified)
├── populate.py      Script that fills the template with scored items
└── README.md        This file
```

---

## When NOT to use VERDICT

Use this for backlog triage, feature prioritization, sprint planning, roadmap sequencing, and comparing initiatives within a known domain.

Do not use it for funding allocation (use financial models), regulatory compliance sequencing (use compliance frameworks), hiring plans, or high stakes bets with existential risk. Those need deeper analysis than a weighted score can provide. That is what Kill Criteria is for. When in doubt, kill first, score second.

---

## Known limitations

All scores are subjective. Use the Scale Guide and calibrate as a team to reduce bias.

Weighted sums assume linear tradeoffs. A catastrophic risk should be caught by Kill Criteria, not by scoring.

Changing weights shifts priorities. Rescore when switching operating modes.

VERDICT is a thinking tool, not a decision authority. It structures the conversation. Judgment still required.

---

## Credit

VERDICT framework created by Tes, founder of SO Email Security. Part of the Tesformation Claude Skills collection at github.com/Tesformationthegreat/claude-skills.
