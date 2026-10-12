# Especificação — PauseOrResumeTracking

**Estado:** Rascunho — 2026-10-12<br>
**Escopo:** Condicional — não compõe o MVP manual; exige gate e spec incremental aprovada.<br>
**Módulo:** PriceTracking<br>
**Responsável pelo domínio:** Produto

## Problema e objetivo

Dar controle ao usuário para interromper e retomar atualizações automáticas de um acompanhamento sem perder seu histórico.

## Atores e autorização

- Ator: usuário autenticado, proprietário do acompanhamento.
- O backend valida a propriedade em cada comando.

## Contexto e regras de domínio

- `DR-011`/`DR-012`: acompanhamento pertence a uma conta e seus dados são privados.
- Pausar não apaga histórico; remoção é outro caso de uso.
- `DR-010`: frequência alvo respeita limites da fonte.

## Escopo

### Incluído

- Pausar e reativar um acompanhamento existente.
- Controlar futuros jobs periódicos associados.

### Fora de escopo

- Apagar acompanhamento ou histórico.
- Alterar cotas/intervalo por domínio.

## Comportamento esperado

Pausar impede criação de novos jobs periódicos para o acompanhamento. Jobs já iniciados podem terminar conforme política operacional, que deve ser definida. Reativar permite novo agendamento conforme frequência e limites configurados. Transições repetidas para o estado atual não duplicam jobs.

## Cenários de aceite

### AC-PRT-001 — Pausar acompanhamento próprio

**Dado** que o acompanhamento está ativo e pertence ao usuário<br>
**Quando** o usuário solicita pausa<br>
**Então** seu estado passa a pausado e não são criados novos jobs periódicos

### AC-PRT-002 — Retomar acompanhamento próprio

**Dado** que o acompanhamento está pausado e pertence ao usuário<br>
**Quando** o usuário solicita retomada<br>
**Então** fica ativo e volta a ser elegível a agendamento respeitando as cotas

### AC-PRT-003 — Preservar histórico

**Dado** que há observações de preço<br>
**Quando** o acompanhamento é pausado ou retomado<br>
**Então** as observações existentes permanecem intactas

### AC-PRT-004 — Recusar alteração de recurso alheio

**Dado** que o acompanhamento pertence a outra conta<br>
**Quando** o usuário tenta pausar ou retomar<br>
**Então** a operação é recusada e o estado não muda

### AC-PRT-005 — Transição idempotente

**Dado** que o acompanhamento já está no estado solicitado<br>
**Quando** o mesmo comando é repetido<br>
**Então** não são criados jobs duplicados nem alterado o histórico

## Regras de domínio aplicáveis

- `DR-010`, `DR-011`, `DR-012`.

## Classes de domínio afetadas

| Classe/conceito | Responsabilidade/invariante | Critérios/regras |
|---|---|---|
| `TrackedProduct` (proposta) | Estado ativo/pausado e proprietário | `AC-PRT-001` a `AC-PRT-005` |
| `CollectionJob` (proposta) | Agendamento sem duplicidade | `AC-PRT-001`, `AC-PRT-002`, `AC-PRT-005` |

## Rastreabilidade dos testes

| Critério/regra | Caso de uso relacionado | Nome(s) planejado(s) do teste/display name | Caminho no projeto de testes | Estado |
|---|---|---|---|---|
| `AC-PRT-001` a `AC-PRT-005` | Pausa/retomada | `AC-PRT-001 — Pausar acompanhamento próprio`<br>`AC-PRT-002 — Retomar acompanhamento próprio`<br>`AC-PRT-003 — Preservar histórico`<br>`AC-PRT-004 — Recusar alteração de recurso alheio`<br>`AC-PRT-005 — Transição idempotente` | `tests/Modules/PriceTracking.Tests/Features/PauseOrResumeTracking/` | Pendente da implementação |
| `AC-PRT-001` a `AC-PRT-005` | `TrackedProduct` | `AC-PRT-001 — TrackedProduct — Pausar acompanhamento próprio`<br>`AC-PRT-002 — TrackedProduct — Retomar acompanhamento próprio`<br>`AC-PRT-003 — TrackedProduct — Preservar histórico`<br>`AC-PRT-004 — TrackedProduct — Recusar alteração de recurso alheio`<br>`AC-PRT-005 — TrackedProduct — Transição idempotente` | `tests/Modules/PriceTracking.Tests/Domain/TrackedProduct/` | Pendente da implementação; futura |
| `AC-PRT-001`/`AC-PRT-002`/`AC-PRT-005` | `CollectionJob` (propriedade de WebDiscovery) | `AC-PRT-001 — CollectionJob — Pausar acompanhamento próprio`<br>`AC-PRT-002 — CollectionJob — Retomar acompanhamento próprio`<br>`AC-PRT-005 — CollectionJob — Transição idempotente` | `tests/Modules/WebDiscovery.Tests/Domain/CollectionJob/` | Pendente da implementação; futura |

## Dados e integrações

- Entrada: acompanhamento alvo e ação pause/resume.
- Dados alterados: estado e instante da transição.
- Efeitos: atualizar elegibilidade do agendador.
- Idempotência: comando repetido para estado atual é seguro.

## Requisitos não funcionais

- Mudança de estado e criação/cancelamento de jobs devem ser transacionais ou recuperáveis.
- Verificar proprietário no backend e antifalsificação para chamadas por cookie.

## Questões em aberto

- Jobs em andamento são cancelados, terminam ou recebem flag de cancelamento?
- Reativar agenda imediatamente ou aguarda próxima janela?
- A pausa é por produto acompanhado ou por cada oferta?

## Decisões e referências

- [`docs/domain/context.md`](../../../domain/context.md)
