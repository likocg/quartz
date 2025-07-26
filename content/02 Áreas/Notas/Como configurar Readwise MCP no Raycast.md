---
{"publish":true,"created":"2025-07-26T12:44","modified":"2025-07-26T12:44","published":"2025-07-26T13:03:46.439-03:00","tags":["notas","como","mcp","Readwise","raycast"],"cssclasses":""}
---

# Configurando Readwise MCP no Raycast

## Overview
O Readwise MCP é um servidor local que conecta o Raycast ao Readwise via Model Context Protocol, permitindo acessar highlights e anotações diretamente nas conversas com IA.

## Pré-requisitos
- [ ] Node.js instalado
- [ ] Conta no Readwise com token de acesso
- [ ] Raycast com suporte a MCP

## Configuração

### 1. Obter Token do Readwise
- Acesse: https://readwise.io/access_token
- Copie seu token de acesso

### 2. Configurar no Raycast
Vá em Settings > AI > MCP Servers > Add Server:

**Name:** `Readwise MCP`

**Command:** `npx`

**Arguments:** separados por espaço


```
-y @readwise/readwise-mcp
```

**Environment Variables:**
- Key: `ACCESS_TOKEN`
- Value: [seu token do readwise]

**Transport:** Standard Input/Output

### 3. Instalar
Clique em "Install MCP Server"

## Troubleshooting

### "Could not attach to MCP server"
```bash
# Se usar nvm
nvm use 18

# Ou reinstalar Node.js
```

### Erros durante uso
- Tente trocar entre modelos diferentes do Claude
- Verifique se o token está correto
- Reinicie o Raycast

## Uso
Depois de configurado, você pode:
- Buscar highlights por livro/autor
- Filtrar anotações por data/tag
- Referenciar trechos específicos nas conversas

## Links úteis:
https://github.com/readwiseio/readwise-mcp

---

# Teste
