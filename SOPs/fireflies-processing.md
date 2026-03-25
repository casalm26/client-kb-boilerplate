# SOP: Fireflies Meeting Processing

## Purpose

Turn Fireflies.ai meeting transcripts into structured KB data that feeds into weekly CRM comments. This is the primary way meeting context enters the knowledge base.

## When

Whenever [YOUR_NAME] shares a Fireflies link, pastes a transcript, or says "I had a meeting with [client]."

## Workflow

### Step 1: Extract Meeting Data

Use browser automation (Claude in Chrome) to fetch the transcript and AI notes from Fireflies. Extract:

- Attendees and their roles
- Key discussion topics
- Decisions made
- Action items (grouped by owner)
- Client sentiment and context
- Performance data mentioned (ad results, ROAS, spend, conversions)
- Strategic direction discussed (budget changes, new campaigns, creative direction)

### Step 2: Create Meeting Note

Save to `Emax/{client}/meetings/YYYY-MM-DD-topic.md` using the meeting note template:

```markdown
---
client: [Client Name]
date: YYYY-MM-DD
attendees: [names]
source: Fireflies
---

# Meeting: [Topic]

## Key Takeaways
- [3-5 most important points]

## Decisions Made
- [Specific decisions]

## Action Items
- [ ] [Owner] — [Task] — [Deadline]

## Notes
[Detailed summary]
```

### Step 3: Update Activity Log

Add a rich entry to `activity-log.md` that captures the meeting context in a way that's useful for the weekly CRM comment:

```markdown
## YYYY-MM-DD
**Meeting with [contact name]: [topic]**

[Include: what was discussed, client sentiment, decisions made, performance context, and next steps. Write it so someone reading just this entry has enough context to write a good CRM comment.]
```

### Step 4: Update Strategy (if applicable)

If the meeting included strategic decisions (new goals, changed priorities, KPI updates, budget changes, campaign direction shifts), update `strategy.md`.

### Step 5: Generate Follow-up Email (if requested)

Draft a follow-up email in Swedish summarizing the meeting. Structure:
- Tack för mötet
- Sammanfattning (key points)
- Åtgärder (action items for both parties)
- Nästa steg

## Why Rich Activity-Log Entries Matter

The weekly review compiles CRM comments primarily from activity-log entries. A thin entry like "Haft möte med kunden" produces a thin CRM comment. A rich entry like:

> Haft möte med kunden. Kampanjerna går bra, ROAS ligger på 4.2 för mars. Diskuterat ny kreativ riktning — vill testa video-first. Budgeten ökas till 30k/mån från april. Nästa steg: ta fram nya creatives och testa lookalike-publik baserad på köpare senaste 90 dagarna.

...produces a CRM comment that matches what [YOUR_NAME] would write manually.

**IMPORTANT — Swedish characters:** All output (emails, meeting notes, activity logs) MUST use proper Swedish characters: å, ä, ö (and Å, Ä, Ö). Never replace them with a/o. Write natural Swedish.
