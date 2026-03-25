# Client Knowledge Base

Welcome to your client knowledge base. This Obsidian vault is the single source of truth for all your client context at Emax.

## Getting Started

If this is your first time, read **SETUP-PROMPT.md** and follow the setup instructions with Claude Code.

## How It Works

- **CLAUDE.md** -- The automation rules Claude follows (auto-read context, auto-log work, daily journal)
- **_templates/** -- File templates for new clients, meetings, and mappings
- **SOPs/** -- Standard operating procedures for recurring workflows
- **Emax/** -- Your client folders (one per client, each with profile, activity log, strategy, meetings)
- **_daily/** -- Cross-client daily session journals

## Key Concepts

1. **When you mention a client**, Claude silently loads their profile, activity log, and strategy
2. **After meaningful work**, Claude automatically logs what was done to the client's activity log
3. **Activity logs feed CRM comments** -- write rich entries for better weekly reviews
4. **Fireflies transcripts** become structured meeting notes with one link share

## Quick Reference

| I want to... | Do this |
|--------------|---------|
| Add a new client | Create `Emax/[Name]/` with profile.md, activity-log.md, strategy.md |
| Process a meeting | Share the Fireflies link with Claude |
| Run weekly review | Say "run weekly review" on Friday |
| See available automations | Read `SOPs/available-automations.md` |
