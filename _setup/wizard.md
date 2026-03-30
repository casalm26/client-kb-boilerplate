# Setup Wizard — Emax Paid Social KB

This file is read by Claude, not by the user. CLAUDE.md auto-triggers this when the system is unconfigured.

---

## Behavior Rules

1. **One question at a time.** Use AskUserQuestion for structured choices. Plain chat for free-text input.
2. **Show progress.** Use TodoWrite. Mark tasks complete as you go.
3. **Validate immediately.** After connecting any tool, test it and report the result.
4. **Be concise.** Short sentences. No lectures.
5. **Skip gracefully.** If something fails, say "Skipping — you can add this later" and move on.
6. **Batch silent work.** Replace placeholders and create files silently, then confirm the result.
7. **Swedish characters.** All Swedish text must use proper å, ä, ö.

---

## Entry Point

Start with:

> Welcome! This is your client knowledge base for Emax. It automatically loads client context, logs your work, processes Fireflies meetings, and compiles weekly CRM comments for Data Pulse.
>
> Let's get you set up — it takes about 5 minutes.

Create TodoWrite progress:
```
□ Set up your profile
□ Connect your tools
□ Add your clients
□ Choose automations
□ Verify everything works
```

---

## Phase 1: Profile

Mark "Set up your profile" as in_progress.

**Step 1.1 — Name**
Ask: "What's your first name?"

**Step 1.2 — Role**
Use AskUserQuestion:
- Question: "What's your primary role at Emax?"
- Options:
  - **Paid Social** — "Meta Ads, campaign management, creative strategy"
  - **Paid Social + other services** — "Meta Ads plus content, strategy, or other"
  - (Other as free text)

**Step 1.3 — Platforms**
Use AskUserQuestion:
- Question: "Which ad platforms do you work with?"
- Multi-select: true
- Options:
  - **Meta Ads (Facebook/Instagram)**
  - **Google Ads**
  - **TikTok Ads**
  - **LinkedIn Ads**

If they select platforms beyond Meta, note this — the profile template's "Ad Accounts & Technical Setup" table should include rows for each platform.

**Step 1.4 — Apply configuration**
Detect vault root (the directory containing this file's parent).

Replace all placeholders across all files:
- `[YOUR_NAME]` → their name
- `[VAULT_ROOT]` → detected absolute path

Files: CLAUDE.md, SOPs/fireflies-processing.md, SOPs/weekly-client-review.md, SOPs/available-automations.md, Emax/_emax-overview.md, _templates/client-project-mapping.md, _templates/crm-client-mapping.md, _templates/solution.md, _setup/settings.local.json

Update `Emax/_emax-overview.md` with their name, role, and platforms.

Mark "Set up your profile" as completed.

Confirm: "Profile configured for [name] — [role] at Emax."

---

## Phase 2: Tools

Mark "Connect your tools" as in_progress.

**Step 2.1 — Check what's connected**
Silently check:
- Claude in Chrome: try `mcp__Claude_in_Chrome__tabs_context_mcp`
- Google Calendar: search MCP registry
- Node.js: `node --version`

Present status:
```
Tools status:
✅/❌ Claude in Chrome — [connected/not detected]
✅/❌ Google Calendar — [connected/not connected]
✅/❌ Node.js — [installed/not found]
```

**Step 2.2 — Fix missing baseline tools**

**Claude in Chrome** (if missing):
"Claude in Chrome lets me fetch Fireflies transcripts and scan Gmail automatically. Install it from the Chrome Web Store, click the extension icon, and select 'Connect to Claude Code'. Let me know when done."
After they confirm → test → report result.

**Google Calendar** (if missing):
Search MCP registry → suggest connector → guide auth with @emaxmedia.se account → test → report.
Add MCP permission entries to `.claude/settings.local.json` with detected UUID.

**Step 2.3 — Supabase CRM credentials**
Use AskUserQuestion:
- Question: "Do you have your Supabase CRM key ready?"
- Options:
  - **Yes, I have it** — "I'll paste it now"
  - **No, I'll add it later** — "Get it from Caspian or the dev team"

If yes: create `.env` from `.env.example`, ask them to paste the key, write it.
If no: create `.env` with placeholder. "CRM sync won't work until you add the key, but everything else will."

**Step 2.4 — Optional: Todoist**
Use AskUserQuestion:
- Question: "Do you use Todoist for task management?"
- Options:
  - **Yes** — "I'll connect it — enables Monday scrum and task distribution"
  - **No** — "Skip — the KB works fine without it"

If yes: ask for API token → add to `.env` → uncomment Todoist section in CLAUDE.md → test → report.

**Step 2.5 — Install permissions and structure**
```bash
mkdir -p .claude _solutions
cp _setup/settings.local.json .claude/settings.local.json
```
Add any tool-specific permissions discovered above.

Mark "Connect your tools" as completed.

---

## Phase 3: Add Clients

Mark "Add your clients" as in_progress.

Ask: "Which Emax clients do you manage? List their names and I'll set up folders for each one."

For each client:
1. Create `Emax/{Client Name}/` with profile.md, activity-log.md, strategy.md, meetings/
2. Fill in `{{title}}` placeholders with the client name
3. Add to `_templates/client-project-mapping.md`

If CRM credentials are configured, automatically query Supabase for each client:
```bash
source .env
curl -s "${SUPABASE_URL}/clients?select=id,name&name=ilike.*{search}*" \
  -H "apikey: ${SUPABASE_KEY}" -H "Authorization: Bearer ${SUPABASE_KEY}"
```
If found → add to `_templates/crm-client-mapping.md`.
If not found → note it, the user can add manually later.

Ask: "Any short names or nicknames you use for these clients?"
→ Add to aliases table in client-project-mapping.md.

If Todoist was connected: "Do you have Todoist project IDs for these clients?" → add to mapping if yes.

Use AskUserQuestion:
- Question: "Want to fill in a client profile now as an example?"
- Options:
  - **Yes, let's do one** — "I'll walk through the first client"
  - **No, I'll do it as I go** — "Claude loads whatever context exists when you mention a client"

If yes: pick their first client, walk through profile.md — business overview, contacts, ad accounts, pixel setup, audiences, goals.

Mark "Add your clients" as completed.

---

## Phase 4: Automations

Mark "Choose automations" as in_progress.

"The KB has automated workflows that run on a schedule. I'll only show ones that work with your connected tools."

Build the list based on Phase 2:

**Always available:**
- Friday Weekly Review — "Compile CRM-ready comments from activity logs every Friday"
- Friday Review Nudge — "Flag clients needing context before the review" (needs Calendar)
- Weekly Safety Net — "Mid-week catch for forgotten follow-ups"
- Weekly Systems Review — "Analyse automation health and propose improvements every Friday"

**Available if Claude in Chrome + Calendar connected:**
- Daily Meeting Follow-up — "Process yesterday's Fireflies meetings into notes + emails each morning"
- Meeting Prep Briefs — "Auto-generate talking points before client meetings"

**Available if CRM credentials configured:**
- Weekly CRM Sync — "Pull notes and grades from Supabase into vault files every Monday"

**Available if Todoist connected:**
- Monday Scrum + Distribution — "Generate weekly scrum and distribute tasks"
- Meeting Prep Todos — "Create Todoist tasks for upcoming meeting prep"

Use AskUserQuestion (multi-select):
- Question: "Which automations do you want to activate? You can add more anytime."
- Show only relevant options from above
- Include: **None for now** — "Use the KB manually first"

For each selected:
1. Read the prompt from `SOPs/available-automations.md`
2. Install with `mcp__scheduled-tasks__create_scheduled_task`
3. Confirm: "✓ [Name] — runs [schedule]"

Mark "Choose automations" as completed.

---

## Phase 5: Verify

Mark "Verify everything works" as in_progress.

**Test 1: Client context**
Pick one of their clients: "Loading [client]'s context..." → read profile.md → "✓ Client context loaded."

**Test 2: CRM** (if configured)
Test Supabase query → "✓ CRM connected — [N] clients found." or "Skipped — CRM not configured yet."

**Test 3: Calendar** (if connected)
Fetch events → "✓ Calendar — [N] events this week."

**Test 4: Claude in Chrome** (if connected)
Test connection → "✓ Browser automation ready."

**Test 5: Daily journal**
Write to `_daily/YYYY-MM-DD.md`:
```
### HH:MM — KB Setup Complete
- What: Set up client KB with [N] clients, [N] tools connected, [N] automations
- Next: Fill in client profiles, process first meeting
```

Mark "Verify everything works" as completed.

---

## Summary

```
All done, [name]!

Clients: [list]
Tools: [list]
Automations: [list]

Day-to-day:
- Mention a client → I load their context
- After work → I log it automatically
- Share a Fireflies link → I create meeting notes + draft a follow-up email
- "weekly review" on Fridays → I compile CRM comments for Data Pulse
- "reconfigure" → change settings or add tools/clients
```

---

## Re-entry

When triggered by CLAUDE.md for reconfiguration:

1. "add tools" / "connect tools" → run Phase 2
2. "add clients" → run Phase 3
3. "add automations" → run Phase 4
4. "reconfigure" / "change settings" → ask which phase:

Use AskUserQuestion:
- Question: "What do you want to change?"
- Options:
  - **Connect more tools**
  - **Add clients**
  - **Change automations**
  - **Full reconfigure**
