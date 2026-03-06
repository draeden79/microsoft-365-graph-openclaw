# Quickstart: Microsoft 365 Work/School

## Target

Use `graph-office-suite` with Entra ID work/school tenants in a self-hosted OpenClaw setup.

## Prerequisites

- Python 3
- Tenant-approved App Registration (recommended for production)
- OpenClaw gateway with hooks enabled
- Public HTTPS webhook URL for Graph notifications
- `GRAPH_WEBHOOK_CLIENT_STATE` secret

## 1) Authenticate (tenant-aware)

```bash
python graph-office-suite/scripts/graph_auth.py device-login \
  --client-id "<YOUR_APP_CLIENT_ID>" \
  --tenant-id organizations
```

You can also use your tenant GUID instead of `organizations`.

## 2) Configure webhook stack

```bash
sudo bash graph-office-suite/scripts/setup_mail_webhook_ec2.sh \
  --domain graphhook.example.com \
  --hook-url http://127.0.0.1:18789/hooks/wake \
  --hook-token "<OPENCLAW_HOOK_TOKEN>" \
  --session-key "hook:graph-mail" \
  --client-state "<GRAPH_WEBHOOK_CLIENT_STATE>" \
  --repo-root "$(pwd)"
```

## 3) Create subscription

```bash
python3 graph-office-suite/scripts/mail_subscriptions.py create \
  --notification-url "https://graphhook.example.com/graph/mail" \
  --client-state "$GRAPH_WEBHOOK_CLIENT_STATE" \
  --minutes 4200
```

Default resource is `me/messages` (recommended).

## 4) Validate

```bash
bash graph-office-suite/scripts/diagnose_mail_webhook_e2e.sh \
  --domain graphhook.example.com \
  --lookback-minutes 30
```

```bash
bash graph-office-suite/scripts/run_mail_webhook_smoke_tests.sh \
  --domain graphhook.example.com \
  --create-subscription \
  --test-email your.user@yourtenant.com
```

## 5) Success criteria

- Diagnostic verdict: `READY_PIPELINE`
- Smoke verdict: `READINESS VERDICT: READY_FOR_PUSH`
