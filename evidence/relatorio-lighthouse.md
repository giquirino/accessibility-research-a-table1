# Relatório de Acessibilidade — Resultados do Lighthouse

> Auditorias realizadas com Google Lighthouse 12.4.0 via CLI  
> Modo: Headless · Categoria: Accessibility only · Data: Abril/2025

---

## picpay.com — Score: 76/100

### Falhas encontradas (7 erros, 32 elementos afetados)

| # | Critério WCAG | Erro | Elementos | Impacto |
|---|---|---|---|---|
| 1 | 1.1.1 — Non-text Content | Imagens sem atributo `alt` | 4 | Invisível para leitores de tela |
| 2 | 1.4.3 — Contrast (Minimum) | Contraste insuficiente entre texto e fundo | 2 | Dificulta leitura para baixa visão |
| 3 | 2.4.6 — Headings and Labels | Hierarquia de headings fora de ordem (h4 sem h3 anterior) | 5 | Navegação confusa por leitor de tela |
| 4 | 4.1.2 — Name, Role, Value | Botões sem nome acessível | 3 | Leitor de tela anuncia apenas "button" |
| 5 | 2.5.3 — Label in Name | Labels visíveis não correspondem ao nome acessível | 2 | Confusão para usuários de voz |
| 6 | 2.4.4 — Link Purpose | Links sem texto discernível | 3 | Leitor de tela não consegue descrever o link |
| 7 | 2.5.5 — Target Size | Alvos de toque pequenos demais (< 24x24px) | 13 | Dificulta uso por pessoas com limitação motora |

### Detalhes dos erros mais críticos

**Botões sem nome acessível** (impacto crítico em leitores de tela)
```html
<!-- ❌ Encontrado no picpay.com — botões do carrossel sem label -->
<button class="teaser__dot active" data-gtm-button-decorated="true">
</button>

<!-- ✅ Correção correta -->
<button class="teaser__dot active" aria-label="Ir para o slide 1">
</button>
```

**Imagens sem `alt`** (viola WCAG 1.1.1 — Non-text Content)
```html
<!-- ❌ Encontrado no picpay.com -->
<img src="https://publish-p153277-e1666050.adobeaemcloud.com/..." loading="lazy">

<!-- ✅ Correção para imagem informativa -->
<img src="..." alt="Tela do app PicPay mostrando saldo disponível" loading="lazy">

<!-- ✅ Correção para imagem decorativa -->
<img src="..." alt="" role="presentation" loading="lazy">
```

**Links sem nome discernível** (viola WCAG 2.4.4)
```html
<!-- ❌ Encontrado — links de card sem texto visível ou aria-label -->
<a href="https://blog.picpay.com/carteira-de-trabalho-digital/" role="button" 
   data-gtm-name="CARTEIRA_TRABALHO_DIGITAL">
</a>

<!-- ✅ Correção -->
<a href="..." aria-label="Ver artigo: Carteira de trabalho digital">
</a>
```

### O que passou (22 critérios aprovados)

- ARIA attributes são válidos e correspondem aos roles
- Documento tem `<title>` definido
- `<html>` tem atributo `lang` válido (`pt-BR`)
- Nenhum elemento com `tabindex > 0` (boa prática)
- Zoom não bloqueado via `user-scalable=no`
- Listas estruturadas corretamente (`<li>` dentro de `<ul>/<ol>`)

---

## original.com.br — Score: 92/100

### Falhas encontradas (2 erros, 12 elementos afetados)

| # | Critério WCAG | Erro | Elementos | Impacto |
|---|---|---|---|---|
| 1 | 2.4.6 — Headings and Labels | Hierarquia de headings fora de ordem | 3 | Navegação confusa em leitores de tela |
| 2 | 1.1.1 — Non-text Content | Imagens sem atributo `alt` | 9 | Invisível para leitores de tela |

### Análise comparativa

O Banco Original (92/100) performou **16 pontos acima** do PicPay (76/100).  
Ambos cometem o mesmo erro de hierarquia de headings e imagens sem `alt` — sinalizando que são falhas sistêmicas comuns em sites com muitos componentes de marketing.

A diferença principal está nos erros ausentes no Original: sem botões sem nome, sem problemas de contraste, sem links sem nome discernível — indicando maior maturidade no processo de desenvolvimento acessível.

---

## Contexto da auditoria

```
Ferramenta : Google Lighthouse 12.4.0 (CLI)
Node.js    : v25.8.2
Modo       : --headless --no-sandbox
Escopo     : Homepage de cada site
Padrão     : WCAG 2.1 A/AA (via regras Lighthouse Accessibility)
```

> **Nota**: O Lighthouse cobre aproximadamente 30–40% das falhas WCAG detectáveis.  
> Falhas que exigem contexto semântico, navegação real por teclado ou fluxo de foco  
> não são capturáveis automaticamente — exigem testes com NVDA/VoiceOver.
