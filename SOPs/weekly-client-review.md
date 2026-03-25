# SOP: Weekly Client Review

## Purpose

Generate rich, CRM-ready comments for each Emax client every week. The output is posted as comments in Data Pulse CRM and logged in the KB activity logs.

## When

Every Friday (or when [YOUR_NAME] asks for a weekly review).

## The Two-Layer System

### Layer 1 -- Continuous Accumulation (during the week)

The KB `activity-log.md` for each client is the **running buffer** where context accumulates throughout the week from multiple sources:

| Trigger | What happens | Where it lands |
|---------|-------------|----------------|
| **Fireflies link shared** | Process transcript -> meeting note + activity-log entry | `meetings/YYYY-MM-DD-topic.md` + `activity-log.md` |
| **Client work in Claude session** | Auto-log per CLAUDE.md rules | `activity-log.md` |
| **Report or performance data shared** | Extract key data points | `activity-log.md` |
| **Strategy decision made** | Update strategy + log | `strategy.md` + `activity-log.md` |

The key is writing **rich** activity-log entries -- not just "did X" but "did X because Y, result was Z, next step is W."

### Layer 2 -- Friday Compilation

When the weekly review runs:

1. **Read activity-logs** -- rich context from meetings, work sessions, campaign changes
2. **Check Google Calendar** -- meetings that happened this week
3. **Check ad performance** (if available) -- ROAS, spend, conversions, campaign results
4. **Identify gaps** -- clients with activity but no KB context
5. **Nudge [YOUR_NAME]** -- "These clients need context: share Fireflies links or quick notes"
6. **Compile CRM comments** -- match Data Pulse writing style, in Swedish
7. **Present for approval** -- [YOUR_NAME] reviews before posting
8. **Update KB** -- append weekly review entries to activity logs

<!-- OPTIONAL: If using Todoist, add this step before step 1:
0. **Pull Todoist completions** -- task-level detail for the period (Monday-Friday)
-->

## CRM Comment Quality Checklist

A good CRM comment includes (where applicable):

- [ ] What was done (campaign changes, creative updates, audience tests, optimizations)
- [ ] Meeting outcomes and client sentiment
- [ ] Ad performance context (ROAS, spend, conversions, CTR, CPA trends)
- [ ] Strategic decisions or direction changes (budget, targeting, creative)
- [ ] Forward-looking next steps

## Gap Identification

Clients that have activity but thin KB context typically need one of:

1. **Fireflies transcript** -- share the link, Claude processes it
2. **Performance data** -- export from Meta Ads Manager, share with Claude
3. **Quick verbal summary** -- [YOUR_NAME] dictates context, Claude logs it
