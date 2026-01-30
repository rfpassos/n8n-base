# AGENTS.md

Este arquivo fornece orientação ao Antigravity (Gemini) ao trabalhar com código neste repositório.

## Visão Geral do Projeto

Este é o repositório **n8n-base** - um projeto configurado para criação de workflows n8n de alta qualidade usando o Antigravity com o servidor MCP n8n-mcp.

**Repositório n8n-mcp**: https://github.com/czlonkowski/n8n-mcp
**Repositório n8n-skills**: https://github.com/czlonkowski/n8n-skills

**Objetivo**: Criar workflows n8n usando ferramentas MCP e 7 skills especializadas.

**Arquitetura**:
- **n8n-mcp MCP Server**: Fornece acesso a dados (1084+ nodes, validação, templates, gerenciamento de workflows)
- **Skills**: Fornece orientação especializada sobre COMO usar as ferramentas MCP
- **Juntos**: Construtor de workflows especializado com divulgação progressiva

---

## As 7 Skills Instaladas

Skills disponíveis em `.agent/skills/`:

### 1. n8n Expression Syntax
- Ensina sintaxe correta de expressões n8n (padrões {{}})
- Cobre erros comuns e correções
- **Gotcha crítico**: Dados de webhook sob `$json.body`

### 2. n8n MCP Tools Expert (PRIORIDADE MÁXIMA)
- Ensina como usar ferramentas n8n-mcp MCP efetivamente
- Cobre ferramentas unificadas: `get_node`, `validate_node`, `search_nodes`
- Gerenciamento de workflows com `n8n_update_partial_workflow`
- Novo: `n8n_deploy_template`, `n8n_workflow_versions`, `activateWorkflow`

### 3. n8n Workflow Patterns
- Ensina padrões arquiteturais comprovados de workflows
- 5 padrões: webhook, HTTP API, database, AI, scheduled

### 4. n8n Validation Expert
- Interpreta erros de validação e guia correções
- Lida com falsos positivos e loops de validação
- Auto-correção com `n8n_autofix_workflow`

### 5. n8n Node Configuration
- Orientação de configuração de nodes ciente de operações
- Dependências de propriedades e padrões comuns

### 6. n8n Code JavaScript
- Escrever JavaScript em nodes Code do n8n
- Padrões de acesso a dados, `$helpers`, DateTime

### 7. n8n Code Python
- Escrever Python em nodes Code do n8n
- Conscientização de limitações (sem bibliotecas externas)

---

## Ferramentas MCP Principais

O servidor n8n-mcp fornece estas ferramentas unificadas:

### Descoberta de Nodes
- `search_nodes` - Encontrar nodes por palavra-chave
- `get_node` - Info unificada com níveis de detalhe (minimal, standard, full) e modos (info, docs, search_properties, versions)

### Validação
- `validate_node` - Validação unificada com modos (minimal, full) e perfis (runtime, ai-friendly, strict)
- `validate_workflow` - Validação completa de workflow

### Gerenciamento de Workflows
- `n8n_create_workflow` - Criar novos workflows
- `n8n_update_partial_workflow` - Atualizações incrementais (17 tipos de operação incluindo `activateWorkflow`)
- `n8n_validate_workflow` - Validar por ID
- `n8n_autofix_workflow` - Auto-corrigir problemas comuns
- `n8n_deploy_template` - Deploy de template para instância n8n
- `n8n_workflow_versions` - Histórico de versões e rollback
- `n8n_test_workflow` - Execução de teste
- `n8n_executions` - Gerenciar execuções

### Templates
- `search_templates` - Múltiplos modos (keyword, by_nodes, by_task, by_metadata)
- `get_template` - Obter detalhes do template

### Guias
- `tools_documentation` - Meta-documentação para todas as ferramentas
- `ai_agents_guide` - Orientação de workflow de agentes AI

---

## Padrões Importantes

### Padrão de Uso Mais Comum
```
search_nodes → get_node (18s avg entre passos)
```

### Padrão de Validação Mais Comum
```
n8n_update_partial_workflow → n8n_validate_workflow
Avg 23s pensando, 58s corrigindo
```

### Ferramenta Mais Usada
```
n8n_update_partial_workflow (99.0% sucesso)
Avg 56 segundos entre edições
```

---

## Ativação de Skills

Skills são ativadas automaticamente quando queries correspondem aos seus gatilhos de descrição:
- "Como escrevo expressões n8n?" → n8n Expression Syntax
- "Encontre um node de Slack" → n8n MCP Tools Expert
- "Construa um workflow de webhook" → n8n Workflow Patterns

---

## Integração Entre Skills

Skills são projetadas para trabalhar juntas:
1. Use n8n Workflow Patterns para identificar estrutura
2. Use n8n MCP Tools Expert para encontrar nodes
3. Use n8n Node Configuration para configuração
4. Use n8n Expression Syntax para mapeamento de dados
5. Use n8n Code JavaScript/Python para lógica customizada
6. Use n8n Validation Expert para validar

---

## Requisitos

- Servidor MCP n8n-mcp instalado e configurado
- Antigravity com Gemini
- Entendimento de conceitos de workflows n8n

---

## Créditos

Skills concebidas por **Romuald Członkowski** - [www.aiadvisors.pl/en](https://www.aiadvisors.pl/en)

Parte do projeto n8n-mcp.

## Licença

MIT License - Veja o arquivo LICENSE para detalhes.
