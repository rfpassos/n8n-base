# Antigravity - n8n Workflow Builder

Este arquivo fornece instruções especializadas para a criação de workflows de alta qualidade no n8n utilizando o Antigravity com os recursos do MCP Server e Skills do n8n.

## 🔧 Ferramentas Disponíveis

Após a configuração do n8n-MCP, estarão disponíveis as seguintes ferramentas:

### Core Tools (7 ferramentas)
| Ferramenta | Descrição |
|------------|-----------|
| `tools_documentation` | Documentação de boas práticas para MCP tools |
| `search_nodes` | Busca full-text em todos os nodes (1084+ disponíveis) |
| `get_node` | Informações detalhadas sobre um node específico |
| `validate_node` | Validação de configuração de nodes |
| `validate_workflow` | Validação completa de workflows |
| `search_templates` | Busca em 2709+ templates de workflows |
| `get_template` | Obtém JSON completo de um template |

### Ferramentas de Gerenciamento n8n (13 ferramentas)
| Ferramenta | Descrição |
|------------|-----------|
| `n8n_create_workflow` | Cria novos workflows |
| `n8n_get_workflow` | Recupera workflows existentes |
| `n8n_update_full_workflow` | Atualiza workflow inteiro |
| `n8n_update_partial_workflow` | Atualiza parcialmente via operações diff |
| `n8n_delete_workflow` | Deleta workflows |
| `n8n_list_workflows` | Lista workflows com filtros |
| `n8n_validate_workflow` | Valida workflow por ID |
| `n8n_autofix_workflow` | Corrige erros comuns automaticamente |
| `n8n_workflow_versions` | Gerencia histórico e rollback |
| `n8n_deploy_template` | Deploy de templates do n8n.io |
| `n8n_test_workflow` | Testa/dispara execução de workflow |
| `n8n_executions` | Gerencia execuções |
| `n8n_health_check` | Verifica conectividade da API |

---

## 🎯 Princípios de Desenvolvimento

### 1. Execução Silenciosa
Execute as ferramentas sem comentários intermediários. Responda APENAS após todas as ferramentas completarem.

### 2. Execução Paralela
Quando operações são independentes, execute-as em paralelo para máxima performance.

### 3. Templates Primeiro
SEMPRE verifique templates antes de construir do zero (2709+ disponíveis).

### 4. Validação Multi-nível
Use o padrão: `validate_node(mode='minimal')` → `validate_node(mode='full')` → `validate_workflow`

### 5. Nunca Confie em Defaults
⚠️ **CRÍTICO**: Valores padrão de parâmetros são a fonte #1 de falhas em runtime.
SEMPRE configure TODOS os parâmetros que controlam o comportamento do node explicitamente.

---

## 📋 Processo de Desenvolvimento de Workflows

### Fase 1: Descoberta de Templates
```javascript
// Busca por metadados
search_templates({ searchMode: 'by_metadata', complexity: 'simple' })

// Busca por tarefa
search_templates({ searchMode: 'by_task', task: 'webhook_processing' })

// Busca por texto
search_templates({ query: 'slack notification' })

// Busca por tipo de node
search_templates({ searchMode: 'by_nodes', nodeTypes: ['n8n-nodes-base.slack'] })
```

**Estratégias de filtro:**
- Iniciantes: `complexity: "simple"` + `maxSetupMinutes: 30`
- Por função: `targetAudience: "marketers"` | `"developers"` | `"analysts"`
- Por tempo: `maxSetupMinutes: 15` para quick wins
- Por serviço: `requiredService: "openai"` para compatibilidade

### Fase 2: Descoberta de Nodes
```javascript
// Busca com exemplos
search_nodes({ query: 'keyword', includeExamples: true })

// Busca por triggers
search_nodes({ query: 'trigger' })

// Nodes com IA
search_nodes({ query: 'AI agent langchain' })
```

### Fase 3: Configuração
```javascript
// Detalhes padrão com exemplos
get_node({ nodeType, detail: 'standard', includeExamples: true })

// Apenas metadados básicos (~200 tokens)
get_node({ nodeType, detail: 'minimal' })

// Informação completa (~3000-8000 tokens)
get_node({ nodeType, detail: 'full' })

// Busca propriedades específicas
get_node({ nodeType, mode: 'search_properties', propertyQuery: 'auth' })

// Documentação em markdown
get_node({ nodeType, mode: 'docs' })
```

### Fase 4: Validação
```javascript
// Validação rápida (campos obrigatórios)
validate_node({ nodeType, config, mode: 'minimal' })

// Validação completa com correções
validate_node({ nodeType, config, mode: 'full', profile: 'runtime' })

// Validação de workflow completo
validate_workflow(workflow)
```

### Fase 5: Construção
- Use templates quando disponíveis: `get_template(templateId, { mode: "full" })`
- **Atribuição obrigatória**: "Baseado no template de **[author.name]** (@[username]). Ver em: [url]"
- Configure TODOS os parâmetros explicitamente
- Conecte nodes corretamente
- Adicione tratamento de erros
- Use expressões n8n: `$json`, `$node["NodeName"].json`

### Fase 6: Deploy
```javascript
// Criar workflow
n8n_create_workflow(workflow)

// Validar após deploy
n8n_validate_workflow({ id })

// Atualizações em lote
n8n_update_partial_workflow({ id, operations: [...] })

// Testar webhooks
n8n_test_workflow({ id })
```

---

## ⚠️ Padrões Críticos

### Sintaxe addConnection

✅ **CORRETO** - Quatro parâmetros separados:
```json
{
  "type": "addConnection",
  "source": "node-id-string",
  "target": "target-node-id-string",
  "sourcePort": "main",
  "targetPort": "main"
}
```

### Roteamento Multi-output do IF Node

IF nodes têm **duas saídas** (TRUE e FALSE). Use o parâmetro `branch`:

```json
// Rota para branch TRUE
{
  "type": "addConnection",
  "source": "if-node-id",
  "target": "success-handler-id",
  "sourcePort": "main",
  "targetPort": "main",
  "branch": "true"
}

// Rota para branch FALSE
{
  "type": "addConnection",
  "source": "if-node-id",
  "target": "failure-handler-id",
  "sourcePort": "main",
  "targetPort": "main",
  "branch": "false"
}
```

### Operações em Lote

✅ **CORRETO** - Múltiplas operações em uma chamada:
```json
n8n_update_partial_workflow({
  id: "wf-123",
  operations: [
    { type: "updateNode", nodeId: "slack-1", changes: {...} },
    { type: "updateNode", nodeId: "http-1", changes: {...} },
    { type: "cleanStaleConnections" }
  ]
})
```

---

## 🎓 As 7 Skills do n8n (✅ Instaladas)

Skills instaladas em `.agent/skills/` e prontas para uso:

| Skill | Descrição | Ativa Quando |
|-------|-----------|--------------|
| **n8n-expression-syntax** | Sintaxe de expressões ($json, $node, $now, $env) | Escrevendo expressões, usando {{}}, erros de expressão |
| **n8n-mcp-tools-expert** | Guia de uso das ferramentas MCP | Buscando nodes, validando, gerenciando workflows |
| **n8n-workflow-patterns** | 5 padrões arquiteturais comprovados | Criando workflows, escolhendo padrões |
| **n8n-validation-expert** | Interpretação e correção de erros | Erros de validação, falsos positivos |
| **n8n-node-configuration** | Configuração de nodes e dependências | Configurando nodes, entendendo propriedades |
| **n8n-code-javascript** | JavaScript efetivo em Code nodes | Escrevendo JS em n8n, usando $helpers |
| **n8n-code-python** | Python em Code nodes | Escrevendo Python em n8n |

> **Nota**: As skills são ativadas automaticamente com base no contexto da conversa.

---

## 📚 Recursos

- [n8n-MCP GitHub](https://github.com/czlonkowski/n8n-mcp)
- [n8n-Skills GitHub](https://github.com/czlonkowski/n8n-skills)
- [Configuração Antigravity](https://github.com/czlonkowski/n8n-mcp/blob/main/docs/ANTIGRAVITY_SETUP.md)

---

## 🔐 Configuração do MCP Server (✅ Configurado)

### Status
- ✅ n8n-mcp instalado globalmente
- ✅ Configurado em `mcp_config.json`
- ⚠️ **Pendente**: Configurar `N8N_API_KEY`

### Arquivo de Configuração
Localizado em: `C:\Users\rfpas\.gemini\antigravity\mcp_config.json`

```json
{
  "mcpServers": {
    "n8n-mcp": {
      "command": "node",
      "args": [
        "C:\\Users\\rfpas\\AppData\\Roaming\\npm\\node_modules\\n8n-mcp\\dist\\mcp\\index.js"
      ],
      "env": {
        "MCP_MODE": "stdio",
        "LOG_LEVEL": "error",
        "DISABLE_CONSOLE_OUTPUT": "true",
        "N8N_API_URL": "http://localhost:5678",
        "N8N_BASE_URL": "http://localhost:5678",
        "N8N_API_KEY": ""
      }
    }
  }
}
```

> **Próximo passo**: Configure `N8N_API_KEY` com sua chave de API do n8n e reinicie o Antigravity.

---

## 📁 Estrutura do Projeto

```
n8n-base/
├── AGENTS.md              # Instruções gerais para Antigravity
├── Antigravity.md         # Este arquivo - guia de referência
├── .agent/
│   └── skills/            # Skills do n8n (7 instaladas)
│       ├── n8n-expression-syntax/
│       ├── n8n-mcp-tools-expert/
│       ├── n8n-workflow-patterns/
│       ├── n8n-validation-expert/
│       ├── n8n-node-configuration/
│       ├── n8n-code-javascript/
│       └── n8n-code-python/
└── n8n-skills/            # Repositório original (pode ser removido)
```

---

## 🙏 Créditos

Skills concebidas por **Romuald Członkowski** - [www.aiadvisors.pl/en](https://www.aiadvisors.pl/en)

Parte do projeto [n8n-mcp](https://github.com/czlonkowski/n8n-mcp).
