# Client ↔ Folder Mapping

Single source of truth for client → folder path resolution. Used by all skills and automation workflows.

> **Vault root:** `[VAULT_ROOT]`
> To construct an absolute path: prepend vault root + folder path from table below.

Fill in your clients as you onboard them. Add one row per client.

## Emax Clients

| Client Name | Folder Path | Todoist Project ID (optional) |
|-------------|-------------|-------------------------------|
|             |             |                               |

<!-- Example:
| Acme Corp | `Emax/Acme Corp/` | `abc123` |
| Big Brand | `Emax/Big Brand/` |           |
-->

## Common Aliases

Used for fuzzy matching calendar events and meeting titles to clients:

| Alias | Maps to |
|-------|---------|
|       |         |

<!-- Example:
| Acme | Acme Corp |
| BB   | Big Brand |
-->

## Meeting Filtering Rule

**Primary filter:** Attendee email addresses. A meeting is considered external (and therefore a client meeting) if **any** attendee has an email that does **not** contain `@emaxmedia.se`. This catches meetings regardless of who created/invited, or what the title says.

**Secondary step:** Match the external meeting to a client using the names/aliases above, or the external attendee's email domain. If no match is found, flag it for [YOUR_NAME].

### Known internal-only meeting titles (for reference)

- Fånga dagen
- Veckans Kunskapsboost

<!-- Add any other recurring internal meetings here so they're filtered out -->
