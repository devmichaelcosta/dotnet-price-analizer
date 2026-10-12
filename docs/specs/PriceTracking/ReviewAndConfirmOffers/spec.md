# Especificação — ReviewAndConfirmOffers

**Estado:** Rascunho — 2026-10-12<br>
**Escopo:** Condicional — não compõe o MVP manual; exige gate e spec incremental aprovada.<br>
**Módulo:** PriceTracking<br>
**Responsável pelo domínio:** Produto

> Esta spec trata somente de candidatos retornados por um conector HTML aprovado em uma etapa futura. O MVP manual cadastra ofertas em [`AddManualOffer`](../AddManualOffer/spec.md), sem descoberta externa. Não usar APIs, feeds ou agregadores.

## Problema e objetivo

Permitir que a pessoa selecione quais anúncios encontrados correspondem ao produto e variante que quer acompanhar.

## Atores e autorização

- Ator: usuário autenticado, proprietário do acompanhamento.
- O backend verifica propriedade do acompanhamento e dos candidatos em cada operação.

## Contexto e regras de domínio

- `DR-001`: candidato, produto, página, oferta e observação são conceitos separados.
- `DR-006`: não misturar variante, kit, condição ou vendedor incompatível; título parecido sozinho não garante correspondência.
- `DR-011`/`DR-012`: dados privados por conta.

## Escopo

### Incluído

- Mostrar dados observados e permitir confirmar ou rejeitar candidatos.
- Associar somente ofertas confirmadas ao acompanhamento.

### Fora de escopo

- Confirmação automática de candidatos ambíguos.
- Editar diretamente preço coletado ou fazer pedido de compra.

## Comportamento esperado

O usuário revisa nome, identificador, marca/modelo, variante/condição, vendedor, preço, disponibilidade, fonte e horário disponíveis. Ao confirmar, a oferta passa a ser acompanhada. Rejeitar um candidato impede sua inclusão. A ação é persistida e pode ser repetida com segurança.

## Cenários de aceite

### AC-RCO-001 — Confirmar candidato da própria conta

**Dado** que o candidato pertence ao acompanhamento do usuário autenticado<br>
**Quando** o usuário confirma que corresponde ao produto<br>
**Então** a oferta é associada ao acompanhamento e fica apta a receber observações

### AC-RCO-002 — Rejeitar candidato

**Dado** que o usuário identifica um candidato como incompatível<br>
**Quando** o rejeita<br>
**Então** ele não é associado nem comparado como oferta daquele produto

### AC-RCO-003 — Preservar variante e vendedor

**Dado** que candidatos têm variantes, condições, kits ou vendedores diferentes<br>
**Quando** o usuário revisa e confirma<br>
**Então** cada anúncio permanece separado e somente os confirmados são acompanhados

### AC-RCO-004 — Recusar acesso entre contas

**Dado** que o candidato pertence a outra conta<br>
**Quando** o usuário tenta confirmar ou rejeitar<br>
**Então** a operação é recusada e o estado não muda

### AC-RCO-005 — Evitar confirmação duplicada

**Dado** que a oferta já está confirmada no acompanhamento<br>
**Quando** o mesmo pedido é repetido<br>
**Então** não são criadas associações nem jobs duplicados

## Regras de domínio aplicáveis

- `DR-001`, `DR-006`, `DR-011`, `DR-012`.

## Classes de domínio afetadas

| Classe/conceito | Responsabilidade/invariante | Critérios/regras |
|---|---|---|
| `ProductCandidate` (contrato de WebDiscovery) | Candidato pode ser confirmado/rejeitado pelo dono | `AC-RCO-001` a `AC-RCO-005` |
| `Offer` (PriceTracking) | Oferta confirmada mantém identidade do anúncio/vendedor | `AC-RCO-001` a `AC-RCO-003`, `DR-001`, `DR-006` |
| `Product` (ProductCatalog) | Produto canônico é resolvido sem fundir anúncio ou variante incompatível | `AC-RCO-001`, `AC-RCO-003`, `DR-001`, `DR-006` |
| `TrackedProduct` (proposta) | Mantém associações privadas | `AC-RCO-001`, `AC-RCO-004`, `AC-RCO-005`, `DR-011`, `DR-012` |

## Rastreabilidade dos testes

| Critério/regra | Caso de uso relacionado | Nome(s) planejado(s) do teste/display name | Caminho no projeto de testes | Estado |
|---|---|---|---|---|
| `AC-RCO-001` a `AC-RCO-005` | Revisão e confirmação | `AC-RCO-001 — Confirmar candidato da própria conta`<br>`AC-RCO-002 — Rejeitar candidato`<br>`AC-RCO-003 — Preservar variante e vendedor`<br>`AC-RCO-004 — Recusar acesso entre contas`<br>`AC-RCO-005 — Evitar confirmação duplicada` | `tests/Modules/PriceTracking.Tests/Features/ReviewAndConfirmOffers/` | Pendente da implementação |
| `AC-RCO-001` a `AC-RCO-005` | `ProductCandidate` (contrato de WebDiscovery) | `AC-RCO-001 — ProductCandidate — Confirmar candidato da própria conta`<br>`AC-RCO-002 — ProductCandidate — Rejeitar candidato`<br>`AC-RCO-003 — ProductCandidate — Preservar variante e vendedor`<br>`AC-RCO-004 — ProductCandidate — Recusar acesso entre contas`<br>`AC-RCO-005 — ProductCandidate — Evitar confirmação duplicada` | `tests/Modules/WebDiscovery.Tests/Domain/ProductCandidate/` | Pendente de fonte elegível/spec aprovada |
| `AC-RCO-001` a `AC-RCO-003`, `DR-001`/`DR-006` | `Offer` | `AC-RCO-001 — Offer — Confirmar candidato da própria conta`<br>`AC-RCO-002 — Offer — Rejeitar candidato`<br>`AC-RCO-003 — Offer — Preservar variante e vendedor` | `tests/Modules/PriceTracking.Tests/Domain/Offer/` | Pendente da implementação |
| `AC-RCO-001`/`AC-RCO-003`, `DR-001`/`DR-006` | `Product` | `AC-RCO-001 — Product — Confirmar candidato da própria conta`<br>`AC-RCO-003 — Product — Preservar variante e vendedor` | `tests/Modules/ProductCatalog.Tests/Domain/Product/` | Pendente da implementação |
| `AC-RCO-001`/`AC-RCO-004`/`AC-RCO-005`, `DR-011`/`DR-012` | `TrackedProduct` | `AC-RCO-001 — TrackedProduct — Confirmar candidato da própria conta`<br>`AC-RCO-004 — TrackedProduct — Recusar acesso entre contas`<br>`AC-RCO-005 — TrackedProduct — Evitar confirmação duplicada` | `tests/Modules/PriceTracking.Tests/Domain/TrackedProduct/` | Pendente da implementação |

## Dados e integrações

- Entradas: IDs do acompanhamento/candidato e decisão explícita.
- Dados alterados: associação, status de revisão e timestamp de confirmação.
- Efeitos: chama `ProductCatalog.ResolveCanonicalProduct` pelo contrato do módulo e associa uma oferta no PriceTracking; pode enfileirar a primeira leitura conforme fluxo decidido.
- Idempotência: confirmação repetida não duplica associação.

## Requisitos não funcionais

- Autorização por recurso no backend e antifalsificação na sessão por cookie.
- Exibir fonte, URL e instante da observação para apoiar decisão informada.

## Questões em aberto

- Confirmação inicia imediatamente a primeira coleta ou aguarda próxima atualização?
- Usuário pode corrigir metadados de variante sem alterar o anúncio da fonte?
- Ao rejeitar, candidato fica oculto ou pode ser reaberto?

## Decisões e referências

- [`docs/domain/context.md`](../../../domain/context.md)
- [`ResolveCanonicalProduct`](../../ProductCatalog/ResolveCanonicalProduct/spec.md)
