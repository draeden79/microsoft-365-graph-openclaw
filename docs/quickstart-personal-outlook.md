# Quickstart: Personal Outlook/Hotmail

## Target

Use `graph-office-suite` with personal Microsoft accounts (for example Outlook.com/Hotmail) in a self-hosted OpenClaw setup.

## Prerequisites

- Python 3
- OpenClaw gateway with hooks enabled
- Public HTTPS webhook URL for Graph notifications
- `GRAPH_WEBHOOK_CLIENT_STATE` secret

## 1) Authenticate

```bash
python graph-office-suite/scripts/graph_auth.py device-login \
  --client-id 952d1b34-682e-48ce-9c54-bac5a96cbd42 \
  --tenant-id consumers
```

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
  --test-email your.account@outlook.com
```

## 5) Success criteria

- Diagnostic verdict: `READY_PIPELINE`
- Smoke verdict: `READINESS VERDICT: READY_FOR_PUSH`
