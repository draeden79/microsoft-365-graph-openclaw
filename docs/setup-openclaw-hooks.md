# Configurar hooks do OpenClaw (hook token)

Para o webhook de mail funcionar, o OpenClaw precisa de um **token de hooks** configurado. O mesmo valor é usado no ficheiro de configuração do OpenClaw e no setup do webhook (`--hook-token`).

## 1. Como criar o token

O valor é um segredo que **tu escolhes** (não é gerado pelo OpenClaw nem por este repositório).

Recomendações:

- **Linux/macOS:** `openssl rand -hex 16` ou `uuidgen`
- Ou usar um gestor de palavras-passe para gerar um token longo
- Não usar palavras óbvias nem reutilizar palavras-passe

O **mesmo** valor será usado em dois sítios (ver abaixo); têm de ser iguais.

## 2. Onde colocar no OpenClaw

- **Ficheiro:** normalmente `~/.openclaw/openclaw.json` (ou o config que o OpenClaw usa na tua instalação).
- **Bloco `hooks`:** garantir que existe, que `enabled` é `true` e que `token` tem o valor gerado (não deixar o literal `<OPENCLAW_HOOK_TOKEN>`).

Exemplo de bloco completo:

```json
"hooks": {
  "enabled": true,
  "token": "<valor-gerado-aqui-ex.-e10c9f57-4f21-4665-907e-5380241c92b9>",
  "defaultSessionKey": "hook:ingress",
  "allowRequestSessionKey": false,
  "allowedSessionKeyPrefixes": ["hook:"]
}
```

Substituir apenas o valor de `"token"` pelo segredo que criaste. O resto pode manter-se como no exemplo.

**Após alterar o ficheiro,** reiniciar o OpenClaw para carregar a nova configuração.

## 3. Onde usar no setup do webhook

- Ao correr `setup_mail_webhook_ec2.sh` ou `run_mail_webhook_e2e_setup.sh`, passar esse **mesmo** valor em `--hook-token "<valor>"`.
- Esse valor fica gravado em `/etc/default/graph-mail-webhook` como `OPENCLAW_HOOK_TOKEN`; o worker usa-o em cada POST para o hook.

## 4. Segurança

- Não commitar o token no repositório nem colar em logs.
- Rotacionar se tiver sido exposto: alterar em `openclaw.json` e em `/etc/default/graph-mail-webhook`, reiniciar OpenClaw e o worker.
