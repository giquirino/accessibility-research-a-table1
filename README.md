# digital-accessibility

**Research and hands-on project on digital accessibility testing tools — DAD 2026 · Instituto J&amp;F**

---

## Sobre esta pesquisa

Investigação sobre como desenvolvedores descobrem e medem falhas de acessibilidade digital, mapeando as principais ferramentas do mercado, seus mecanismos de funcionamento e o processo adotado por times que levam acessibilidade a sério.

---

## O que descobrimos (Principais Achados)

- **96,3% dos sites** mais visitados do mundo tinham falhas de acessibilidade detectáveis automaticamente em 2024 — isso inclui bancos, fintechs e portais de governo. *(WebAIM Million Report, 2024)*
- **Nenhuma ferramenta cobre tudo sozinha**: o Lighthouse detecta automaticamente ~30% das falhas WCAG; o restante exige testes manuais com leitores de tela como NVDA ou VoiceOver. *(Deque Systems, 2023)*
- **`alt` ausente e contraste insuficiente** lideram as falhas mais comuns há 5 anos consecutivos — erros simples de corrigir, mas que persistem por falta de processo. *(WebAIM Million Report, 2024)*
- **Ferramentas automatizadas encontram falhas diferentes**: o axe-core tem taxa de falso-positivo menor que o Lighthouse, mas o Wave oferece visualização no contexto da página, mais útil para iniciantes. *(Deque Systems, 2023)*
- **Integrar testes no CI/CD** reduz o custo de correção em até 30x comparado a corrigir em produção — o ponto de maior impacto é o PR review. *(Google, Lighthouse Docs, 2023)*

---

## Ferramentas de Teste de Acessibilidade

### Comparativo das principais ferramentas

| Ferramenta | Tipo | O que detecta | O que não detecta | Como usar |
|---|---|---|---|---|
| **Google Lighthouse** | Automatizada (browser) | Contraste, alt text, labels ARIA, hierarquia de headings, tamanho de touch targets | Contexto semântico, navegação por teclado real, leitor de tela | DevTools > Lighthouse > Accessibility |
| **axe DevTools** | Extensão browser | Falhas WCAG 2.1 A/AA, ARIA inválido, formulários sem label | Comportamento dinâmico (modais, dropdowns) | Extensão Chrome/Firefox |
| **WAVE** | Extensão / web | Visualização inline de erros, alertas e features de acessibilidade | Nada que o axe já não cubra, mas tem UX melhor para aprendizado | wave.webaim.org ou extensão |
| **NVDA** | Leitor de tela (Windows) | Experiência real de usuário com deficiência visual | Nada — simula o usuário real | Gratuito, nvaccess.org |
| **VoiceOver** | Leitor de tela (macOS/iOS) | Idem NVDA, nativo Apple | Nada — simula o usuário real | Nativo: Cmd+F5 (Mac) |
| **axe-core CLI** | Automatizada (terminal/CI) | Mesmo que axe DevTools, mas integrável em pipelines | Testes de contexto visual, interação real | `npm install -g axe-cli` |

### Como cada ferramenta funciona

**Google Lighthouse** injeta um script na página, percorre o DOM e aplica ~30 regras de acessibilidade baseadas em WCAG 2.1. Gera score de 0 a 100. Roda dentro do Chrome DevTools ou via CLI.

**axe-core** usa um motor de regras open-source mantido pela Deque Systems. Tem menor taxa de falso-positivo entre as ferramentas automatizadas. Pode ser embarcado em testes automatizados (Jest, Cypress, Playwright).

**WAVE / WebAIM** renderiza a página com marcações visuais sobrepostas — ícones vermelhos para erros, amarelos para alertas. Ótimo para entender o impacto visual das falhas.

**NVDA / VoiceOver** são leitores de tela reais. São os únicos que revelam falhas que nenhuma ferramenta automatizada detecta: fluxo de navegação confuso, labels que fazem sentido no visual mas não na leitura sequencial, modais que prendem o foco.

### O que cada ferramenta detecta que as outras não detectam

```
Lighthouse    → score consolidado, fácil de reportar para stakeholders
axe-core      → menor falso-positivo, melhor para CI/CD
WAVE          → contexto visual inline, melhor para aprendizado
NVDA/VoiceOver → experiência real — única forma de testar navegação por teclado e fluxo de foco
```

---

## Demonstração Prática

> Demonstração prática: rode o Lighthouse no site do PicPay ou do Banco Original. Qual score de acessibilidade? Quais erros foram encontrados?

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
