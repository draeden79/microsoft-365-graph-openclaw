# Quickstart: Microsoft 365 Work/School

Para o **fluxo mínimo em 6 passos**, ver [Guia mínimo](guia-minimo.md).

## Target

Use this skill with Entra ID work/school tenants in a self-hosted OpenClaw setup.

## Prerequisites

- Python 3
- OpenClaw gateway with hooks enabled and [hook token configured](setup-openclaw-hooks.md)
- Public HTTPS webhook URL for Graph notifications
- In the minimal flow, `GRAPH_WEBHOOK_CLIENT_STATE` is auto-generated. For production you may use your own App Registration (see [Criar App Registration](app-registration.md)).

## 1) Authenticate (tenant-aware)

Default: use the Alitar app (no portal setup required):

```bash
python3 scripts/graph_auth.py device-login \
  --client-id 952d1b34-682e-48ce-9c54-bac5a96cbd42 \
  --tenant-id organizations
```

Or use your own App Registration: `--client-id "<YOUR_APP_CLIENT_ID>"`. You can also use your tenant GUID instead of `organizations`.

## 2) Configure webhook stack

```bash
sudo bash scripts/setup_mail_webhook_ec2.sh \
  --domain graphhook.example.com \
  --hook-url http://127.0.0.1:18789/hooks/wake \
  --hook-token "<OPENCLAW_HOOK_TOKEN>" \
  --session-key "hook:graph-mail" \
  --client-state "<GRAPH_WEBHOOK_CLIENT_STATE>" \
  --repo-root "$(pwd)"
```

## 3) Create subscription

```bash
python3 scripts/mail_subscriptions.py create \
  --notification-url "https://graphhook.example.com/graph/mail" \
  --client-state "$GRAPH_WEBHOOK_CLIENT_STATE" \
  --minutes 4200
```

Default resource is `me/messages` (recommended).

## 4) Validate

```bash
bash scripts/diagnose_mail_webhook_e2e.sh \
  --domain graphhook.example.com \
  --lookback-minutes 30
```

```bash
bash scripts/run_mail_webhook_smoke_tests.sh \
  --domain graphhook.example.com \
  --create-subscription \
  --test-email your.user@yourtenant.com
```

## 5) Success criteria

- Diagnostic verdict: `READY_PIPELINE`
- Smoke verdict: `READINESS VERDICT: READY_FOR_PUSH`
