---
name: cockpit-creative
description: Cria pacote de criativos de imagem para anuncios Meta Ads de qualquer cliente. Le o manual de marca do cliente (design-guide.md), o dossie (CLAUDE.md), e gera HTMLs com a identidade visual correta, renderizando em feed (1080x1080) e stories (1080x1920) via Playwright. Reusa setup conversacional e qualidade visual da skill /carrossel. Use quando o usuario disser "criar criativo", "novo lote de criativos", "criativos para [cliente]", "anuncios de imagem", "remarketing visual", ou /cockpit-creative.
---

# /cockpit-creative — Criação de Criativos de Imagem para Meta Ads

Cria criativos de imagem (estatica) pra anuncios Meta Ads com a identidade visual de cada cliente. Pega o manual de marca do cliente, le o dossie pra entender produto/promessa/avatar, gera copy direta e renderiza em HTML+PNG nos formatos feed (1080x1080) e stories (1080x1920).

**Filosofia:** mesma qualidade visual da skill `/carrossel` aplicada a criativos pagos. Sem genericos de IA, sem layouts "AI slop". Identidade fiel ao manual de marca do cliente.

---

## Setup (primeira vez)

Antes de criar criativos, checar 4 coisas. Se tudo OK, ir direto pro workflow.

### 1. Cliente

Perguntar de qual cliente sao os criativos. Pode ser dito no comando (`/cockpit-creative Takaki`) ou perguntar:

> "Pra qual cliente vamos criar os criativos? Me diz o nome ou slug (ex: takaki, elaine-sensitiva)"

Validar:
- Existe pasta `clientes/[slug]/` com `CLAUDE.md` (dossie) ja gerado pela skill `cockpit-dossie`?
- Se nao existir, sugerir: *"Esse cliente ainda nao tem dossie. Roda `/cockpit-dossie [nome]` primeiro pra gente ter contexto. Depois volta aqui."*

### 2. Design guide do cliente

Ler `clientes/[slug]/design-guide.md`. Se nao existir ou estiver vazio:

> "Pra criar os criativos com a identidade do [cliente], preciso de:
> 1. Cor principal da marca (hex tipo #FF5C35, ou descreve)
> 2. Cor secundaria/destaque (opcional)
> 3. Fonte de titulo (preferencia ou descricao do estilo)
> 4. Fonte de corpo
> 5. Logo (arquivo na pasta `clientes/[slug]/marca/logo.png`)
> 6. Estilo geral: clean/minimalista, bold/impactante, editorial/elegante, ou outro?
> 7. Tem foto do expert/produto? (joga em `clientes/[slug]/marca/`)"

Salvar em `clientes/[slug]/design-guide.md` no formato:

```markdown
# Design Guide — [Cliente]

## Paleta
- **Primaria:** #XXXXXX
- **Secundaria:** #XXXXXX
- **Fundo escuro:** #XXXXXX
- **Fundo claro:** #XXXXXX
- **Texto:** #XXXXXX

## Tipografia
- **Display (titulos):** [fonte]
- **Body (corpo):** [fonte]

## Logo
- Arquivo: `clientes/[slug]/marca/logo.png`
- Variacoes: claro / escuro (se tiver)

## Estilo
[clean / bold / editorial / outro]

## Referencias visuais (opcional)
- [link ou path de imagens de referencia]
```

Se o usuario nao souber as cores exatas, pegar do site do cliente (ler com WebFetch) ou usar a paleta do logo.

### 3. Dossie do cliente

Ler `clientes/[slug]/CLAUDE.md` (gerado pelo `/cockpit-dossie`). Precisa ter pelo menos:
- Nome do produto principal
- Promessa central
- Avatar (dores + desejos)
- Provas sociais

Se faltar essas secoes, avisar e oferecer atualizar dossie primeiro: *"Pra escrever bons criativos preciso dessas infos. Quer rodar `/cockpit-dossie [cliente]` rapido pra completar?"*

### 4. Playwright

Verificar se ta instalado:

```bash
npx playwright screenshot --help 2>/dev/null && echo "OK" || echo "INSTALAR"
```

Se nao tiver, instalar:

```bash
npx playwright install chromium
```

Avisar que demora ~30s na primeira vez.

---

## Workflow em 3 Fases

### Fase 1 — Briefing + Copy

1. Ler `clientes/[slug]/CLAUDE.md` (dossie) e `clientes/[slug]/design-guide.md`
2. Perguntar briefing rapido:

> "Vou criar criativos pro [cliente]. Antes de escrever, me confirma:
> 1. Quantos criativos? (padrao: 5 imagens)
> 2. Objetivo: leads / vendas / engajamento / outro?
> 3. Angulo principal: dor / desejo / prova social / oferta / conteudo educativo?
> 4. Tem algo NOVO pra destacar? (lancamento, promocao, evento, depoimento recente)
> 5. Texto base ou ideia pronta, ou eu crio do zero?"

3. Gerar **copy de cada criativo** seguindo principios:
   - **Headline forte** (4-8 palavras, foco em dor ou desejo do avatar)
   - **Sub-headline** (1-2 linhas, contexto)
   - **CTA claro** (1 frase, acao especifica)
   - **Logo + identidade** visivel mas sem dominar
   - Usar a linguagem do dossie (termos proprios do avatar)
   - Nada generico ("transforme sua vida", "descubra o segredo", etc.)
   - Sem travessoes (a nao ser que `_contexto/preferencias.md` diga o contrario)

4. Variar angulos entre os criativos. Padrao de 5 criativos:
   - 1 dor (avatar reconhece o problema)
   - 1 desejo (avatar visualiza o resultado)
   - 1 prova social (depoimento ou numero forte)
   - 1 oferta direta (produto + beneficio)
   - 1 autoridade (mostra expertise do expert)

5. Mostrar copy completa no chat (todos os criativos com headline + sub + CTA)

6. Salvar em `clientes/[slug]/criativos/YYYY-MM-DD_[campanha]/copy.md`

**CHECKPOINT 1:** Esperar usuario aprovar copy antes de seguir pra Fase 2. Se pedir ajuste, mudar so o criativo apontado.

---

### Fase 2 — Visual (HTMLs)

1. **Acionar skill `frontend-design`** pra orientar o design dos criativos. Ler `~/.claude/skills/frontend-design/SKILL.md` e aplicar os principios:
   - Tipografia distintiva (nao usar Inter/Arial genericos)
   - Direcao estetica clara (escolher entre brutalist, editorial, minimal, etc., com base no `design-guide.md`)
   - Detalhe visual em todo lugar (espacamento, hierarquia, contraste)
   - Diferenciacao memoravel

2. Criar 1 HTML por criativo na pasta `clientes/[slug]/criativos/YYYY-MM-DD_[campanha]/feed/`:
   - Tamanho: 1080x1080 (feed)
   - Usar a paleta do `design-guide.md` do cliente
   - Logo posicionado de forma sutil (canto inferior, com handle do IG abaixo)
   - Foto do expert (se tiver) integrada ao design, nao colada
   - Texto seguindo a hierarquia headline > sub > CTA

3. **Criar versao stories** (1080x1920) na pasta `stories/`:
   - Mesmo conceito do feed, mas re-layoutado pra vertical
   - Logo no topo (safe area)
   - CTA na parte de baixo (com safe zone 230px pra controles do IG)

4. Renderizar **criativo 1 primeiro** via Playwright:

```bash
npx playwright screenshot --viewport-size=1080,1080 --full-page "file:///caminho/absoluto/feed/criativo-01.html" "feed/criativo-01.png"
npx playwright screenshot --viewport-size=1080,1920 --full-page "file:///caminho/absoluto/stories/criativo-01.html" "stories/criativo-01.png"
```

**CHECKPOINT 2:** Mostrar criativo 1 renderizado (feed + stories). Se aprovado, renderizar os outros. Se pedir ajuste, editar HTML e re-renderizar so esse.

---

### Fase 3 — Output final

```
clientes/[slug]/criativos/YYYY-MM-DD_[campanha]/
  copy.md                      <- copy aprovada (headlines + subs + CTAs)
  feed/
    criativo-01.html -> criativo-01.png  (1080x1080)
    criativo-02.html -> criativo-02.png
    ...
  stories/
    criativo-01.html -> criativo-01.png  (1080x1920)
    criativo-02.html -> criativo-02.png
    ...
```

Mostrar grade visual com todos os criativos rendererizados. Perguntar:

> "Bora subir pro Meta Ads? Se sim, posso usar a `/cockpit-meta` pra criar os anuncios direto na conta de [cliente]."

Se o usuario topar, chamar `/cockpit-meta` passando o path dos criativos pra subir.

---

## Geração de imagens auxiliares (opcional)

Se o usuario quiser imagem de fundo gerada por IA:

### Nano Banana (recomendado)

Se a skill `nanobanana-ratos` estiver instalada, usar pra gerar imagens de fundo abstratas ou produto.

**Dica do prompt:** ser especifico + adicionar "no text, clean background, professional" pra resultado limpo.

### Alternativa sem skill

Orientar a gerar em Canva/ChatGPT/Midjourney e jogar na pasta `clientes/[slug]/criativos/[campanha]/imagens/`.

---

## Diferencas entre criativo de IMAGEM (essa skill) e criativo de VIDEO

Esta skill cria apenas **imagens estaticas** (feed 1080x1080 e stories 1080x1920). Pra video, usar editores tradicionais (CapCut, Premiere) ou a `cockpit-remotion` (em desenvolvimento).

Boa pratica: usar imagens como **remarketing** dos videos. Quem viu o video por X segundos vira publico de remarketing, e ve as imagens depois.

---

## Regras

- **Copy aprovada na Fase 1 nao muda na Fase 2** (visual fiel a copia)
- **Sempre mostrar criativo 1 primeiro** antes de renderizar os demais
- **Se o usuario pedir ajuste no visual**, editar o HTML e re-renderizar apenas esse criativo
- **Sem travessoes no texto** por padrao
- **Setup feito antes nao repetir** — ir direto pro workflow
- **Identidade visual e do cliente**, nao do Cockpit nem do usuario — sempre ler design-guide.md
- **Diferenciacao:** se os 5 criativos do lote ficarem parecidos, refazer com angulos mais distintos

## Skills relacionadas

- `/cockpit-dossie` — gera o dossie do cliente que essa skill le
- `/carrossel` — skill irma, mesmo padrao visual mas para Instagram organico
- `frontend-design` — usado internamente pra qualidade visual
- `/cockpit-meta` — pra subir os criativos prontos pro Meta Ads
- `nanobanana-ratos` — pra gerar imagens de fundo via IA (opcional)
