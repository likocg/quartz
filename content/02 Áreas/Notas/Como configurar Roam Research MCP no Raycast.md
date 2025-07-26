---
{"publish":true,"created":"2025-07-26T13:27","modified":"2025-07-26T13:27","published":"2025-07-26T13:28:51.947-03:00","tags":["notas","como","roamresearch","mcp","raycast"],"cssclasses":""}
---

# Configurando Roam Research MCP no Raycast

## Setup Básico

**Name:** roam-research
**Command:** npx
**Arguments:** -y roam-research-mcp (digitar manualmente)
**Transport:** Standard Input/Output

## Variáveis de Ambiente

Adicionar via "Add Item":

- ROAM_API_TOKEN: token da api do roam research
- ROAM_GRAPH_NAME: nome do seu graph
- MEMORIES_TAG: #[[LLM/Memories]] (opcional, para função de memória)

## Como Pegar o Token da API

1. Abrir seu Roam Research
2. Settings → Graph tab → API Tokens
3. Clicar em "+ New API Token"
4. Copiar o token gerado

## Nome do Graph

É o nome que aparece na URL do seu Roam:
- URL: https://roamresearch.com/#/app/meu-graph
- ROAM_GRAPH_NAME: meu-graph

## Funcionalidades Principais

### Gerenciamento de Conteúdo
- **roam_fetch_page_by_title:** buscar páginas por título
- **roam_create_page:** criar novas páginas
- **roam_import_markdown:** importar markdown estruturado
- **roam_create_outline:** criar outlines hierárquicos

### Busca Avançada
- **roam_search_by_text:** buscar blocos por texto
- **roam_search_for_tag:** buscar por tags específicas
- **roam_search_by_status:** buscar TODOs/DONE
- **roam_search_by_date:** buscar por datas
- **roam_search_hierarchy:** buscar hierarquia de blocos

### Funcionalidades Especiais
- **roam_add_todo:** adicionar todos na daily page
- **roam_remember/recall:** sistema de memória persistente
- **roam_datomic_query:** queries avançadas no graph
- **roam_process_batch_actions:** operações complexas em lote

## Teste Rápido

Após configurar, testar com: "me mostra as páginas modificadas hoje"

## Dicas de Uso

### Melhores Práticas
- Sempre usar UIDs quando possível para precisão
- Ler o Roam Markdown Cheatsheet antes de operações complexas
- Para estruturas complexas (tabelas), usar roam_process_batch_actions
- Texto é case-sensitive no Roam

### Exemplo de Prompts
- "Cria uma página 'Projeto X' com outline estruturado"
- "Marca 'revisar relatório' como done e adiciona novo todo"
- "Busca todos os blocos com tag #importante modificados essa semana"

## Status do Projeto

**Atenção:** Projeto marcado como "Work in Progress" - pode ter bugs ocasionais.

## Troubleshooting

### Erro de Autenticação
- Verificar se ROAM_API_TOKEN está correto
- Confirmar se o token tem permissões adequadas

### Graph não encontrado
- Verificar se ROAM_GRAPH_NAME está exato (case-sensitive)
- Confirmar acesso ao graph especificado

---
# Teste




