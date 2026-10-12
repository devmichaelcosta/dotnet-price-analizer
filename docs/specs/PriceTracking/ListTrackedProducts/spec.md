# Especificação — ListTrackedProducts

**Estado:** Rascunho — 2026-10-12<br>
**Escopo:** MVP manual<br>
**Módulo:** PriceTracking<br>
**Responsável pelo domínio:** Produto

## Problema e objetivo

Mostrar ao usuário seus acompanhamentos e estado atual sem revelar dados de outras contas.

## Atores e autorização

- Ator: usuário autenticado.
- Identidade e propriedade são determinadas no backend pelo principal autenticado.

## Contexto e regras de domínio

- `DR-011` e `DR-012`: acompanhamento/histórico privados por conta.
- A lista resume ofertas e observações manuais; estado de job não existe no MVP manual.

## Escopo

### Incluído

- Listar acompanhamentos próprios com dados resumidos e estado.

### Fora de escopo

- Listagem administrativa de todas as contas.
- Histórico completo de preço (caso de uso próprio).

## Comportamento esperado

O sistema retorna somente acompanhamentos do usuário autenticado, ordenados por critério a definir, com nome/descrição, identificador opcional, quantidade de ofertas manuais e resumo de preço/data quando disponível. O resumo não descreve preço informado manualmente como verificado pela loja.

## Cenários de aceite

### AC-LTP-001 — Listar somente dados próprios

**Dado** que há acompanhamentos de várias contas<br>
**Quando** um usuário autenticado lista os seus<br>
**Então** recebe somente registros associados à própria identidade

### AC-LTP-002 — Conta sem acompanhamentos

**Dado** que o usuário autenticado ainda não acompanha produtos<br>
**Quando** abre a lista<br>
**Então** recebe uma lista vazia válida, sem erro

### AC-LTP-003 — Exigir autenticação

**Dado** que a requisição não está autenticada<br>
**Quando** solicita a lista<br>
**Então** não recebe dados privados

### AC-LTP-004 — Mostrar resumo manual útil

**Dado** que um acompanhamento possui ofertas e observações manuais<br>
**Quando** aparece na lista<br>
**Então** a quantidade e o resumo de preço/data são identificáveis como dados informados pelo usuário

## Regras de domínio aplicáveis

- `DR-011`, `DR-012`.

## Classes de domínio afetadas

| Classe/conceito | Responsabilidade/invariante | Critérios/regras |
|---|---|---|
| `TrackedProduct` (proposta) | Propriedade e estado resumido do acompanhamento | `AC-LTP-001` a `AC-LTP-004`, `DR-011`, `DR-012` |

## Rastreabilidade dos testes

| Critério/regra | Caso de uso relacionado | Nome(s) planejado(s) do teste/display name | Caminho no projeto de testes | Estado |
|---|---|---|---|---|
| `AC-LTP-001` a `AC-LTP-004` | Listagem própria | `AC-LTP-001 — Listar somente dados próprios`<br>`AC-LTP-002 — Conta sem acompanhamentos`<br>`AC-LTP-003 — Exigir autenticação`<br>`AC-LTP-004 — Mostrar resumo manual útil` | `tests/Modules/PriceTracking.Tests/Features/ListTrackedProducts/` | Pendente da implementação |
| `AC-LTP-001` a `AC-LTP-004`, `DR-011`/`DR-012` | `TrackedProduct` | `AC-LTP-001 — TrackedProduct — Listar somente dados próprios`<br>`AC-LTP-002 — TrackedProduct — Conta sem acompanhamentos`<br>`AC-LTP-003 — TrackedProduct — Exigir autenticação`<br>`AC-LTP-004 — TrackedProduct — Mostrar resumo manual útil` | `tests/Modules/PriceTracking.Tests/Domain/TrackedProduct/` | Pendente da implementação |

## Dados e integrações

- Entrada: principal autenticado; filtros/paginação ainda não definidos.
- Dados lidos: acompanhamentos, ofertas manuais e resumo das observações mais recentes.
- Efeitos: somente leitura.
- Idempotência: leitura sem efeitos.

## Requisitos não funcionais

- Filtrar por proprietário no backend antes de materializar resposta.
- Paginação pode ser adicionada conforme o limite de acompanhamentos definido.

## Questões em aberto

- Ordenação e campos da linha/cartão resumido.
- Limite máximo de acompanhamentos por usuário e necessidade de paginação.
- Como resumir ofertas sem preço ou com disponibilidade desconhecida?
- Qual regra de atualidade/ordenação deve ser aplicada ao menor preço resumido (F0-17)?

## Decisões e referências

- [`docs/domain/context.md`](../../../domain/context.md)
