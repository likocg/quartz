---
{"publish":true,"created":"2025-07-11T20:39","modified":"2025-07-11T20:40","published":"2025-08-05T10:57:27.970-03:00","tags":["notas","Roam-Research","bluesky","workflow","n8n"],"cssclasses":""}
---

# Workflow n8n ‑ “Bluesky → Roam Daily Note” VERSAO ERRADA SMARTBLOCKS

Esta página ensina a criar, do zero, um fluxo n8n que :

1. consulta periodicamente o feed de uma conta Bluesky;  
2. detecta apenas posts inéditos (sem duplicar);  
3. grava cada post como bloco na *Daily Note* do Roam Research  
   usando o **RoamJS SmartBlocks Server** (API oficial da RoamJS).

> Tempo total ≈ 15 min  
> Pré-requisitos:  
> • instância n8n (self-hosted ou n8n.cloud)  
> • conta Bluesky pública  
> • extensão **RoamJS SmartBlocks** habilitada no seu graph

---

## Índice

1. Ativar o SmartBlocks Server no Roam  
2. Estrutura geral do workflow  
3. Passo-a-passo de cada node  
4. Testes manuais  
5. Personalizações e resolução de erros  
6. Anexo – Código completo dos nodes Function  

---

## 1. Ativar o SmartBlocks Server

1. Abra seu graph → **Settings ▸ RoamJS ▸ SmartBlocks**  
2. Ligue a chave “SmartBlocks Server” → *Enable*  
3. Clique **Generate Token**  
   • copie o valor (ex.: `sb-Jk7z9AqL2pQ…`)  
4. Anote também o **graph name**: é o texto logo após  
   `https://roamresearch.com/#/app/` na barra de endereço  
   (ex.: `mynotes`).

> Guarde token e graph name, serão usados no node final.

---

## 2. Estrutura do workflow

```
┌─ Schedule Trigger ─┬────> dispara a cada X min
│                    │
│ 1 item             ▼
├─ HTTP Request (Bluesky)   → baixa o feed JSON
│                    ▼
├─ Code “Pick Post”        → extrai {postId,text,created}
│                    ▼
├─ Code “Check New”        → compara com último ID
│                    ▼
├─ IF (isNew = true?) ─────┐
│            false ──┐      │
│                    ▼      ▼
└───────>  Code “Save ID”   → HTTP Request (Roam)
                             (adiciona bloco na Daily Note)
```

Total: 7 nodes.

---

## 3. Configuração detalhada

### 3.1 Schedule Trigger

| Campo | Valor | Obs. |
|-------|-------|------|
| Mode  | Every |      |
| Every | 5 minutes (exemplo) | ajuste à vontade |

### 3.2 HTTP Request (Bluesky)

| Campo | Valor |
|-------|-------|
| Method | GET |
| URL | `https://public.api.bsky.app/xrpc/app.bsky.feed.getAuthorFeed?actor=handle.bsky.social&limit=10` |
| Auth  | None |

Troque `handle.bsky.social` pelo @-handle da conta.

### 3.3 Code – “Pick Post”

```js
const newest = items[0].json.feed[0];

return [{
  json: {
    postId:  newest.post.uri,
    text:    newest.post.record.text,
    created: newest.post.record.createdAt,
  },
}];
```

### 3.4 Code – “Check New”

```js
// @ts-ignore  (helper interno do n8n)
const data = $getWorkflowStaticData('global');

const currentId = $json.postId;
const lastId    = data.lastPostId ?? null;
const isNew     = currentId !== lastId;

// se for novo, grava imediatamente
if (isNew) data.lastPostId = currentId;

// Daily Note em formato YYYY-MM-DD
const dnTitle = new Date($json.created).toISOString().slice(0, 10);

return [{
  json: { ...$json, isNew, dnTitle },
}];
```

### 3.5 IF

*Condição*

```
A → {{$json.isNew}}
Operator → is equal to
B → true
```

Ramo **false**: termina.  
Ramo **true**: vai para “Save ID”.

### 3.6 Code – “Save ID” (pode ser puro *pass-through*)

```js
return items;   // id já foi salvo no node anterior
```

### 3.7 HTTP Request – “Roam → DN”

| Campo | Valor |
|-------|-------|
| Method | POST |
| URL | `https://api.roamjs.com/smartblocks/run` |
| Send Headers | ON → `Content-Type: application/json` |
| Send Body | ON → JSON (usar **expressions** nos valores) |

```json
{
  "key": "sb-COLE-SEU-TOKEN",
  "graph": "mynotes",
  "command": "CREATE",
  "variables": {
    "page": "={{ $json.dnTitle }}",
    "block": "={{ '**' + $json.created + '** — ' + $json.text }}"
  }
}
```

Nenhum outro cabeçalho é necessário; a API Key no corpo já autentica.

---

## 4. Testes

1. Clique **Execute workflow** → primeira execução apenas registra o último post.  
2. Crie um post novo no Bluesky.  
3. Execute novamente → abra a *Daily Note* (YYYY-MM-DD) no Roam; o bloco aparece.  
4. Ative o workflow (toggle *Inactive → Active*).

---

## 5. Personalizações & erros

| Sintoma | Causa | Solução |
|---------|-------|---------|
| Repetindo posts | falha em salvar `lastPostId` | verifique se o node “Check New” tem `$getWorkflowStaticData` e se o workflow foi salvo/ativado |
| Nada chega ao Roam | Token errado ou graph name errado | gere token novo em Settings ▸ RoamJS e copie exatamente o nome do graph |
| 400/401 do RoamJS | corpo JSON mal-formado | use “Preview” do node e confira que `key`, `graph` e `variables` estão presentes |

---

## 6. Anexo – dump JSON do workflow (pronto-para-importar)

```json
{
  "nodes": [
    { "parameters": { "triggerTimes": { "item": [{ "mode": "every", "value": 5, "unit": "minutes" }] } }, "id": "Schedule Trigger", "name": "Schedule Trigger", "type": "n8n-nodes-base.scheduleTrigger" },

    { "parameters": { "url": "https://public.api.bsky.app/xrpc/app.bsky.feed.getAuthorFeed?actor=handle.bsky.social&limit=10", "responseFormat": "json" }, "id": "HTTP Request", "name": "HTTP Request", "type": "n8n-nodes-base.httpRequest" },

    { "parameters": { "functionCode": "const newest = items[0].json.feed[0];\nreturn [{json:{postId:newest.post.uri,text:newest.post.record.text,created:newest.post.record.createdAt}}];" }, "id": "Pick Post", "name": "Pick Post", "type": "n8n-nodes-base.function" },

    { "parameters": { "functionCode": "// @ts-ignore\nconst data = $getWorkflowStaticData('global');\nconst currentId=$json.postId;\nconst lastId=data.lastPostId??null;\nconst isNew=currentId!==lastId;\nif(isNew)data.lastPostId=currentId;\nconst dnTitle=new Date($json.created).toISOString().slice(0,10);\nreturn [{json:{...$json,isNew,dnTitle}}];" }, "id": "Check New", "name": "Check New", "type": "n8n-nodes-base.function" },

    { "parameters": { "conditions": { "boolean": [{ "value1": "={{$json.isNew}}", "operation": "equal", "value2": true }] } }, "id": "IF", "name": "IF", "type": "n8n-nodes-base.if" },

    { "parameters": { "functionCode": "return items;" }, "id": "Save ID", "name": "Save ID", "type": "n8n-nodes-base.function" },

    { "parameters": { "url": "https://api.roamjs.com/smartblocks/run", "method": "POST", "jsonParameters": true, "options": {}, "bodyParametersJson": "{\n  \"key\": \"sb-COLE-SEU-TOKEN\",\n  \"graph\": \"mynotes\",\n  \"command\": \"CREATE\",\n  \"variables\": {\n    \"page\": \"={{ $json.dnTitle }}\",\n    \"block\": \"={{ '**' + $json.created + '** — ' + $json.text }}\"\n  }\n}" }, "id": "Roam DN", "name": "Roam DN", "type": "n8n-nodes-base.httpRequest" }
  ],
  "connections": {
    "Schedule Trigger": { "main": [[{ "node": "HTTP Request", "type": "main", "index": 0 }]] },
    "HTTP Request":     { "main": [[{ "node": "Pick Post", "type": "main", "index": 0 }]] },
    "Pick Post":        { "main": [[{ "node": "Check New", "type": "main", "index": 0 }]] },
    "Check New":        { "main": [[{ "node": "IF", "type": "main", "index": 0 }]] },
    "IF": { "main": [
      [{ "node": "Save ID", "type": "main", "index": 0 }],  /* true */
      []                                                    /* false */
    ]},
    "Save ID": { "main": [[{ "node": "Roam DN", "type": "main", "index": 0 }]] }
  }
}
```

Importe em **Settings ▸ Import Workflow**, troque `handle.bsky.social`, token e graph, salve, ative – pronto.