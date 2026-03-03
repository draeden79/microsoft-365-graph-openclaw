# Auth Reference

## Recommended authentication profiles

### Personal Microsoft account (`@outlook.com`, `@hotmail.com`, Microsoft 365 Family)

- **Skill default Client ID**: `9e5f94bc-e8a4-4e73-b8be-63364c29d753`
- **Skill default tenant**: `consumers`
- **Use when**: you authenticate with Microsoft personal accounts (MSA), without corporate Entra ID.

### Work/school account (Microsoft Entra ID / Azure AD)

- **Recommended tenant**: `organizations` (or your tenant GUID)
- **Client ID**: your organization app registration (or another tenant-approved app)
- **Use when**: work/school accounts are controlled by tenant policy.
- **Suggested scopes**:
  - `Mail.ReadWrite`
  - `Mail.Send`
  - `Calendars.ReadWrite`
  - `Files.ReadWrite.All`
  - `Contacts.ReadWrite` *(remove if contacts are not needed)*
  - `offline_access` (required for refresh token)

## Assisted device-code flow

1. Run (personal-account profile): `python graph-office-suite/scripts/graph_auth.py device-login --client-id 9e5f94bc-e8a4-4e73-b8be-63364c29d753 --tenant-id consumers --scopes Mail.ReadWrite Mail.Send Calendars.ReadWrite Files.ReadWrite.All Contacts.ReadWrite offline_access`
2. The script prints **URL** and **code**.
3. Open `https://microsoft.com/devicelogin`, paste the code, and authorize.
4. On success, the script saves `state/graph_auth.json` with `access_token`, `refresh_token`, expiration, and scopes.
5. Tokens auto-refresh before requests. To force refresh: `python graph-office-suite/scripts/graph_auth.py refresh`.

## `state/graph_auth.json` structure

```json
{
  "client_id": "9e5f94bc-e8a4-4e73-b8be-63364c29d753",
  "tenant_id": "consumers",
  "scopes": ["Mail.ReadWrite", "Mail.Send", ...],
  "token": {
    "access_token": "...",
    "refresh_token": "...",
    "expires_at": 1234567890
  }
}
```

Never commit this file (`.gitignore`).

## Common errors

| Symptom | Fix |
| --- | --- |
| `authorization_pending` | Wait, user has not completed authorization yet. |
| `interaction_required` | Re-run device login (token invalid/consent removed). |
| `AADSTS50059` on `/devicecode` | Tenant is incompatible with account type. For personal accounts use `--tenant-id consumers`. |
| `AADSTS700016` on `consumers` | `client_id` is not valid for Microsoft personal accounts. Use an MSA-compatible app registration. |
| `AADSTS70011 invalid_scope` with `OnlineMeetings.*` on `consumers` | `OnlineMeetings.*` scopes are not supported in this workflow for personal accounts. Remove these scopes and keep default scopes. |
| Repeated 401 errors | Run `graph_auth.py refresh`; if still failing, run `clear` and re-authenticate. |
| 403 (Access Denied) | Add the required scope and grant consent again. |
