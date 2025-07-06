---
{"publish":true,"title":"Checklist: Setup Quartz 4 para Digital Garden com Obsidian","created":"2025-07-05T11:51","modified":"2025-07-06T11:22","tags":["notas"],"cssclasses":""}
---

Com base no [Setup Guide](https://saberzero1.github.io/quartz-syncer-docs/Setup-Guide) do plugin no Obsidian Quartz Syncer.

### pré-requisitos
- [x] conta no github (criar em github.com/signup se não tiver)
- [x] obsidian instalado

### 1. criar repositório quartz
- [x] ir em https://github.com/new?template_name=quartz&template_owner=jackyzha0
- [x] criar repo usando o template do quartz
- [x] anotar o nome do repo e seu username

- alterei o nome do meu antigo repositório para quartz4 e fiz um novo /quartz a partir do 4: https://github.com/likocg/quartz

### 2. configurar quartz.config.ts
- [x] abrir arquivo `quartz.config.ts` no repo
- [x] alterar `pageTitle` pro título do seu site
	- Matagal Dazideia
- [x] alterar `baseUrl` pra seu domínio próprio (sem https://) OU `<seu-username>.github.io/<nome-do-repo>`
	- Segui com teste para o github.io, gostando, migro para o subdomínio no Cosmoliko.com
		- likocg.github.io/quartz
- [x] definir `defaultDateType` ("created", "modified", ou "published")
	- Modified
- [x] configurar `markdownLinkResolution` ("shortest", "relative", ou "absolute")
	- mantive shortest, mas só vou descobrir se esse é o melhor caso depois testando.

-     Troquei o locale para "pt-BR".

Sobre o markdownLinkResolution:

Essa configuração define como o quartz vai interpretar os links entre suas notas quando converter de obsidian pra web.

**o problema:** no obsidian você pode linkar notas de várias formas:
- `[[Minha Nota]]` (só o nome)
- `[[pasta/Minha Nota]]` (com caminho)
- `[[../outra-pasta/Minha Nota]]` (caminho relativo)

**as opções:**

**"shortest"** (mais comum):
- sempre usa o nome mais curto possível
- `[[Minha Nota]]` mesmo que ela esteja em `projetos/trabalho/Minha Nota.md`
- **problema:** se você tiver duas notas com mesmo nome em pastas diferentes, vai dar conflito

**"relative"**:
- usa caminho relativo da nota atual
- se você tá em `projetos/index.md` e quer linkar `projetos/trabalho/nota.md`, fica `[[trabalho/nota]]`
- mais seguro, mas links ficam mais longos

**"absolute"**:
- sempre usa o caminho completo desde a raiz
- `[[projetos/trabalho/Minha Nota]]`
- mais verboso, mas nunca dá conflito

**na prática:** 
- se você usa links simples no obsidian tipo `[[Nome da Nota]]`, deixa "shortest"
- se você organiza muito em pastas e usa caminhos, muda pra "relative" ou "absolute"

Tem que bater com como eu escrevo no obsidian, senão os links vão quebrar no site. Mas como o obsidian não é uma ferramenta que eu uso diariamente e nem sigo o mesmo setup, é muito provável que a forma com que eu uso hoje seja diferente logo menos.

### 3. configurar deploy automático
- [x] ir em Settings > Pages no seu repo
- [x] mudar Source pra "GitHub Actions"
- [x] criar arquivo `.github/workflows/deploy.yaml`
	- [x] escolher uma das opções:
	  - **opção 1:** quartz padrão (copiar yaml do guia)
	  - **opção 2:** com tema do obsidian (trocar `THEME_NAME` pelo tema desejado)

- Seguindo com o padrão quartz, por enquanto

### 4. gerar token de acesso
- [x] ir em https://github.com/settings/personal-access-tokens/new
- [x] nome: algo como "Quartz Syncer token"
- [x] expiração: definir (padrão 30 dias)
	- deixei 90 dias
- [x] acesso: "Only select repositories" > selecionar seu repo quartz
- [x] permissões: Contents > "Read and write"
- [x] clicar "Generate token"
- [x] **COPIAR O TOKEN** (não vai aparecer de novo)

### 5. instalar e configurar quartz syncer
- [x] instalar plugin "Quartz Syncer" no obsidian
- [x] ir em Settings > Community Plugins > Quartz Syncer
- [x] preencher:
  - **GitHub repo name:** nome do seu repo
  - **GitHub username:** seu username
  - **GitHub token:** colar o token gerado
- [x] verificar se aparece checkmark verde

### 6. testar
- [x] criar uma nota no obsidian
- [x] usar o comando do quartz syncer pra publicar
- [x] verificar se apareceu no github
- [x] aguardar deploy (alguns minutos)
- [x] acessar seu site em `<username>.github.io/<repo-name>`

**dica:** se der merda, a documentação de troubleshooting tá linkada no guia original. mas basicamente é sempre problema de token ou configuração de repo.


---

## Outros jardins digitais que utilizam quartz

https://quartz.eilleeenz.com/Quartz-customization-log


## Roadmap

- [ ] Adicionar o efeito sunlight que tem do jzhao
	- https://jzhao.xyz/thoughts/craft
- [ ] Adicionar tanto a data de criação quanto de modificação em cada nota
	- https://luciradis.arczenpulse.dev/Records/The-First-Respite
- [ ] 

Custom css
- https://github.com/SheathedBlade/The-World-of-Luciradis/blob/v4/quartz/styles/custom.scss


## Mais sobre o Quartz

Quartz supports the following frontmatter:

- title
    - `title`
- description
    - `description`
- permalink
    - `permalink`
- comments
    - `comments`
- lang
    - `lang`
- publish
    - `publish`
- draft
    - `draft`
- enableToc
    - `enableToc`
- tags
    - `tags`
    - `tag`
- aliases
    - `aliases`
    - `alias`
- cssclasses
    - `cssclasses`
    - `cssclass`
- socialDescription
    - `socialDescription`
- socialImage
    - `socialImage`
    - `image`
    - `cover`
- created
    - `created`
    - `date`
- modified
    - `modified`
    - `lastmod`
    - `updated`
    - `last-modified`
- published
    - `published`
    - `publishDate`
    - `date`


