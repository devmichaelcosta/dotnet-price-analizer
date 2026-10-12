# Especificação — CompareCurrentOffers

**Estado:** Rascunho — 2026-10-12<br>
**Escopo:** MVP manual<br>
**Módulo:** PriceAnalysis<br>
**Responsável pelo domínio:** Produto

## Problema e objetivo

Permitir que a pessoa compare os últimos preços informados para ofertas manuais do mesmo produto, sem tratar variantes ou condições incompatíveis como equivalentes nem afirmar que o valor ainda está vigente na loja.

## Atores e autorização

- Ator: usuário autenticado e proprietário do produto/acompanhamento.
- O backend valida propriedade antes de carregar ofertas ou observações.

## Contexto e regras de domínio

- `DR-001`: produto, oferta e observação são conceitos separados.
- `DR-003`/`DR-004`/`DR-005`: dinheiro usa decimal/moeda, a observação tem instante e indisponibilidade não é preço zero.
- `DR-006`: não comparar variantes, condições ou kits incompatíveis.
- `DR-007`: considerar somente o preço do item, sem frete.
- `DR-012`/`DR-018`: dados privados por conta e origem manual identificada.

## Escopo

### Incluído

- Consultar ofertas do produto acompanhado e suas observações mais recentes.
- Agrupar/apresentar somente ofertas comparáveis conforme as regras aprovadas.
- Identificar origem manual, moeda e instante de cada valor.

### Fora de escopo

- Comparar moedas diferentes ou converter cotação.
- Comparar variantes, condições, kits ou critérios de preço incompatíveis.
- Incorporar frete, cashback, assinatura ou benefício condicionado não aprovado.
- Buscar ou atualizar preços automaticamente.

## Comportamento esperado

Para um produto autorizado, o sistema apresenta a observação mais recente por oferta. Pela proposta F0-17, a interface chama o valor de “último preço informado”, exibe data/hora e autoria, e não afirma que o valor continua vigente na loja. Só destaca o menor valor entre ofertas disponíveis, com preço positivo, moeda BRL, variante e condição compatíveis e conhecidas. Dados desconhecidos, indisponíveis ou sem preço aparecem fora do ranking. Vendedor é exibido, mas não impede a comparação por si só. Essa política permanece proposta até aprovação.

## Cenários de aceite

### AC-CCO-001 — Comparar ofertas compatíveis

**Dado** que o usuário tem duas ou mais ofertas do mesmo produto com últimas observações disponíveis, positivas e comparáveis<br>
**Quando** solicita a comparação dos últimos preços informados<br>
**Então** recebe cada preço do item, moeda, loja, oferta, instante e proveniência em um resultado que permite comparação direta

### AC-CCO-002 — Excluir ofertas incompatíveis da comparação direta

**Dado** que uma oferta tem variante, condição, kit ou moeda incompatível<br>
**Quando** a comparação é montada<br>
**Então** o sistema não a mistura no mesmo grupo comparável e explica o estado conforme a política aprovada

### AC-CCO-003 — Identificar menor preço comparável

**Dado** que há pelo menos duas últimas observações disponíveis e comparáveis em BRL, com variante e condição conhecidas<br>
**Quando** o usuário consulta a comparação<br>
**Então** o menor valor informado entre elas é identificado sem incluir frete e acompanhado de sua data

### AC-CCO-004 — Tratar ausência ou indisponibilidade

**Dado** que a observação mais recente de uma oferta não tem preço, indica indisponibilidade ou tem disponibilidade desconhecida<br>
**Quando** a comparação é exibida<br>
**Então** seu estado aparece sem criar valor zero ou incluí-la no ranking de preço

### AC-CCO-005 — Isolar dados por conta

**Dado** que o produto/acompanhamento pertence a outra conta<br>
**Quando** o usuário solicita a comparação<br>
**Então** o acesso é recusado sem revelar preços ou a existência do recurso

### AC-CCO-006 — Identificar observações manuais

**Dado** que as observações foram inseridas manualmente<br>
**Quando** aparecem na comparação<br>
**Então** a interface informa a origem manual e não afirma que os preços foram verificados pela loja

## Regras de domínio aplicáveis

- `DR-001`, `DR-003`, `DR-004`, `DR-005`, `DR-006`, `DR-007`, `DR-012`, `DR-018`.

## Classes de domínio afetadas

| Classe/conceito | Responsabilidade/invariante | Critérios/regras |
|---|---|---|
| `Offer` (proposta) | Identidade, atributos e vínculo ao produto | `AC-CCO-001`/`AC-CCO-002`, `DR-001`, `DR-006` |
| `PriceObservation` (proposta) | Último preço, moeda, disponibilidade, instante e proveniência | `AC-CCO-001`, `AC-CCO-004`, `AC-CCO-006`, `DR-003` a `DR-005`, `DR-018` |
| `OfferComparison` (proposta) | Resultado apenas entre valores comparáveis | `AC-CCO-001`/`AC-CCO-002`/`AC-CCO-003`/`AC-CCO-004`, `DR-003`, `DR-006`, `DR-007` |

## Rastreabilidade dos testes

| Critério/regra | Caso de uso/classe | Nome(s) planejado(s) do teste/display name | Caminho no projeto de testes | Estado |
|---|---|---|---|---|
| `AC-CCO-001` a `AC-CCO-006`; `DR-001`/`DR-003`/`DR-004`/`DR-005`/`DR-006`/`DR-007`/`DR-012`/`DR-018` | Comparação dos últimos preços informados | `AC-CCO-001 — Comparar ofertas compatíveis`<br>`AC-CCO-002 — Excluir ofertas incompatíveis da comparação direta`<br>`AC-CCO-003 — Identificar menor preço comparável`<br>`AC-CCO-004 — Tratar ausência ou indisponibilidade`<br>`AC-CCO-005 — Isolar dados por conta`<br>`AC-CCO-006 — Identificar observações manuais` | `tests/Modules/PriceAnalysis.Tests/Features/CompareCurrentOffers/` | Pendente da implementação |
| `AC-CCO-001`/`AC-CCO-002`, `DR-001`/`DR-006` | `Offer` (propriedade de PriceTracking) | `AC-CCO-001 — Offer — Comparar ofertas compatíveis`<br>`AC-CCO-002 — Offer — Excluir ofertas incompatíveis da comparação direta` | `tests/Modules/PriceTracking.Tests/Domain/Offer/` | Pendente da implementação |
| `AC-CCO-001`/`AC-CCO-004`/`AC-CCO-006`, `DR-003`/`DR-004`/`DR-005`/`DR-018` | `PriceObservation` (propriedade de PriceTracking) | `AC-CCO-001 — PriceObservation — Comparar ofertas compatíveis`<br>`AC-CCO-004 — PriceObservation — Tratar ausência ou indisponibilidade`<br>`AC-CCO-006 — PriceObservation — Identificar observações manuais` | `tests/Modules/PriceTracking.Tests/Domain/PriceObservation/` | Pendente da implementação |
| `AC-CCO-001`/`AC-CCO-002`/`AC-CCO-003`/`AC-CCO-004`, `DR-003`/`DR-006`/`DR-007` | `OfferComparison` | `AC-CCO-001 — OfferComparison — Comparar ofertas compatíveis`<br>`AC-CCO-002 — OfferComparison — Excluir ofertas incompatíveis da comparação direta`<br>`AC-CCO-003 — OfferComparison — Identificar menor preço comparável`<br>`AC-CCO-004 — OfferComparison — Tratar ausência ou indisponibilidade` | `tests/Modules/PriceAnalysis.Tests/Domain/OfferComparison/` | Pendente da implementação |

## Dados e integrações

- Entrada: produto/acompanhamento e principal autenticado.
- Leitura: ofertas e última observação válida de cada uma, limitadas ao proprietário.
- Efeitos: somente leitura; nenhum acesso à Internet.
- Saída: ofertas/preços comparáveis, estados não comparáveis e metadados de proveniência; interface identifica os valores como últimos preços informados.

## Requisitos não funcionais

- Cálculo decimal; moeda explícita; sem arredondar valores persistidos.
- Autorização e filtro por conta aplicados no backend.
- Exibir a data/hora de cada observação e não alegar que um preço informado manualmente ainda está vigente na loja.

## Questões em aberto

- A proposta F0-17 não usa janela de expiração para observações manuais; o rótulo é “último preço informado”. Confirmar ou alterar.
- Confirmar se ofertas incompatíveis ou com variante/condição desconhecida permanecem fora do ranking.
- Confirmar o tratamento de empate e a exibição de ofertas sem informação suficiente.
- A proposta é manter a variação temporal por oferta, separada da comparação entre lojas; confirmar ou alterar.

## Decisões e referências

- [`docs/domain/context.md`](../../../domain/context.md)
- [`ComparePriceVariation`](../ComparePriceVariation/spec.md)
- [`docs/decisions/registro-fase-0.md`](../../../decisions/registro-fase-0.md) — F0-07, F0-08 e F0-17.
