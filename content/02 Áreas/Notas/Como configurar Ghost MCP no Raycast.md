---
{"publish":true,"created":"2025-07-26T13:13","modified":"2025-07-26T13:13","published":"2025-07-26T13:17:39.987-03:00","tags":["notas","como","mcp","Ghost","raycast"],"cssclasses":""}
---

# Configurando Ghost MCP no Raycast

## Setup Básico

**Name:** ghost-mcp
**Command:** npx
**Arguments:** -y @fanyangmeng/ghost-mcp (digitar manualmente, não copiar/colar)
**Transport:** Standard Input/Output

## Variáveis de Ambiente

Adicionar via "Add Item":

- GHOST_API_URL: https://seudominio.com (sem barra no final)
- GHOST_ADMIN_API_KEY: chave da admin api do ghost
- GHOST_API_VERSION: v5.0

## Como Pegar a Admin API Key

1. seusite.com/ghost → Settings → Integrations
2. Custom Integrations → Add custom integration
3. Copiar a **Admin API Key** (não a Content API Key)

## Troubleshooting

### Erro "Invalid tag name"
- **Causa:** caracteres invisíveis no campo Arguments
- **Solução:** digitar manualmente -y @fanyangmeng/ghost-mcp

### Alternativas se npx não funcionar

```
Command: npm
Arguments: exec -y @fanyangmeng/ghost-mcp
```

ou

```
Command: npx
Arguments: --yes @fanyangmeng/ghost-mcp
```

## Funcionalidades Disponíveis

- **Posts:** criar, editar, deletar, buscar
- **Membros:** gerenciar assinantes
- **Newsletters:** configurar e enviar
- **Tags:** organizar conteúdo
- **Tiers:** planos de assinatura
- **Ofertas:** promoções
- **Webhooks:** integrações

## Links úteis
https://github.com/MFYDev/ghost-mcp
https://fanyangmeng.blog/from-python-to-typescript-improving-ghost-mcp-server/

---

# Teste

![[03 Arquivos/Attachments/CleanShot 2025-07-26 at 13.16.34@2x.png]]
