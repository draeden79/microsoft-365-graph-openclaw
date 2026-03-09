# Guia mínimo: webhook de mail em 6 passos

Este guia usa o **mínimo de parâmetros** e assume que instalaste o skill via ClawHub (ou estás na raiz do repositório). O comando de setup gera o `clientState` automaticamente e cria a subscrição; não precisas de rodar o adapter para copiar valores nem de passar `--session-key`.

## Pré-requisitos

- DNS: o teu domínio (ex.: `graphhook.alitar.one`) aponta para o IP público da EC2 (ou da máquina onde corre o adapter).
- Portas **80** e **443** abertas no firewall / security group.
- OpenClaw a correr localmente (ex.: em `127.0.0.1:18789`).
- **Hook token** configurado no OpenClaw: ver [Configurar hooks do OpenClaw](setup-openclaw-hooks.md).

## Passos

### 1. Preparar

```bash
cd /caminho/para/skill   # pasta do skill (ClawHub: .../skills/microsoft-365-graph-openclaw)
export REPO_ROOT="$(pwd)"
```

### 2. Autenticar

Conta **pessoal** (Outlook, Hotmail):

```bash
python3 scripts/graph_auth.py device-login \
  --client-id 952d1b34-682e-48ce-9c54-bac5a96cbd42 \
  --tenant-id consumers
```

Conta **work/school**:

```bash
python3 scripts/graph_auth.py device-login \
  --client-id 952d1b34-682e-48ce-9c54-bac5a96cbd42 \
  --tenant-id organizations
```

Abrir o URL indicado, inserir o código e autorizar.

### 3. Hook token no OpenClaw

Garantir que em `openclaw.json` (ex.: `~/.openclaw/openclaw.json`) o bloco `hooks` tem `enabled: true` e `token` com o valor que escolheste. Ver [setup-openclaw-hooks.md](setup-openclaw-hooks.md). Reiniciar o OpenClaw após alterar.

### 4. Um comando de setup

O script instala Caddy, systemd (adapter, worker, timer), cria a subscrição Graph e grava o ID no env. Não é preciso passar `--client-state` nem `--session-key` (têm valores por defeito ou são gerados).

```bash
sudo bash scripts/run_mail_webhook_e2e_setup.sh \
  --domain graphhook.alitar.one \
  --hook-token "<mesmo-valor-de-hooks.token>" \
  --test-email "teu-email@exemplo.com" \
  --repo-root "$REPO_ROOT"
```

Substituir `graphhook.alitar.one` pelo teu domínio e `"<mesmo-valor-de-hooks.token>"` pelo token configurado no passo 3.

### 5. Diagnóstico

```bash
bash scripts/diagnose_mail_webhook_e2e.sh \
  --domain graphhook.alitar.one \
  --repo-root "$REPO_ROOT"
```

Corrigir qualquer FAIL antes de seguir.

### 6. Smoke test

```bash
bash scripts/run_mail_webhook_smoke_tests.sh \
  --domain graphhook.alitar.one \
  --create-subscription \
  --test-email "teu-email@exemplo.com"
```

Verificar que o verdict é `READY_FOR_PUSH`.

## Resumo

| Passo | O que fazes |
|-------|-------------|
| 1 | `cd` para o skill, `export REPO_ROOT` |
| 2 | Device-login (app Alitar, consumers ou organizations) |
| 3 | Hook token em `openclaw.json` + reiniciar OpenClaw |
| 4 | Um comando: `run_mail_webhook_e2e_setup.sh` com `--domain`, `--hook-token`, `--test-email` |
| 5 | Diagnóstico |
| 6 | Smoke test |

Não é preciso criar App Registration nem gerar `clientState` manualmente; o setup gera e persiste o que for necessário.
