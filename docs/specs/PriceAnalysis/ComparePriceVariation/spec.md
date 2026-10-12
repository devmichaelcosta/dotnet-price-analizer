# Especificação — ComparePriceVariation

**Estado:** Rascunho — 2026-10-12<br>
**Escopo:** MVP manual<br>
**Módulo:** PriceAnalysis<br>
**Responsável pelo domínio:** Produto

## Problema e objetivo

Calcular se o preço observado de uma oferta subiu, caiu ou permaneceu igual em relação a uma referência temporal.

## Atores e autorização

- Ator: usuário autenticado, proprietário da oferta/acompanhamento.
- O backend valida propriedade antes de consultar observações.

## Contexto e regras de domínio

- `DR-002`/`DR-003`/`DR-004`: observações imutáveis, valor decimal/moeda e timestamp UTC.
- `DR-006`: só comparar ofertas/variantes equivalentes.
- `DR-007`: considerar apenas preço do item, sem frete.
- `DR-012`/`DR-018`: dados privados por conta e origem manual identificada.

## Escopo

### Incluído

- Comparar observações selecionadas ou os extremos válidos do intervalo escolhido, conforme a regra final.
- Calcular diferença absoluta e percentual quando matematicamente definido.

### Fora de escopo

- Comparar moedas ou converter cotação.
- Comparar anúncios distintos como se fossem a mesma oferta.
- Incorporar frete, cashback ou preço parcelado não aprovado como medida principal.

## Comportamento esperado

Para uma mesma oferta e moeda, o sistema seleciona duas observações manuais válidas e calcula `diferença = preço final - preço inicial`. Sinal positivo significa aumento; negativo, redução; zero, estabilidade. Percentual é calculado somente quando o preço inicial é positivo. O resultado identifica a origem manual. Se os dados não forem comparáveis, o sistema retorna motivo sem fabricar resultado.

## Cenários de aceite

### AC-CPV-001 — Identificar aumento

**Dado** que duas observações válidas da mesma oferta têm moeda igual e o segundo preço é maior<br>
**Quando** a variação é calculada<br>
**Então** a diferença absoluta e percentual indicam aumento

### AC-CPV-002 — Identificar redução

**Dado** que o segundo preço é menor que o primeiro<br>
**Quando** a variação é calculada<br>
**Então** a diferença absoluta e percentual indicam redução

### AC-CPV-003 — Identificar estabilidade

**Dado** que preços comparáveis são iguais<br>
**Quando** a variação é calculada<br>
**Então** a diferença é zero e o estado indica estabilidade

### AC-CPV-004 — Recusar observações incompatíveis

**Dado** que moeda, oferta, variante ou condição não são comparáveis<br>
**Quando** a variação é solicitada<br>
**Então** o sistema não calcula percentual e informa que a comparação não é válida

### AC-CPV-005 — Tratar ausência de preço-base válido

**Dado** que preço inicial é zero, ausente ou indisponível<br>
**Quando** o usuário solicita variação<br>
**Então** a diferença percentual não é calculada e o motivo é informado

### AC-CPV-006 — Isolar histórico por conta

**Dado** que a oferta pertence a outra conta<br>
**Quando** o usuário solicita comparação<br>
**Então** o acesso é recusado sem revelar os valores

### AC-CPV-007 — Identificar observações manuais

**Dado** que as observações comparadas foram inseridas manualmente<br>
**Quando** o resultado da variação é apresentado<br>
**Então** a proveniência é identificada como informada pelo usuário e não como verificada pela loja

## Regras de domínio aplicáveis

- `DR-002`, `DR-003`, `DR-004`, `DR-005`, `DR-006`, `DR-007`, `DR-012`, `DR-018`.

## Classes de domínio afetadas

| Classe/conceito | Responsabilidade/invariante | Critérios/regras |
|---|---|---|
| `PriceObservation` (proposta) | Fornecer amostras válidas, comparáveis e com origem identificada | `AC-CPV-001` a `AC-CPV-005`, `DR-018` |
| `PriceVariation` (proposta) | Resultado com diferença, percentual, direção e intervalo | `AC-CPV-001` a `AC-CPV-005`, `DR-003`, `DR-007` |
| `Offer` (proposta) | Garantir mesma identidade de anúncio/variante | `AC-CPV-004`, `DR-006` |

## Rastreabilidade dos testes

| Critério/regra | Caso de uso relacionado | Nome(s) planejado(s) do teste/display name | Caminho no projeto de testes | Estado |
|---|---|---|---|---|
| `AC-CPV-001` a `AC-CPV-007`; `DR-012`/`DR-018` | Comparação de variação | `AC-CPV-001 — Identificar aumento`<br>`AC-CPV-002 — Identificar redução`<br>`AC-CPV-003 — Identificar estabilidade`<br>`AC-CPV-004 — Recusar observações incompatíveis`<br>`AC-CPV-005 — Tratar ausência de preço-base válido`<br>`AC-CPV-006 — Isolar histórico por conta`<br>`AC-CPV-007 — Identificar observações manuais` | `tests/Modules/PriceAnalysis.Tests/Features/ComparePriceVariation/` | Pendente da implementação |
| `AC-CPV-001` a `AC-CPV-005`, `DR-003`/`DR-007` | `PriceVariation` | `AC-CPV-001 — PriceVariation — Identificar aumento`<br>`AC-CPV-002 — PriceVariation — Identificar redução`<br>`AC-CPV-003 — PriceVariation — Identificar estabilidade`<br>`AC-CPV-004 — PriceVariation — Recusar observações incompatíveis`<br>`AC-CPV-005 — PriceVariation — Tratar ausência de preço-base válido` | `tests/Modules/PriceAnalysis.Tests/Domain/PriceVariation/` | Pendente da implementação |
| `AC-CPV-001` a `AC-CPV-005`/`AC-CPV-007`, `DR-002`/`DR-003`/`DR-004`/`DR-005`/`DR-018` | `PriceObservation` (propriedade de PriceTracking) | `AC-CPV-001 — PriceObservation — Identificar aumento`<br>`AC-CPV-002 — PriceObservation — Identificar redução`<br>`AC-CPV-003 — PriceObservation — Identificar estabilidade`<br>`AC-CPV-004 — PriceObservation — Recusar observações incompatíveis`<br>`AC-CPV-005 — PriceObservation — Tratar ausência de preço-base válido`<br>`AC-CPV-007 — PriceObservation — Identificar observações manuais` | `tests/Modules/PriceTracking.Tests/Domain/PriceObservation/` | Pendente da implementação |
| `AC-CPV-004`, `DR-006` | `Offer` (propriedade de PriceTracking) | `AC-CPV-004 — Offer — Recusar observações incompatíveis` | `tests/Modules/PriceTracking.Tests/Domain/Offer/` | Pendente da implementação |

## Dados e integrações

- Entrada: oferta, observações ou intervalo e estratégia de seleção.
- Saída: preços de referência, diferença absoluta, percentual opcional, direção e motivo de não comparabilidade.
- Efeitos: somente leitura; cálculo decimal com moeda única.
- Idempotência: cálculo puro para os mesmos dados.

## Requisitos não funcionais

- Precisão decimal e regra de arredondamento visível/definida antes da UI.
- Nunca comparar fontes como se fossem a mesma oferta sem confirmação do usuário.

## Questões em aberto

- Comparar a primeira e última observação do período ou permitir escolher duas datas?
- Qual escala de arredondamento percentual e quantas casas decimais?
- Como selecionar ponto quando há várias observações no mesmo dia?

## Decisões e referências

- [`docs/domain/context.md`](../../../domain/context.md)
- [`docs/decisions/registro-fase-0.md`](../../../decisions/registro-fase-0.md) — F0-08 e F0-16.
