# Checklist de Acessibilidade — Antes de Abrir o PR

> **Dimensão A — DAD 2026 · Instituto J&F**  
> Use este checklist antes de mergear qualquer PR que envolva UI.  
> Tempo estimado: 5 minutos. Custo de não fazer: correção 30x mais cara em produção.

---

## Checklist Rápido (2 min)

```
□ Naveguei a feature SÓ com Tab / Shift+Tab / Enter — funciona?
□ Foco visível em todos os elementos interativos?
□ Lighthouse score >= 90?
□ Imagens novas têm alt (ou alt="" se decorativas)?
□ Botões com ícone têm aria-label?
□ Touch targets têm pelo menos 24×24px?
□ Contraste de texto >= 4.5:1 com o fundo?
□ Hierarquia de headings sem saltos (h1 → h2 → h3)?
```

---

## Por que cada item importa

### ✅ Navegar só com teclado

A regra mais simples: se você não consegue usar a feature sem o mouse, ela está inacessível para pessoas que dependem de teclado — isso inclui pessoas com deficiência motora e usuários de leitor de tela.

**Como testar:** feche a mão, não toque no mouse. Use só Tab, Shift+Tab, Enter e as setas.

---

### ✅ Foco visível

Quando você navega com Tab, o elemento focado precisa ter um indicador visual claro (borda, sombra, destaque). Sem isso, o usuário não sabe onde está na página.

```css
/* ❌ Nunca faça isso */
:focus {
  outline: none;
}

/* ✅ Mantenha o outline ou substitua por algo equivalente */
:focus-visible {
  outline: 2px solid #0066cc;
  outline-offset: 2px;
}
```

---

### ✅ Lighthouse score >= 90

```bash
# Rodar antes de abrir o PR
lighthouse https://localhost:3000 \
  --only-categories=accessibility \
  --output=json \
  --output-path=./evidence/lighthouse-$(date +%Y%m%d).json \
  --chrome-flags="--headless"
```

Score abaixo de 90 = tem algo errado. Corrija antes de pedir review.

---

### ✅ Imagens com alt

```html
<!-- ❌ Errado — leitor de tela anuncia o nome do arquivo -->
<img src="cartao-picpay-verde.jpg">

<!-- ✅ Informativa — descreva o conteúdo relevante -->
<img src="cartao-picpay-verde.jpg" 
     alt="Cartão de crédito PicPay na cor verde">

<!-- ✅ Decorativa — alt vazio, leitor de tela ignora -->
<img src="icone-decorativo.svg" alt="" role="presentation">
```

**Regra:** se a imagem carrega informação, descreva essa informação no alt.  
Se é só decoração, use `alt=""`.  
Nunca omita o atributo alt.

---

### ✅ Botões e links com nome acessível

```html
<!-- ❌ Errado — leitor de tela anuncia só "botão" -->
<button>
  <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24">...</svg>
</button>

<!-- ✅ Correto — via aria-label -->
<button aria-label="Fechar modal de confirmação">
  <svg aria-hidden="true">...</svg>
</button>

<!-- ✅ Correto — via texto visível (melhor opção quando possível) -->
<button>
  <svg aria-hidden="true">...</svg>
  Fechar
</button>
```

**Erro encontrado no PicPay:** botões do carrossel completamente vazios.

```html
<!-- ❌ Encontrado no picpay.com -->
<button class="teaser__dot active"></button>

<!-- ✅ Correção — uma linha -->
<button class="teaser__dot active" aria-label="Ir para o slide 1 de 5"></button>
```

---

### ✅ Touch targets >= 24×24px

Elementos clicáveis menores que 24×24 pixels dificultam o uso para pessoas com tremor, idosos e usuários de touchscreen.

```css
/* ❌ Encontrado no PicPay — 8x8px, um terço do mínimo */
.cardgrid__dot {
  width: 8px;
  height: 8px;
}

/* ✅ Opção 1 — aumentar o elemento visualmente */
.cardgrid__dot {
  width: 24px;
  height: 24px;
}

/* ✅ Opção 2 — manter visual, expandir área de toque */
.cardgrid__dot {
  width: 8px;
  height: 8px;
  position: relative;
}
.cardgrid__dot::before {
  content: '';
  position: absolute;
  inset: -8px; /* expande a área clicável para 24x24 sem mudar o visual */
}
```

---

### ✅ Contraste de texto >= 4.5:1

Texto com baixo contraste é ilegível para pessoas com baixa visão ou daltonismo.

**Mínimos WCAG:**
- Texto normal: **4.5:1**
- Texto grande (18pt+ ou 14pt bold): **3:1**
- Ícones e elementos gráficos: **3:1**

```css
/* ❌ Encontrado no PicPay — contraste 2.28:1 */
.wrapped-teaser h2 { color: #999999; } /* cinza claro sobre fundo claro */

/* ✅ Correção — contraste 7:1 */
.wrapped-teaser h2 { color: #595959; }
```

**Como verificar:** https://webaim.org/resources/contrastchecker/

---

### ✅ Hierarquia de headings sem saltos

Leitores de tela usam os headings (h1, h2, h3...) para construir um sumário navegável da página. Saltar níveis quebra essa navegação.

```html
<!-- ❌ Errado — pula do h2 direto para h4 -->
<h1>PicPay</h1>
  <h2>Conheça nossos cartões</h2>
    <h4>PicPay Card</h4>  <!-- cadê o h3? -->

<!-- ✅ Correto — hierarquia contínua -->
<h1>PicPay</h1>
  <h2>Conheça nossos cartões</h2>
    <h3>PicPay Card</h3>
```

**Erro encontrado no PicPay:** 5 elementos `h4` sem `h3` anterior.

---

## Erros mais comuns (WebAIM Million 2024)

Esses são os erros que aparecem em mais de 80% dos sites com falhas:

| Erro | % dos sites afetados | Dificuldade de correção |
|---|---|---|
| Baixo contraste | 81% | Baixa |
| `alt` ausente em imagens | 54% | Baixa |
| Links sem texto | 48% | Baixa |
| Labels ausentes em formulários | 45% | Média |
| Botões sem nome | 27% | Baixa |
| Idioma da página não declarado | 18% | Baixa |

> Todos os 7 erros encontrados no PicPay são desse grupo — os mais comuns e os mais fáceis de corrigir.

---

## Configurar o eslint-plugin-jsx-a11y (React / Next.js)

Para pegar esses erros enquanto você digita, antes mesmo de abrir o navegador:

```bash
npm install --save-dev eslint-plugin-jsx-a11y
```

```json
// .eslintrc.json
{
  "extends": ["plugin:jsx-a11y/recommended"],
  "plugins": ["jsx-a11y"]
}
```

O editor passa a acusar:
- `<img>` sem `alt`
- `<div onClick>` sem `role` e `tabIndex`
- `<a>` sem conteúdo de texto
- `<button>` vazio

---

## Referências

- WebAIM. *The WebAIM Million 2024*. https://webaim.org/projects/million/
- W3C. *WCAG 2.1 — Success Criteria*. https://www.w3.org/TR/WCAG21/
- WebAIM. *Contrast Checker*. https://webaim.org/resources/contrastchecker/
- MDN. *ARIA — Accessible Rich Internet Applications*. https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA