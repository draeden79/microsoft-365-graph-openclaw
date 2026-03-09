# Criar o seu próprio App Registration (Microsoft Entra ID)

Para **começar rapidamente** podes usar o app da Alitar (ver [references/auth.md](../references/auth.md)); não é preciso criar nada no portal. Esta página descreve como criar o **teu próprio** App Registration quando quiseres controlo total (produção, políticas do tenant, governação).

## Quando faz sentido

- Produção com requisitos de conformidade ou políticas do tenant
- A organização exige uso de um app registado no tenant
- Queres gerir scopes e consentimento de forma isolada

## Passo a passo

### 1. Onde entrar

- **Portal Azure:** [https://portal.azure.com](https://portal.azure.com) → Azure Active Directory (ou Microsoft Entra ID)
- Ou **Microsoft Entra admin center:** [https://entra.microsoft.com](https://entra.microsoft.com)

Navegação: **Azure Active Directory** (ou **Entra ID**) → **App registrations** → **New registration**.

### 2. Criar o registo

- **Name:** por exemplo "OpenClaw Graph Mail".
- **Supported account types:** conforme o teu cenário (single tenant, multitenant, ou contas pessoais).
- **Redirect URI:** para device code flow, não é obrigatório configurar redirect; podes deixar em branco ou escolher "Public client / native".
- Clicar **Register**.

### 3. Copiar o Application (client) ID

Na página **Overview** do app, copia o **Application (client) ID**. Esse valor é o teu `--client-id` nos comandos de autenticação.

### 4. Configurar suporte a device code (public client)

- **Authentication** → **Advanced settings** → **Allow public client flows** → **Yes** (para device code flow).

### 5. Adicionar permissões de API

- **API permissions** → **Add a permission** → **Microsoft Graph** → **Delegated permissions**.
- Adicionar pelo menos:
  - `Mail.ReadWrite`
  - `Mail.Send` (opcional)
  - `offline_access` (obrigatório para refresh token)
- Para calendário/Drive/Contacts, conforme o skill: `Calendars.ReadWrite`, `Files.ReadWrite.All`, `Contacts.ReadWrite`.

### 6. Consentimento

- **User consent:** cada utilizador pode conceder ao fazer device login.
- **Admin consent:** em alguns tenants é obrigatório; um administrador concede em **API permissions** → **Grant admin consent for <tenant>**.

### 7. Usar nos comandos

- Conta pessoal (MSA): normalmente o teu app precisa de estar configurado para "Personal Microsoft accounts" no registo; usa `--tenant-id consumers`.
- Work/school: `--client-id <teu-application-id>` e `--tenant-id organizations` (ou o GUID do tenant).

Exemplo:

```bash
python3 scripts/graph_auth.py device-login \
  --client-id "<TEU_APPLICATION_CLIENT_ID>" \
  --tenant-id organizations
```

## Referências

- [Auth reference](../references/auth.md) — perfis recomendados e erros comuns
- [Microsoft identity platform / device code flow](https://learn.microsoft.com/en-us/azure/active-directory/develop/v2-oauth2-device-code)
