# Documentação do Workflow: Google Calendar Manager API

## Visão Geral

Este workflow do n8n atua como uma **API Gateway** centralizada para gerenciar eventos do Google Calendar. Ele padroniza as operações de criar, ler, atualizar, excluir e listar eventos através de um único endpoint Webhook, garantindo que todas as interações sigam um contrato estrito de dados JSON.

**ID do Workflow:** `aR5cDNe5illdvGFd`
**Nome:** `Google Calendar Manager API`
**Método:** `POST`

---

## Arquitetura do Fluxo

O fluxo opera na seguinte sequência lógica:

1.  **Entrada (Webhook):** Recebe requisições POST.
2.  **Roteamento (Switch):** Analisa o campo `action` do JSON recebido e direciona para a operação correta (`create`, `update`, `delete`, `get`, `list`).
3.  **Processamento (Google Calendar Nodes):** Executa a operação nativa na API do Google Calendar usando nós específicos.
4.  **Padronização de Saída (Code Nodes):** Formata a resposta do Google Calendar para um JSON padronizado (`Format Create`, `Format List`, etc.).
5.  **Resposta (Respond to Webhook):** Retorna o JSON final padronizado para o solicitante com status HTTP 200.

---

## Contrato de Dados (Schemas)

### JSON de Entrada (Request)

Todas as requisições devem ser enviadas para o Webhook com o método `POST` e o seguinte formato:

```json
{
  "action": "create" | "update" | "delete" | "get" | "list",
  "data": {
    // Campos variáveis dependendo da ação
  }
}
```

#### Detalhes do Objeto `data` por Ação:

**1. Create (`action: "create"`)**
```json
"data": {
  "calendarId": "string" (Opcional, default: "primary"),
  "summary": "string" (Título do evento, **Obrigatório**),
  "description": "string" (Opcional),
  "start": "ISO8601 Date" (ex: "2024-02-20T14:00:00Z", **Obrigatório**),
  "end": "ISO8601 Date" (ex: "2024-02-20T15:00:00Z", **Obrigatório**)
}
```

**2. Update (`action: "update"`)**
```json
"data": {
  "calendarId": "string" (Opcional, default: "primary"),
  "eventId": "string" (ID do evento a atualizar, **Obrigatório**),
  "summary": "string" (Novo título, Opcional),
  "description": "string" (Nova descrição, Opcional)
  // Outros campos suportados pela API podem ser adicionados no nó
}
```

**3. Delete (`action: "delete"`)**
```json
"data": {
  "calendarId": "string" (Opcional, default: "primary"),
  "eventId": "string" (ID do evento a excluir, **Obrigatório**)
}
```

**4. Get (`action: "get"`)**
```json
"data": {
  "calendarId": "string" (Opcional, default: "primary"),
  "eventId": "string" (ID do evento a consultar, **Obrigatório**)
}
```

**5. List (`action: "list"`)**
```json
"data": {
  "calendarId": "string" (Opcional, default: "primary")
  // O fluxo está configurado para retornar os últimos 20 eventos por padrão
}
```

---

### JSON de Saída (Response)

O sistema garante uma resposta consistente para sucesso ou falha controlada:

```json
{
  "success": boolean,       // true para operações bem-sucedidas
  "action": "string",       // A ação que foi executada (ex: "create")
  "timestamp": "string",    // Data/hora da resposta (ISO8601)
  "data": object | array,   // O payload retornado pelo Google (ou { deleted: true })
  "message": "string"       // Mensagem legível para humanos
}
```

---

## Exemplos Práticos (cURL)

### Criar um Agendamento
```bash
curl -X POST <SEU_WEBHOOK_URL> \
-H "Content-Type: application/json" \
-d '{
  "action": "create",
  "data": {
    "summary": "Reunião de Alinhamento",
    "start": "2024-03-01T10:00:00Z",
    "end": "2024-03-01T11:00:00Z"
  }
}'
```

### Listar Agendamentos
```bash
curl -X POST <SEU_WEBHOOK_URL> \
-H "Content-Type: application/json" \
-d '{
  "action": "list",
  "data": {}
}'
```

---

## Configuração Necessária

Para que este workflow funcione em seu ambiente n8n, siga estes passos:

1.  **Credenciais do Google Calendar:**
    *   No editor do n8n, abra cada um dos 5 nós relacionados ao Google (nodes laranjas).
    *   Em "Credential to connect with", selecione ou crie uma nova credencial para a API do Google Calendar (OAuth2 ou Service Account).

2.  **Ativação:**
    *   Altere o status do workflow para **Active** no canto superior direito do editor.

3.  **URL de Produção:**
    *   Use a URL de "Production" do nó Webhook, não a de "Test", para integrações reais.
