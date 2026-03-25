# Setup Prompt: Client Knowledge Base

Feed this entire file to Claude Code to set up your client knowledge base system. Claude will interview you and configure everything interactively.

---

## Instructions for Claude

You are setting up a client knowledge base (KB) system built on Obsidian for a consultant at Emax Media (a digital marketing consultancy in Sweden). This system automates client context loading, activity logging, meeting processing, and weekly CRM reporting.

**Your approach:** Run this as an interactive setup wizard. Ask questions, wait for answers, then configure. Do not dump all information at once. Move through phases one at a time, confirming each before proceeding.

**Important rules:**
- After each configuration action, briefly confirm what you did
- When replacing placeholders in files, do all files in one batch silently — don't narrate each file
- If something fails (missing tool, credential issue), explain the problem and offer to skip and come back later
- Use Swedish characters (å, ä, ö) correctly in all Swedish text you write

---

## Phase 1: Introduction & Personal Setup

Start the conversation with a brief welcome. Explain in 3-4 sentences what this system does: it's a client knowledge base that automatically loads client context, logs your work, processes meeting transcripts, and compiles weekly CRM comments.

Then ask the following questions (use the AskUserQuestion tool or ask in chat):

1. **"What's your first name?"** — This will be used throughout the system so Claude knows how to refer to you.

2. **"What's your role at Emax?"** — e.g., Paid Social Consultant, Digital Marketing Specialist. This goes in your Emax overview file.

3. **"What's your primary focus area?"** — The templates are pre-configured for Meta Ads / paid social. Ask if this is correct or if they also do Google Ads, TikTok, content, or other services. If their focus differs significantly from paid social, note this — you'll adapt the templates accordingly.

**After getting answers:**
- Detect the vault root path (the directory this file is in — use `pwd` or derive from the file path)
- Replace `[YOUR_NAME]` with their first name in ALL files that contain it:
  - `CLAUDE.md`
  - `SOPs/fireflies-processing.md`
  - `SOPs/weekly-client-review.md`
  - `SOPs/available-automations.md`
  - `Emax/_emax-overview.md`
  - `_templates/client-project-mapping.md`
- Replace `[VAULT_ROOT]` with the detected absolute vault path in ALL files that contain it:
  - `CLAUDE.md`
  - `SOPs/available-automations.md`
  - `_templates/client-project-mapping.md`
  - `_templates/crm-client-mapping.md`
  - `_setup/settings.local.json`
- Update `Emax/_emax-overview.md` with their name, role, and focus area
- If their focus area isn't purely Meta Ads, adapt the profile and strategy templates to include their platforms

Confirm: "Done — I've personalized all the files. Your vault is now configured for [name], [role] at Emax."

---

## Phase 2: Tools & Integrations

Explain: "Now let's connect the tools you'll use day-to-day. I'll check what's already available and help you set up the rest."

### 2.1 Check what's already connected

Run these checks silently:
- `node --version` — is Node.js installed?
- Check if Claude in Chrome MCP is available (try `mcp__Claude_in_Chrome__tabs_context_mcp`)
- Check if Google Calendar MCP is available (search the MCP registry)
- Check if Google Drive MCP is available

Then present a status summary:

```
Tools status:
✅ Claude Code — connected (you're using it now)
✅/❌ Node.js — [installed/not found]
✅/❌ Claude in Chrome — [connected/not detected]
✅/❌ Google Calendar — [connected/not connected]
✅/❌ Google Drive — [connected/not connected]
```

### 2.2 Fix missing baseline tools

For each missing tool, walk the user through setup:

**Node.js** (if missing): "Install Node.js from https://nodejs.org — it's needed for some background tools. We can continue without it for now."

**Claude in Chrome** (if missing):
1. "Install the 'Claude in Chrome' extension from the Chrome Web Store"
2. "Click the extension icon and select 'Connect to Claude Code'"
3. "Let me know when it's done and I'll verify the connection"
4. After they confirm, test with `mcp__Claude_in_Chrome__tabs_context_mcp`

**Google Calendar** (if missing):
1. Search the MCP registry: `mcp__mcp-registry__search_mcp_registry` with keywords ["google", "calendar"]
2. If found, suggest the connector: `mcp__mcp-registry__suggest_connectors`
3. "Click Connect and sign in with your @emaxmedia.se Google account"
4. After connected, test with a calendar query

### 2.3 Ask about optional tools

Ask: **"Do you want to connect any of these optional tools?"**

Present as a multi-select choice:
- **Todoist** — "For task management. Each client gets a Todoist project. Enables Monday scrum generation and automated task distribution."
- **Google Drive** — "For accessing shared client documents directly from Claude."
- **None for now** — "You can always add these later."

If they choose Todoist:
- Uncomment the Todoist line in `.env.example` (and later in `.env`)
- Uncomment the Todoist section in `CLAUDE.md`
- Note that they'll need their API token (Settings > Integrations > Developer in Todoist)

### 2.4 Set up credentials (.env)

Ask: **"Do you have your Supabase CRM key ready? (You can get this from Caspian or the dev team)"**

- If yes: create `.env` from `.env.example`, ask them to paste the key, write it to the file
- If no: create `.env` with the URL pre-filled and a placeholder for the key. Say: "No problem — ask Caspian for the Supabase service role key and paste it into `.env` later. The CRM sync won't work until then, but everything else will."
- If Todoist was selected: also ask for the Todoist API token

### 2.5 Install permissions

Copy the settings file into place:
```bash
mkdir -p .claude
cp _setup/settings.local.json .claude/settings.local.json
```

Then update it:
- The `[VAULT_ROOT]` placeholder should already be replaced from Phase 1
- If Google Calendar MCP was connected, detect its UUID and add the permission entries:
  ```
  "mcp__{uuid}__gcal_list_events",
  "mcp__{uuid}__gcal_get_event",
  "mcp__{uuid}__gcal_list_calendars"
  ```
- If Todoist was selected, add: `"Bash(curl -s *api.todoist.com*)"`

Confirm: "Permissions configured. Claude Code now has access to read your vault, query the CRM, and use your connected tools."

---

## Phase 3: Add Clients

Ask: **"Let's add your clients. Which Emax clients do you manage? List their names and I'll set up folders for each one."**

Wait for the list. Then for each client:

1. Create the folder structure:
   ```
   Emax/{Client Name}/
   ├── profile.md      (from template, with client name filled in)
   ├── activity-log.md  (from template)
   ├── strategy.md      (from template)
   └── meetings/
   ```

2. Add to `_templates/client-project-mapping.md`

3. If the CRM credentials are configured, automatically query Supabase to find the CRM ID:
   ```bash
   source .env
   curl -s "${SUPABASE_URL}/clients?select=id,name&name=ilike.*{search_term}*" \
     -H "apikey: ${SUPABASE_KEY}" \
     -H "Authorization: Bearer ${SUPABASE_KEY}"
   ```
   If found, add to `_templates/crm-client-mapping.md` automatically.

4. If Todoist was selected, ask: "Do you have Todoist project IDs for these clients?" — if yes, add them to the mapping.

After creating all client folders, ask: **"Want to fill in any client profiles now, or do that later as you go?"**

- If now: pick the first client, ask for business overview, contacts, ad accounts, pixel setup, audiences, current goals. Fill in profile.md and strategy.md.
- If later: "No problem. Claude will auto-load whatever context exists when you mention a client. You can fill in profiles organically as you work."

Also ask: **"Any short names or aliases you use for these clients?"** — add to the aliases table in client-project-mapping.md.

---

## Phase 4: Automations

Explain: "This system comes with automated workflows that can run on a schedule. Let me show you what's available and you pick what you want."

Present the automation catalog interactively. Group them:

**Recommended (work out of the box):**
| # | Automation | What it does | When |
|---|-----------|-------------|------|
| 1 | Daily meeting follow-up | Fetches yesterday's Fireflies transcripts, creates meeting notes, drafts follow-up emails | Weekday mornings |
| 2 | Friday review nudge | Flags clients that need context before the weekly review | Friday 08:00 |
| 3 | Friday weekly review | Compiles CRM-ready comments from activity logs | Friday 14:00 |
| 4 | Meeting prep briefs | Generates talking points before upcoming client meetings | Weekday mornings |
| 5 | Weekly CRM sync | Pulls notes and grades from Supabase into vault files | Monday morning |
| 6 | Weekly safety net | Catches forgotten follow-ups, quiet clients, orphaned tasks | Wednesday morning |

**Requires Todoist:** (only show if Todoist was connected)
| 7 | Monday scrum | Generates weekly scrum doc from completed tasks |
| 8 | Meeting prep todos | Creates Todoist tasks for upcoming meeting prep |

Ask: **"Which automations do you want to activate? You can start with a few and add more later."**

For each selected automation:
1. Read the prompt from `SOPs/available-automations.md`
2. Replace any remaining `[YOUR_NAME]` and `[VAULT_ROOT]` placeholders
3. Install using `mcp__scheduled-tasks__create_scheduled_task` with the correct taskId, cronExpression, description, and prompt
4. Confirm each one: "Installed: [name] — runs [schedule]"

If they choose none: "That's fine. The core auto-behaviors (context loading, activity logging, daily journal) work without any scheduled tasks. You can install automations later from `SOPs/available-automations.md`."

---

## Phase 5: Test Run

Explain: "Let's make sure everything works. I'll run a few quick tests."

### Test 1: Client context loading
Pick one of their clients and say: "Let me try loading [client name]'s context..."
- Read the client's profile.md
- Confirm: "Client context loaded. I can see [client name]'s profile."

### Test 2: CRM connection (if configured)
- Run a test query against Supabase
- Confirm: "CRM connection works. I can see [X] clients in the database."
- Or: "CRM not configured yet — skipping this test."

### Test 3: Google Calendar (if connected)
- Fetch today's events
- Confirm: "Calendar connected. I can see your schedule."
- Or: "Calendar not connected — skipping."

### Test 4: Claude in Chrome (if connected)
- Test the connection
- Confirm: "Browser automation ready. I can read web pages for Fireflies transcripts."
- Or: "Browser not connected — you can set this up later."

### Test 5: Activity logging
- Write a test entry to the daily journal (`_daily/YYYY-MM-DD.md`):
  ```
  ### HH:MM — KB Setup Complete
  - What: Set up client knowledge base with [X] clients, [Y] automations
  - Next: Fill in client profiles, process first meeting
  ```
- Confirm: "Daily journal entry written."

---

## Phase 6: Wrap-up

Summarize what was set up:

```
Setup complete! Here's your system:

Name: [name]
Role: [role]
Clients: [list]
Connected tools: [list]
Active automations: [list]
CRM: [configured/pending]

What to do next:
1. Fill in client profiles as you work (Claude will prompt you)
2. Share Fireflies links after meetings — Claude processes them automatically
3. On Fridays, say "run weekly review" to generate CRM comments
4. Check SOPs/available-automations.md to add more automations later
```

End with: "You're all set. Just mention a client name and I'll have their context ready."

---

## Reference: File Locations

These files are part of the vault (for Claude's reference during setup):

| File | Purpose |
|------|---------|
| `CLAUDE.md` | Core automation rules (auto-read, auto-log, journal) |
| `_templates/client-profile.md` | Profile template (ad accounts, pixels, audiences) |
| `_templates/strategy.md` | Strategy template (ROAS, CPA, CTR, spend) |
| `_templates/activity-log.md` | Activity log template |
| `_templates/meeting-note.md` | Meeting note template |
| `_templates/client-project-mapping.md` | Client-to-folder mapping |
| `_templates/crm-client-mapping.md` | CRM ID mapping |
| `SOPs/fireflies-processing.md` | Meeting transcript workflow |
| `SOPs/weekly-client-review.md` | Weekly CRM review process |
| `SOPs/available-automations.md` | Automation catalog with prompts |
| `_setup/settings.local.json` | Permission whitelist template |
| `.env.example` | Credential template |
| `Emax/_emax-overview.md` | Personal Emax overview |
