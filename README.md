# digital-accessibility

**Research and hands-on project on digital accessibility testing tools — DAD 2026 · Instituto J&F**

---

## Sobre esta pesquisa

Investigação sobre como desenvolvedores descobrem e medem falhas de acessibilidade digital, mapeando as principais ferramentas do mercado, seus mecanismos de funcionamento e o processo adotado por times que levam acessibilidade a sério.

---

## O que descobrimos (Principais Achados)

- **96,3% dos sites** mais visitados do mundo tinham falhas de acessibilidade detectáveis automaticamente em 2024 — isso inclui bancos, fintechs e portais de governo. *(WebAIM Million Report, 2024)*
- **Nenhuma ferramenta cobre tudo sozinha**: o Lighthouse detecta automaticamente ~30% das falhas WCAG; o restante exige testes manuais com leitores de tela como NVDA. *(Deque Systems, 2023)*
- **`alt` ausente e contraste insuficiente** lideram as falhas mais comuns há 5 anos consecutivos — erros simples de corrigir, mas que persistem por falta de processo. *(WebAIM Million Report, 2024)*
- **Ferramentas automatizadas encontram falhas diferentes**: o axe-core tem taxa de falso-positivo menor que o Lighthouse, mas o Wave oferece visualização no contexto da página, mais útil para iniciantes. *(Deque Systems, 2023)*
- **Integrar testes no CI/CD** reduz o custo de correção em até 30x comparado a corrigir em produção — o ponto de maior impacto é o PR review. *(Google, Lighthouse Docs, 2023)*

---

## Ferramentas de Teste de Acessibilidade

### Comparativo rápido

| Ferramenta | Tipo | O que detecta | O que não detecta | Como usar |
|---|---|---|---|---|
| **Google Lighthouse** | Automatizada (browser) | Contraste, alt text, labels ARIA, hierarquia de headings, tamanho de touch targets | Contexto semântico, navegação por teclado real, leitor de tela | DevTools > Lighthouse > Accessibility |
| **axe DevTools** | Extensão browser | Falhas WCAG 2.1 A/AA, ARIA inválido, formulários sem label | Comportamento dinâmico (modais, dropdowns) | Extensão Chrome/Firefox |
| **WAVE** | Extensão / web | Visualização inline de erros, alertas e features de acessibilidade | SPAs e conteúdo dinâmico | wave.webaim.org ou extensão |
| **NVDA** | Leitor de tela (Windows) | Experiência real de usuário com deficiência visual | Nada — simula o usuário real | Gratuito, nvaccess.org |

| **axe-core CLI** | Automatizada (terminal/CI) | Mesmo que axe DevTools, mas integrável em pipelines | Testes de contexto visual, interação real | `npm install -g axe-cli` |

---

### 1. Google Lighthouse

**Tipo:** Automatizada — nativa no Chrome
**Criada por:** Google (open-source)
**Repositório:** https://github.com/GoogleChrome/lighthouse

O Lighthouse é uma ferramenta de auditoria de páginas web. Ele analisa performance, acessibilidade, SEO e boas práticas — tudo de uma vez — e entrega um score de 0 a 100 em cada categoria.

A engine de acessibilidade por baixo é o **axe-core** da Deque Systems. Por isso os erros aparecem com nomes padronizados como `image-alt`, `button-name`, `color-contrast`.

**Como funciona por dentro:**
```
1. Abre um Chrome isolado (sem extensões, sem cache, sem cookies)
2. Carrega a página simulando um dispositivo mobile (Moto G Power por padrão)
3. Percorre o DOM e aplica ~50 regras de acessibilidade
4. Calcula o score como média ponderada (cada regra tem um peso)
5. Gera relatório em HTML e/ou JSON
```

**Como usar — F12 (DevTools):**
1. Abra o Chrome e vá até o site que quer testar
2. Pressione `F12` → clique na aba **Lighthouse**
3. Marque **Accessibility** e selecione **Mobile**
4. Clique em **Analyze page load**

**Como usar — CLI (Terminal):**
```bash
# Instalar uma vez
npm install -g lighthouse

# Rodar contra qualquer site
lighthouse https://www.picpay.com/ \
  --only-categories=accessibility \
  --output=html \
  --output=json \
  --output-path=./evidence/lighthouse-resultado \
  --form-factor=mobile \
  --chrome-flags="--headless=new"
```

Isso gera dois arquivos: `lighthouse-resultado.html` (relatório visual) e `lighthouse-resultado.json` (dados brutos que vão pro repositório como evidência).

**O que NÃO detecta:**
- Se a ordem de leitura com leitor de tela faz sentido
- Se o fluxo de navegação por teclado funciona em modais e menus
- Se o conteúdo dinâmico (toasts, autocomplete) é anunciado corretamente
- Se o `aria-label` é semanticamente correto — ele só vê que existe

> **Score 100 não significa acessibilidade total.** O próprio Google avisa isso na documentação.

---

### 2. axe DevTools

**Tipo:** Automatizada — extensão do navegador
**Criada por:** Deque Systems
**Instalar:** [Chrome Web Store — axe DevTools](https://chrome.google.com/webstore/detail/axe-devtools-web-accessib/lhdoppojpmngadmnindnejefpokejbdd)

Extensão gratuita para Chrome e Firefox baseada no **axe-core** — o mesmo motor do Lighthouse, mas com **~90 regras** (quase o dobro). A principal vantagem é a menor taxa de falso-positivo: o axe só reporta o que tem certeza que é erro.

**Como usar:**
1. Instale a extensão no Chrome
2. Abra o site que quer testar
3. Pressione `F12` → clique na aba **axe DevTools**
4. Clique em **Scan ALL of my page**
5. O resultado aparece com o elemento exato na página destacado

**O que detecta de diferente:** tudo que o Lighthouse detecta, mais erros de ARIA mais sutis (atributos que existem mas estão errados), formulários com label errado ou incompleto, e casos que o Lighthouse deixa passar por ser mais conservador.

**Quando usar:** auditoria detalhada depois do Lighthouse, ou quando o Lighthouse passou mas você suspeita que tem algo errado.

---

### 3. WAVE

**Tipo:** Automatizada — extensão / site
**Criada por:** WebAIM
**Usar online:** https://wave.webaim.org
**Extensão:** [Chrome Web Store — WAVE](https://chrome.google.com/webstore/detail/wave-evaluation-tool/jbbplnpkjmmeebjpijfedlgcdilocofh)

O WAVE funciona de forma visual: em vez de gerar um relatório separado, ele **sobrepõe ícones coloridos diretamente na página** que você está olhando.

- 🔴 **Vermelho** = erro (precisa corrigir)
- 🟡 **Amarelo** = alerta (verifique se é problema)
- 🟢 **Verde** = recurso acessível encontrado
- 🔵 **Azul** = elemento estrutural (heading, landmark)

**Limitações:** não funciona bem com SPAs (React, Angular, Vue) e não gera JSON para evidência.

**Quando usar:** revisão visual da interface antes de entregar uma tela. Mais intuitivo para quem está aprendendo.

---

### 4. axe-core CLI

**Tipo:** Automatizada — terminal / CI/CD
**Criada por:** Deque Systems
**Repositório:** https://github.com/dequelabs/axe-core

A versão de linha de comando do axe-core. Tem as mesmas ~90 regras do axe DevTools, mas roda no terminal — permitindo integrá-lo em pipelines de CI/CD (GitHub Actions, GitLab CI, Jenkins).

```bash
# Instalar
npm install -g axe-cli

# Rodar contra um site
axe https://www.picpay.com/ --reporter json > evidence/axe-resultado.json
```

**Integração com GitHub Actions:**
```yaml
# .github/workflows/accessibility.yml
name: Accessibility Check
on: [pull_request]

jobs:
  a11y:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Install axe-cli
        run: npm install -g axe-cli
      - name: Run accessibility check
        run: axe https://staging.meusite.com/ --exit
        # --exit faz o job falhar se encontrar erros — bloqueia o merge
```

**Quando usar:** no pipeline de CI/CD para bloquear PR automaticamente se houver erro.

---

### 5. NVDA

**Tipo:** Manual — leitor de tela
**Sistema:** Windows
**Custo:** Gratuito
**Download:** https://www.nvaccess.org/download/

NVDA (NonVisual Desktop Access) é o leitor de tela mais usado no mundo em Windows. É o software que **pessoas cegas usam no dia a dia** para navegar em sites.

Para testar, você **desliga o monitor** e navega o site só com o teclado e o áudio:
```
Tab         → próximo elemento interativo
Shift+Tab   → elemento anterior
Enter       → ativar link ou botão
H           → pular para próximo heading
F           → pular para próximo formulário
```

**O que detecta que as ferramentas automáticas não detectam:**
- Ordem de leitura — o NVDA lê na ordem do DOM, não na visual
- Qualidade dos labels — se o que está escrito no `aria-label` faz sentido de verdade
- Fluxo de foco em modais — se abrir um modal e o foco não entrar nele, o NVDA fica preso fora
- Anúncio de conteúdo dinâmico — toasts, autocomplete e mensagens de erro precisam de `aria-live`

> Esse passo pega os **43% de falhas** que nenhuma ferramenta automática encontra.

---

---

### O que cada ferramenta detecta que as outras não detectam

```
Lighthouse     → score consolidado, fácil de reportar para stakeholders
axe-core       → menor falso-positivo, melhor para CI/CD
WAVE           → contexto visual inline, melhor para aprendizado
NVDA          → experiência real — única forma de testar navegação por teclado e fluxo de foco
```

### Onde cada uma encaixa no fluxo do dev

```
┌────────────────────────────────────────────────────────────────┐
│  1. ENQUANTO ESCREVE O CÓDIGO                                  │
│     → eslint-plugin-jsx-a11y no editor                         │
│     → Marca <img> sem alt e <button> vazio enquanto você digita│
├────────────────────────────────────────────────────────────────┤
│  2. ANTES DE COMMITAR                                          │
│     → Lighthouse no DevTools (F12 → Lighthouse → Analyze)      │
│     → axe DevTools para auditoria mais detalhada               │
├────────────────────────────────────────────────────────────────┤
│  3. NO PIPELINE (CI/CD)                                        │
│     → axe-core CLI no GitHub Actions                           │
│     → PR não merga se score cair abaixo do mínimo              │
├────────────────────────────────────────────────────────────────┤
│  4. ANTES DE LIBERAR EM PRODUÇÃO                               │
│     → NVDA: navega o fluxo SÓ COM TECLADO                     │
│     → Esse passo pega os 43% que o automático não pega         │
└────────────────────────────────────────────────────────────────┘
```

---

## Lighthouse via CLI vs. F12 (DevTools) — Qual a diferença?

São duas formas de usar a **mesma ferramenta**, mas com propósitos diferentes:

| | F12 (DevTools) | CLI (`npm install -g lighthouse`) |
|---|---|---|
| **Como roda** | Manual — abre o browser, aperta F12, clica em Analyze | Automatizado — roda um comando no terminal |
| **Resultado** | Relatório visual na tela do browser | Arquivo JSON/HTML salvo no disco |
| **Reproduzível?** | Não — depende de quem rodou e quando | Sim — mesmo comando gera mesmo resultado |
| **Versionável?** | Não — teria que tirar print | Sim — o JSON vai pro repositório como evidência |
| **Integrável no CI/CD?** | Não — não dá pra abrir F12 num servidor de CI | Sim — roda em qualquer pipeline automatizado |
| **Uso ideal** | Diagnóstico rápido durante o desenvolvimento | Evidência formal, quality gate, automação |

**Por que o projeto usa via CLI:** o dev roda `lighthouse` no terminal antes de abrir o PR, o JSON gerado vai como evidência no repositório, e no CI/CD o pipeline roda o mesmo comando automaticamente. Se o score ficar abaixo de 90, o merge é bloqueado. Isso elimina o fator humano.

---

## Demonstração Prática

Auditoria realizada com **Google Lighthouse 12.4.0 via CLI** nas homepages dos dois sites do Grupo J&F.

### Resultados

| Site | Score | Erros | Elementos afetados |
|---|---|---|---|
| **picpay.com** | 🔴 76/100 | 7 | 32 |
| **original.com.br** | 🟢 92/100 | 2 | 12 |

---

### picpay.com — Score: 76/100

| # | Critério WCAG | Erro | Elementos |
|---|---|---|---|
| 1 | 1.1.1 — Non-text Content | Imagens sem atributo `alt` | 4 |
| 2 | 1.4.3 — Contrast (Minimum) | Contraste insuficiente entre texto e fundo | 2 |
| 3 | 2.4.6 — Headings and Labels | Hierarquia de headings fora de ordem (h4 sem h3 anterior) | 5 |
| 4 | 4.1.2 — Name, Role, Value | Botões sem nome acessível | 3 |
| 5 | 2.5.3 — Label in Name | Labels visíveis não correspondem ao nome acessível | 2 |
| 6 | 2.4.4 — Link Purpose | Links sem texto discernível | 3 |
| 7 | 2.5.5 — Target Size | Alvos de toque pequenos demais (< 24x24px) | 13 |

**Erro mais crítico — botões sem nome acessível:**
```html
<!-- ❌ Encontrado no picpay.com — botões do carrossel sem label -->
<button class="teaser__dot active" data-gtm-button-decorated="true"></button>

<!-- ✅ Correção -->
<button class="teaser__dot active" aria-label="Ir para o slide 1"></button>
```

**Imagens sem `alt`:**
```html
<!-- ❌ Encontrado no picpay.com -->
<img src="https://publish-p153277-e1666050.adobeaemcloud.com/..." loading="lazy">

<!-- ✅ Correção -->
<img src="..." alt="Tela do app PicPay mostrando saldo disponível" loading="lazy">
```

---

### original.com.br — Score: 92/100

| # | Critério WCAG | Erro | Elementos |
|---|---|---|---|
| 1 | 2.4.6 — Headings and Labels | Hierarquia de headings fora de ordem | 3 |
| 2 | 1.1.1 — Non-text Content | Imagens sem atributo `alt` | 9 |

---

### Análise comparativa

O Banco Original performou **16 pontos acima** do PicPay. Ambos cometem os mesmos erros base (hierarquia de headings e imagens sem `alt`) — falhas sistêmicas comuns em sites com muitos componentes de marketing.

A diferença está no que o Original **não tem**: nenhum botão sem nome, nenhum problema de contraste, nenhum link sem texto discernível — indicando maior maturidade no processo de desenvolvimento acessível.

> Os JSONs brutos das auditorias estão em [`evidence/lighthouse-picpay-raw.json`](./evidence/lighthouse-picpay-raw.json) e [`evidence/lighthouse-original-raw.json`](./evidence/lighthouse-original-raw.json).

---

## Como isso afeta o nosso trabalho como desenvolvedores

### 3 práticas concretas que todo dev pode adotar a partir de amanhã

**1. Todo `<img>` tem `alt`. Sem exceção.**

```html
<!-- ❌ Errado -->
<img src="produto.jpg">

<!-- ✅ Decorativa (sem informação) -->
<img src="icone-decorativo.svg" alt="">

<!-- ✅ Informativa -->
<img src="cartao-picpay.jpg" alt="Cartão de crédito PicPay na cor verde">
```

**2. Todo `<button>` e `<a>` tem nome acessível.**

```html
<!-- ❌ Errado — leitor de tela anuncia só "button" -->
<button><svg>...</svg></button>

<!-- ✅ Correto — via aria-label -->
<button aria-label="Fechar modal de confirmação"><svg>...</svg></button>

<!-- ✅ Correto — via aria-labelledby -->
<button aria-labelledby="titulo-secao">Ver mais</button>
```

**3. Rodar o Lighthouse antes de abrir PR.**

```bash
# Instalar uma vez
npm install -g lighthouse

# Rodar antes do PR
lighthouse https://localhost:3000 \
  --only-categories=accessibility \
  --output=json \
  --output-path=./evidence/lighthouse-$(date +%Y%m%d).json \
  --chrome-flags="--headless"
```

Qualquer score abaixo de 90 deve bloquear o merge — da mesma forma que um teste unitário falhando.

> **Regra prática**: se você não consegue navegar na sua página só com Tab e Enter, tem problema de acessibilidade.

---

## Referências

Veja o arquivo [`references.md`](./references.md) para a lista completa de fontes.