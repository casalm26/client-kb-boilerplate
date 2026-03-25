# CRM Client ID Mapping

Maps Supabase CRM client IDs to vault folder paths. Used by all CRM-aware skills and scheduled tasks.

Built from `clients` table matched against `client-project-mapping.md`. Update when clients are added/removed.

## Emax Clients

| CRM ID | CRM Name | Vault Folder |
|--------|----------|--------------|
|        |          |              |

<!-- Example:
| `33135c04-c2d5-48fb-9d0d-a184eedba609` | Acme Corp AB | `Emax/Acme Corp/` |
-->

## How to Find CRM IDs

Query the Supabase `clients` table:

```bash
source "[VAULT_ROOT]/.env"

curl -s "${SUPABASE_URL}/clients?select=id,name&order=name" \
  -H "apikey: ${SUPABASE_KEY}" \
  -H "Authorization: Bearer ${SUPABASE_KEY}"
```

## API Reference

```bash
# Source credentials
source "[VAULT_ROOT]/.env"

# Query pattern
curl -s "${SUPABASE_URL}/{table}?{filters}" \
  -H "apikey: ${SUPABASE_KEY}" \
  -H "Authorization: Bearer ${SUPABASE_KEY}"
```

## Key Tables

| Table | Purpose |
|-------|---------|
| `clients` | Client metadata (id, name, status, website) |
| `client_notes` | Work notes (body, created_by, service_type) |
| `client_service_grades` | A/B/C grades per service |
| `client_service_periods` | Contract history (price, dates, renewal) |
| `client_projects` | Project boards |
| `reports` | Monthly reports (may contain ad performance data) |
