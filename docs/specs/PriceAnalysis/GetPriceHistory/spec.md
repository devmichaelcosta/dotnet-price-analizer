# Especificação — GetPriceHistory

**Estado:** Rascunho — 2026-10-12<br>
**Escopo:** MVP manual<br>
**Módulo:** PriceAnalysis<br>
**Responsável pelo domínio:** Produto

## Problema e objetivo

Exibir a evolução observada do preço de uma oferta ao longo do tempo para responder se houve mudança.

## Atores e autorização

- Ator: usuário autenticado, proprietário do acompanhamento/oferta.
- Toda leitura valida propriedade no backend.

## Contexto e regras de domínio

- `DR-002`/`DR-004`/`DR-018`: histórico datado, instante UTC e origem manual identificada.
- `DR-003`/`DR-005`: decimal/moeda; indisponibilidade não é zero.
- `DR-012`/`DR-013`: privacidade por conta e retenção vinculada ao acompanhamento.

## Escopo

### Incluído

- Consultar observações de uma oferta para intervalo selecionado.
- Retornar preço, moeda, disponibilidade, proveniência manual e instante de cada ponto.

### Fora de escopo

- Histórico de oferta de outra conta.
- Preencher lacunas por estimativa, converter moeda ou adicionar frete.

## Comportamento esperado

O usuário seleciona oferta e período. O sistema retorna observações válidas em ordem temporal, preservando estados de indisponibilidade sem preço. Para intervalo sem observações, retorna conjunto vazio e estado informativo.

## Cenários de aceite

### AC-GPH-001 — Consultar histórico próprio

**Dado** que existem observações da oferta pertencente ao usuário<br>
**Quando** consulta um intervalo válido<br>
**Então** recebe observações em ordem cronológica com valor, moeda, autoria/proveniência manual e instante UTC

### AC-GPH-002 — Recusar histórico de outra conta

**Dado** que a oferta pertence a outra conta<br>
**Quando** o usuário solicita seu histórico<br>
**Então** o acesso é recusado sem revelar a série

### AC-GPH-003 — Retornar intervalo sem dados

**Dado** que não há observações no intervalo<br>
**Quando** a consulta termina<br>
**Então** retorna coleção vazia sem inventar valores

### AC-GPH-004 — Representar indisponibilidade

**Dado** que uma observação registra indisponibilidade<br>
**Quando** o histórico é retornado<br>
**Então** disponibilidade aparece separada de preço, sem zero fictício

### AC-GPH-005 — Validar intervalo

**Dado** que a data inicial é posterior à final ou o intervalo viola limite definido<br>
**Quando** a consulta é feita<br>
**Então** a entrada é rejeitada com erro de validação

## Regras de domínio aplicáveis

- `DR-002`, `DR-003`, `DR-004`, `DR-005`, `DR-012`, `DR-013`, `DR-018`.

## Classes de domínio afetadas

| Classe/conceito | Responsabilidade/invariante | Critérios/regras |
|---|---|---|
| `PriceObservation` (proposta) | Ponto imutável da série com origem/autoria | `AC-GPH-001`, `AC-GPH-003`, `AC-GPH-004`, `DR-002` a `DR-005`, `DR-018` |
| `Offer` (proposta) | Vincular a série à oferta correta | `AC-GPH-001`/`AC-GPH-002`, `DR-001`, `DR-012` |
| `TrackedProduct` (proposta) | Garantir propriedade da conta | `AC-GPH-001`/`AC-GPH-002`, `DR-011`, `DR-012` |

## Rastreabilidade dos testes

| Critério/regra | Caso de uso relacionado | Nome(s) planejado(s) do teste/display name | Caminho no projeto de testes | Estado |
|---|---|---|---|---|
| `AC-GPH-001` a `AC-GPH-005` | Consulta de histórico | `AC-GPH-001 — Consultar histórico próprio`<br>`AC-GPH-002 — Recusar histórico de outra conta`<br>`AC-GPH-003 — Retornar intervalo sem dados`<br>`AC-GPH-004 — Representar indisponibilidade`<br>`AC-GPH-005 — Validar intervalo` | `tests/Modules/PriceAnalysis.Tests/Features/GetPriceHistory/` | Pendente da implementação |
| `AC-GPH-001`/`AC-GPH-003`/`AC-GPH-004`, `DR-002` a `DR-005`, `DR-018` | `PriceObservation` (propriedade de PriceTracking) | `AC-GPH-001 — PriceObservation — Consultar histórico próprio`<br>`AC-GPH-003 — PriceObservation — Retornar intervalo sem dados`<br>`AC-GPH-004 — PriceObservation — Representar indisponibilidade` | `tests/Modules/PriceTracking.Tests/Domain/PriceObservation/` | Pendente da implementação |
| `AC-GPH-001`/`AC-GPH-002`, `DR-001`/`DR-011`/`DR-012` | `Offer` e `TrackedProduct` (propriedade de PriceTracking) | `AC-GPH-001 — Offer — Consultar histórico próprio`<br>`AC-GPH-001 — TrackedProduct — Consultar histórico próprio`<br>`AC-GPH-002 — Offer — Recusar histórico de outra conta`<br>`AC-GPH-002 — TrackedProduct — Recusar histórico de outra conta` | `tests/Modules/PriceTracking.Tests/Domain/Offer/` e `tests/Modules/PriceTracking.Tests/Domain/TrackedProduct/` | Pendente da implementação |

## Dados e integrações

- Entrada: oferta e intervalo de instantes.
- Dados lidos: observações da oferta, limitadas ao proprietário.
- Efeitos: somente leitura.
- Consistência: ordem estável por instante e identificador em caso de empate.

## Requisitos não funcionais

- Paginar ou limitar séries extensas conforme volume estimado.
- Não expor identificadores privados de outras contas.
- Instantes são persistidos em UTC e apresentados no fuso horário do usuário.

## Questões em aberto

- Períodos predefinidos e limite máximo do intervalo.
- O gráfico exibe lacunas sem interpolação ou agrega por dia?

## Decisões e referências

- [`docs/domain/context.md`](../../../domain/context.md)
- [`docs/decisions/registro-fase-0.md`](../../../decisions/registro-fase-0.md) — F0-16.
