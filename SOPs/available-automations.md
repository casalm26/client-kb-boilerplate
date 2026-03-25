# Available Automations

All automation workflows that can be installed as scheduled tasks. These run automatically on a schedule via Claude Code's scheduled tasks feature.

**How to install:** Use the `mcp__scheduled-tasks__create_scheduled_task` tool (or ask Claude to "schedule [task name]") with the taskId, cronExpression, description, and prompt from each section below.

**Important:** Scheduled tasks are local to your machine. If you switch computers, re-install them from this file.

---

## Recommended (Universal)

These work regardless of whether you use Todoist.

### 1. daily-meeting-followup

- **Schedule:** `30 8 * * 1-5` -- Weekdays 08:30
- **Description:** Draft follow-up emails for yesterday's client meetings via Fireflies
- **Requires:** Claude in Chrome, Google Calendar MCP, Fireflies access

**What it does:**
1. Checks yesterday's calendar for external client meetings
2. Fetches Fireflies transcript via browser automation
3. Creates meeting note + updates activity log
4. Drafts Swedish follow-up email
5. Presents results for review

**Prompt:**
```
You are running the daily meeting follow-up workflow.

## Steps

1. **Get yesterday's meetings** -- Use `gcal_list_events` with timezone `Europe/Stockholm`. If today is Monday, check Friday instead.

2. **Filter to external meetings** -- A meeting is external if any attendee has an email that does NOT contain `@emaxmedia.se`. Match to a client using `[VAULT_ROOT]/_templates/client-project-mapping.md`.

3. **For each client meeting:**
   a. Fetch the Fireflies transcript via Claude-in-Chrome -- navigate to `https://app.fireflies.ai/notebooks`, find the meeting, extract AI summary and action items
   b. Create a meeting note at `Emax/{client}/meetings/YYYY-MM-DD-{topic-slug}.md`
   c. Update the client's `activity-log.md` with a rich meeting entry
   d. Draft a follow-up email in Swedish (Tack för mötet, Sammanfattning, Åtgärder, Nästa steg)

4. **Present results** -- Show draft emails in chat for review.

If no client meetings happened yesterday, report "Inga kundmöten igår" and exit.
If browser automation is unavailable, notify: "Kunde inte hämta Fireflies-transkript automatiskt."

Swedish characters: All output MUST use proper å, ä, ö.
```

---

### 2. friday-weekly-review

- **Schedule:** `0 14 * * 5` -- Friday 14:00
- **Description:** Compile weekly CRM comments from KB context and calendar
- **Requires:** Google Calendar MCP

**What it does:**
1. Reads all client activity logs for the week
2. Checks calendar for meetings that happened
3. Identifies gaps (clients needing context)
4. Generates CRM-ready comments in Swedish
5. Presents for approval before logging

**Prompt:**
```
You are running the Friday weekly review for [YOUR_NAME]'s Emax client management system.

Read the SOP at `[VAULT_ROOT]/SOPs/weekly-client-review.md`.

Reference files:
- Client mapping: `[VAULT_ROOT]/_templates/client-project-mapping.md`

## Steps

1. **Read KB activity logs** for each client -- check `activity-log.md` for this week's entries and `meetings/` for notes created this week.

2. **Check Google Calendar** via `gcal_list_events` (Europe/Stockholm) for meetings this week.

3. **Identify gaps** -- Flag clients with meetings or known activity but no KB context. Present to [YOUR_NAME]:
   > "Dessa kunder behöver kontext: [list]. Dela Fireflies-länkar eller snabba anteckningar."
   Wait for response before proceeding.

4. **Generate CRM comments** in Swedish, Data Pulse style:
   - What was done (campaign changes, optimizations, creative work)
   - Meeting outcomes and client sentiment
   - Ad performance context (if available)
   - Next steps
   - Length: 3-5 sentences for high activity, 1-2 for low activity

5. **Present comments** for [YOUR_NAME]'s approval.

6. **Update KB** -- Append weekly review entries to each client's `activity-log.md`.

This task requires user interaction at steps 3 and 5 -- do not skip these checkpoints.
Swedish characters: All output MUST use proper å, ä, ö.
```

---

### 3. friday-review-nudge

- **Schedule:** `0 8 * * 5` -- Friday 08:00 (runs before the weekly review)
- **Description:** Flag clients with activity but missing KB context

**Prompt:**
```
You are running the Friday morning review nudge for [YOUR_NAME]'s client knowledge base.

## Steps

1. **Check Google Calendar** for this week's client meetings (filter by non-@emaxmedia.se attendees).

2. **Check the KB** for each client -- read `activity-log.md` for this week's entries.

3. **Report:**

   Ready (have KB context):
   - [Client] -- [brief note]

   Need context before review:
   - [Client] -- meeting on [day] but no KB entries
     -> Share Fireflies link? Quick notes?

   No activity this week:
   - [Client list]

4. **Remind**: "Share any Fireflies links or quick notes for the flagged clients, then say 'run weekly review' when ready."

Client mapping: `[VAULT_ROOT]/_templates/client-project-mapping.md`
```

---

### 4. biweekly-review-reminder

- **Schedule:** `30 7 * * 1-5` -- Weekdays 07:30
- **Description:** Auto-generate meeting prep briefs for client meetings in the next 1-2 days
- **Requires:** Google Calendar MCP

**Prompt:**
```
Check Google Calendar for client meetings tomorrow and the day after (if Thursday, also check Monday; if Friday, check Monday and Tuesday).

Filter to external meetings (non-@emaxmedia.se attendees). Match to clients via `[VAULT_ROOT]/_templates/client-project-mapping.md`.

For each upcoming client meeting:
1. Read profile.md, activity-log.md, strategy.md
2. Compile a brief: recent work, open items, talking points, performance highlights
3. Save to `Emax/{client}/meetings/YYYY-MM-DD-prep.md`
4. Present the brief in chat

Skip meetings that already have a prep file.
```

---

### 5. weekly-crm-sync

- **Schedule:** `43 7 * * 1` -- Monday 07:43
- **Description:** Sync CRM notes, grades, and contracts to vault files
- **Requires:** Supabase credentials in `.env`

**Prompt:**
```
Sync Emax CRM data to the vault knowledge base.

1. Source `.env` for Supabase credentials
2. Read `[VAULT_ROOT]/_templates/crm-client-mapping.md` for CRM ID -> vault folder mapping
3. For each mapped client:
   a. Fetch new CRM notes since last Monday -> append to activity-log.md (tag as [CRM sync], skip existing dates)
   b. Check grade changes -> update profile.md if different
   c. Check contract updates -> update strategy.md if newer period exists
4. Write sync summary to `_daily/YYYY-MM-DD.md`

READ-ONLY on CRM -- only GET requests. Run autonomously.
```

---

### 6. weekly-safety-net

- **Schedule:** `0 8 * * 3` -- Wednesday 08:00
- **Description:** Mid-week scan for missing follow-ups, quiet clients, and gaps

**Prompt:**
```
Mid-week safety check for [YOUR_NAME]'s client management system.

## Checks

1. **Calendar meetings without KB notes** -- external meetings from last 7 days with no meeting note file
2. **Quiet clients** -- no activity-log entries in 14+ days
3. **Pending action items** -- unchecked `- [ ] [YOUR_NAME]` lines in meeting notes from last 30 days

## Report

Group findings: NEEDS ATTENTION -> REVIEW -> SUMMARY. Omit empty sections.
If all clear: "Inga glömda uppgifter hittades."

Client mapping: `[VAULT_ROOT]/_templates/client-project-mapping.md`
Swedish characters: All output MUST use proper å, ä, ö.
```

---

## Optional (Requires Todoist)

These tasks integrate with Todoist for task management. Only install if you use Todoist.

### 7. monday-scrum-and-distribution

- **Schedule:** `0 8 * * 1` -- Monday 08:00
- **Description:** Generate weekly scrum doc and distribute tasks across the week

### 8. meeting-prep-todos

- **Schedule:** `0 8 * * 1` -- Monday 08:00
- **Description:** Create Todoist prep + follow-up tasks for this week's meetings

---

## Not Yet Adapted

These tasks exist in the original system but need adaptation for paid social before they can be used:

### 9. monthly-performance-pull

Currently pulls SEO report data (GSC/GA). Needs rewrite to pull paid social metrics (Meta Ads spend, ROAS, conversions) once the CRM supports it.

### 10. weekly-systems-review

Analyzes automation health and proposes improvements. Can be added later once the system is stable and you want to optimize workflows.
