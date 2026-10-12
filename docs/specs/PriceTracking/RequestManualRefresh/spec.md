# Especificação — RequestManualRefresh

**Estado:** Rascunho — 2026-10-12<br>
**Escopo:** Condicional — não compõe o MVP manual; exige gate e spec incremental aprovada.<br>
**Módulo:** PriceTracking<br>
**Responsável pelo domínio:** Produto e operação

## Problema e objetivo

Permitir que o usuário solicite atualização sob demanda de ofertas já confirmadas, sem refazer uma varredura ampla.

## Atores e autorização

- Ator: usuário autenticado, proprietário do acompanhamento.
- Worker executa o job; autorização de leitura do resultado permanece no backend.

## Contexto e regras de domínio

- `DR-009`: atualizações periódicas/manual usam páginas/ofertas confirmadas; não repetem busca ampla.
- `DR-010`: frequência e limites respeitam a fonte.
- `DR-011`/`DR-012`: acesso privado por conta.

## Escopo

### Incluído

- Solicitar refresh de ofertas confirmadas de um acompanhamento próprio.
- Retornar estado do job para acompanhamento posterior.

### Fora de escopo

- Crawler de descoberta geral ou seleção de novas ofertas.
- Ignorar limite, bloqueio, backoff ou pausa do domínio.

## Comportamento esperado

O backend verifica propriedade e estado do acompanhamento, valida elegibilidade e cria job persistido. Jobs equivalentes em andamento podem ser reutilizados para evitar concorrência excessiva. A API responde sem aguardar a coleta.

## Cenários de aceite

### AC-RMR-001 — Enfileirar refresh próprio

**Dado** que o usuário possui acompanhamento ativo com ofertas confirmadas<br>
**Quando** solicita atualização manual<br>
**Então** um job persistido é criado ou reutilizado e seu estado é retornado

### AC-RMR-002 — Não atualizar oferta não confirmada

**Dado** que há candidato ainda não confirmado<br>
**Quando** o refresh manual é solicitado<br>
**Então** o candidato não é atualizado como oferta acompanhada

### AC-RMR-003 — Respeitar pausa e limites

**Dado** que o acompanhamento está pausado ou a fonte está em backoff/bloqueio<br>
**Quando** o usuário solicita atualização<br>
**Então** o sistema não contorna o estado e informa que não foi enfileirado ou foi adiado

### AC-RMR-004 — Isolar job entre contas

**Dado** que o acompanhamento pertence a outra conta<br>
**Quando** o usuário solicita refresh<br>
**Então** a operação é recusada e nenhum job é criado

### AC-RMR-005 — Deduplicar jobs concorrentes

**Dado** que já há refresh equivalente pendente ou em execução<br>
**Quando** chega uma nova solicitação<br>
**Então** o sistema não cria execução duplicada desnecessária

## Regras de domínio aplicáveis

- `DR-009`, `DR-010`, `DR-011`, `DR-012`, `DR-015`, `DR-017`.

## Classes de domínio afetadas

| Classe/conceito | Responsabilidade/invariante | Critérios/regras |
|---|---|---|
| `TrackedProduct` (proposta) | Elegibilidade por estado e dono | `AC-RMR-001` a `AC-RMR-004` |
| `CollectionJob` (proposta) | Persistência e deduplicação | `AC-RMR-001`, `AC-RMR-005` |
| `Offer` (proposta) | Somente oferta confirmada recebe refresh | `AC-RMR-001`/`AC-RMR-002`, `DR-009` |

## Rastreabilidade dos testes

| Critério/regra | Caso de uso relacionado | Nome(s) planejado(s) do teste/display name | Caminho no projeto de testes | Estado |
|---|---|---|---|---|
| `AC-RMR-001` a `AC-RMR-005` | Refresh manual | `AC-RMR-001 — Enfileirar refresh próprio`<br>`AC-RMR-002 — Não atualizar oferta não confirmada`<br>`AC-RMR-003 — Respeitar pausa e limites`<br>`AC-RMR-004 — Isolar job entre contas`<br>`AC-RMR-005 — Deduplicar jobs concorrentes` | `tests/Modules/PriceTracking.Tests/Features/RequestManualRefresh/` | Pendente da implementação |
| `AC-RMR-001` a `AC-RMR-004` | `TrackedProduct` | `AC-RMR-001 — TrackedProduct — Enfileirar refresh próprio`<br>`AC-RMR-002 — TrackedProduct — Não atualizar oferta não confirmada`<br>`AC-RMR-003 — TrackedProduct — Respeitar pausa e limites`<br>`AC-RMR-004 — TrackedProduct — Isolar job entre contas` | `tests/Modules/PriceTracking.Tests/Domain/TrackedProduct/` | Pendente da implementação; futura |
| `AC-RMR-001`/`AC-RMR-005` | `CollectionJob` (propriedade de WebDiscovery) | `AC-RMR-001 — CollectionJob — Enfileirar refresh próprio`<br>`AC-RMR-005 — CollectionJob — Deduplicar jobs concorrentes` | `tests/Modules/WebDiscovery.Tests/Domain/CollectionJob/` | Pendente da implementação; futura |
| `AC-RMR-001`/`AC-RMR-002`, `DR-009` | `Offer` | `AC-RMR-001 — Offer — Enfileirar refresh próprio`<br>`AC-RMR-002 — Offer — Não atualizar oferta não confirmada` | `tests/Modules/PriceTracking.Tests/Domain/Offer/` | Pendente da implementação; futura |

## Dados e integrações

- Entrada: acompanhamento e ofertas confirmadas pertencentes ao usuário.
- Dados criados: job, chave de idempotência e estado.
- Efeitos: leitura HTTP somente nas fontes elegíveis atualmente aprovadas.
- Retentativas: controladas por conector, com limite e backoff.

## Requisitos não funcionais

- Job durável, idempotente e observável após reinício.
- Timeout e concorrência por fonte; falha isolada.
- Nenhum endpoint autenticado por cookie altera estado sem antifalsificação.

## Questões em aberto

- Refresh é permitido para acompanhamento pausado mediante ação explícita?
- Qual intervalo mínimo entre refreshes manuais?
- Uma solicitação agrupa todas as ofertas ou permite selecionar oferta?

## Decisões e referências

- [`docs/domain/context.md`](../../../domain/context.md)
