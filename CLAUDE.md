# Setup Check

**If this file still contains the literal text `[YOUR_NAME]`, the system is not yet configured.**
Read and follow `_setup/wizard.md` immediately. Do not proceed with normal KB operations until setup is complete.

**If the user says "reconfigure", "change settings", "add tools", or "add clients":**
Read `_setup/wizard.md` and run only the relevant phase.

---

# Client Knowledge Base

This is [YOUR_NAME]'s client knowledge base. It contains profiles, activity logs, strategies, and meeting notes for Emax clients (paid social / digital marketing).

Claude should treat this KB as the single source of truth for all client context.

> **Vault root:** `[VAULT_ROOT]`
> All relative paths in this file are relative to this root. Always prepend this root when constructing absolute paths.

---

## Auto-Read: Client Context Loading

**ALWAYS** do this when a client is mentioned by name or abbreviation:

1. Read their `profile.md` — gives you business context, contacts, services, goals
2. If the conversation involves recent work or history, also read `activity-log.md`
3. If strategy, priorities, or planning is discussed, also read `strategy.md`

Do this silently. Do not tell [YOUR_NAME] you're reading files — just have the context ready.

### Path Resolution

To resolve any client name to an absolute file path:

1. Read `_templates/client-project-mapping.md` — the single source of truth for client names, folder paths, and aliases.
2. Match the client name (or abbreviation) to a row in the mapping tables.
3. Prepend the vault root to the folder path.
4. Append the target file (`profile.md`, `activity-log.md`, `strategy.md`, or `meetings/`).

**Example:** "ClientName profile" → mapping gives `Emax/ClientName/` → `[VAULT_ROOT]/Emax/ClientName/profile.md`

---

## Auto-Retrieve: Knowledge Before Work

Before starting non-trivial work, **silently** search for relevant past knowledge. Do this the same way Auto-Read loads client profiles — have the context ready without announcing it.

1. **Search `_solutions/`** — grep frontmatter and headings for matching problem types, tags, or area names relevant to the current task
2. **Scan memory files** — check `MEMORY.md` index for relevant learnings (feedback, references, patterns)
3. **Check recent activity** — if working on a client, scan their last 3-5 `activity-log.md` entries

**When to trigger:** Tasks that involve debugging, building/modifying automations, processing data from external tools, or complex client work.

**When NOT to trigger:** Simple lookups, file reads, quick edits, or straightforward tasks where past context won't change the approach.

If relevant solutions or learnings are found, use them to inform the approach. If a past solution directly applies, reference it rather than re-discovering the fix.

---

## Auto-Log: Activity Logging After Work

After completing meaningful work for a client during a session, **automatically** append a dated entry to their `activity-log.md`. Do not ask for permission.

**Format:**

```markdown
## YYYY-MM-DD
**[Short title of work done]**

[2-4 sentences: what was done, why, and what's next. Include deliverable names, key decisions, and any pending items.]
```

**Newest entries go at the top** (below the frontmatter/header).

**What counts as meaningful work:**
- Creating deliverables (campaign briefs, ad creatives, audience analyses, reports)
- Completing strategic decisions or planning
- Processing meeting notes or transcripts
- Significant client communication or analysis
- Campaign launches, optimizations, or structural changes

**Do NOT log:** File formatting, quick lookups, reading files, minor edits.

Write **rich** entries — not just "did X" but "did X because Y, result was Z, next step is W." These feed directly into CRM comments.

---

## Auto-Update: Strategy Changes

When strategic decisions are made during a session (new goals, changed priorities, KPI updates, retainer changes, budget adjustments, campaign direction shifts), **automatically** update the relevant sections in the client's `strategy.md`. Keep existing content and update the relevant sections — don't overwrite the whole file.

---

## Auto-Compound: Solution Documentation After Problem-Solving

After solving a non-trivial problem during a session, **offer** to document it as a solution. This captures the investigation and fix while context is fresh.

**When to offer:**
- After debugging an automation failure or workaround
- After figuring out a tool integration or formatting issue
- After resolving a data or workflow problem (CRM, Todoist, calendar)
- After any "that was hard to figure out" moment

**How:** Ask lightly — "This seems worth documenting as a solution. Should I save it to `_solutions/`?" If [YOUR_NAME] says no, move on. If yes, create a file at `_solutions/YYYY-MM-DD-{slug}.md` using the template at `_templates/solution.md`.

**Problem types** (use in frontmatter, extensible):
`automation-failure`, `tool-integration`, `cms-formatting`, `client-workflow`, `data-issue`

Do NOT document routine work, simple fixes, or things that are obvious from the code. Solutions should capture knowledge that would otherwise be lost.

---

## Daily Journal

At session **START**, silently read `_daily/YYYY-MM-DD.md` (today) and yesterday's if it exists. This provides cross-session continuity.

At session **END** (if meaningful work was done across any clients), append to `_daily/YYYY-MM-DD.md`:

```markdown
### HH:MM — [Brief title]
- Client(s): [names]
- What: [1-2 sentences]
- Next: [pending items]
```

The daily journal complements per-client activity logs. Activity logs are the detailed record; the daily journal is a lightweight cross-client index of the day's work.

---

## Graceful Degradation

When a client file is missing or empty:
- **profile.md missing** → note internally, continue with available context, offer to scaffold after current task
- **activity-log.md missing** → treat as new client with no history, create the file with proper header when first logging
- **strategy.md missing** → skip strategy context, do not fail
- **meetings/ missing** → create the directory when saving the first meeting note

When a referenced file (SOP, template, skill) does not exist:
- Log the broken reference in the daily journal
- Continue with best-effort behavior
- Do not interrupt [YOUR_NAME] about infrastructure issues mid-task

---

## Context Preservation

During long sessions, proactively write important state to the daily journal before it gets lost to context compression:
- Decisions made this session
- Client context shared verbally but not yet written to KB files
- In-progress work state (what's done, what's next)

After context compression, re-read the daily journal to recover state.

---

## Procedures & References

Detailed procedures are in dedicated files — read them on demand when the relevant workflow is triggered:

| Topic | Where to find it |
|-------|-----------------|
| Meeting notes format | `_templates/meeting-note.md` |
| Fireflies processing | `SOPs/fireflies-processing.md` |
| Weekly review (two-layer system) | `SOPs/weekly-client-review.md` |
| Client → folder mapping | `_templates/client-project-mapping.md` |
| New client file templates | `_templates/` (profile, strategy, activity-log) |
| Solution documentation format | `_templates/solution.md` |
| Available automations | `SOPs/available-automations.md` |

## Vault Structure

```
{client}/
├── profile.md        — Company info, contacts, services, goals
├── activity-log.md   — Chronological work log (newest first)
├── strategy.md       — Current priorities, KPIs, retainer details
└── meetings/         — Individual meeting note files (YYYY-MM-DD-topic.md)
```

- `Emax/_emax-overview.md` — [YOUR_NAME]'s role at Emax, team, tools, workflows
- `SOPs/` — Standard operating procedures
- `_templates/` — File format templates
- `_daily/` — Cross-client daily session journals
- `_solutions/` — Cross-cutting problem→solution documentation (searchable by problem type and tags)

## Data Sources

Always check the KB first before external sources. The KB is the canonical view.

| Source | Access |
|--------|--------|
| Fireflies.ai | [YOUR_NAME] pastes links, or browse via browser automation |
| Google Calendar | Google Calendar MCP tools |
| Google Drive | Google Drive MCP tools |
| Gmail | [YOUR_NAME] shares relevant threads |
| Supabase CRM | REST API (read-only, credentials in `.env`) |

<!-- OPTIONAL: Todoist Integration
If you use Todoist for task management, uncomment the line below and add your project IDs to client-project-mapping.md.

| Todoist | Todoist MCP tools or REST API (token in `.env`) |
-->
