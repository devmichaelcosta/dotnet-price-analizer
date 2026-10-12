# Especificação — RemoveTracking

**Estado:** Rascunho — 2026-10-12<br>
**Escopo:** MVP manual<br>
**Módulo:** PriceTracking<br>
**Responsável pelo domínio:** Produto e privacidade

## Problema e objetivo

Permitir que a conta proprietária remova um acompanhamento e apague suas observações/históricos associados conforme retenção aprovada.

## Atores e autorização

- Ator: usuário autenticado, proprietário do acompanhamento.
- O backend confirma propriedade; identidade enviada pelo cliente não é prova.

## Contexto e regras de domínio

- `DR-013`: observações e dados privados do acompanhamento são apagados junto com ele.
- `DR-012`: dados privados por conta local.

## Escopo

### Incluído

- Remover acompanhamento, ofertas manuais e observações privadas associadas.

### Fora de escopo

- Apagar conta de usuário (caso de uso separado).
- Remover registros globais de páginas compartilhadas sem decisão de retenção correspondente.

## Comportamento esperado

O usuário confirma a remoção. O sistema remove o acompanhamento e seus dados privados de produto, ofertas e preços. O MVP manual não tem jobs de coleta. A operação deve ser consistente. Se uma fonte HTML for aprovada futuramente, seus requisitos de retenção serão tratados antes da habilitação.

## Cenários de aceite

### AC-DTR-001 — Remover acompanhamento próprio e dados associados

**Dado** que o acompanhamento pertence ao usuário<br>
**Quando** o usuário confirma a remoção<br>
**Então** acompanhamento, observações e dados privados associados deixam de estar disponíveis

### AC-DTR-002 — Remover ofertas e observações privadas

**Dado** que o acompanhamento possui ofertas manuais e observações<br>
**Quando** a remoção é confirmada<br>
**Então** as ofertas e observações privadas vinculadas são removidas

### AC-DTR-003 — Recusar remoção alheia

**Dado** que o recurso pertence a outra conta<br>
**Quando** o usuário solicita sua remoção<br>
**Então** a operação é recusada sem alterar dados

### AC-DTR-004 — Tratar remoção repetida com segurança

**Dado** que o acompanhamento já foi removido<br>
**Quando** a solicitação é repetida<br>
**Então** nenhum dado é recriado e a resposta segue a política de recurso inexistente

## Regras de domínio aplicáveis

- `DR-012`, `DR-013`.

## Classes de domínio afetadas

| Classe/conceito | Responsabilidade/invariante | Critérios/regras |
|---|---|---|
| `TrackedProduct` (proposta) | Propriedade e ciclo de vida do agregado de acompanhamento | `AC-DTR-001`/`AC-DTR-004`, `DR-011`/`DR-012`/`DR-013` |
| `PriceObservation` (proposta) | Retenção vinculada ao acompanhamento | `AC-DTR-001`, `DR-013` |
| `Offer` (proposta) | Vinculação privada da oferta ao acompanhamento removido | `AC-DTR-001`, `DR-013` |

## Rastreabilidade dos testes

| Critério/regra | Caso de uso relacionado | Nome(s) planejado(s) do teste/display name | Caminho no projeto de testes | Estado |
|---|---|---|---|---|
| `AC-DTR-001` a `AC-DTR-004` | Remoção de acompanhamento | `AC-DTR-001 — Remover acompanhamento próprio e dados associados`<br>`AC-DTR-002 — Remover ofertas e observações privadas`<br>`AC-DTR-003 — Recusar remoção alheia`<br>`AC-DTR-004 — Tratar remoção repetida com segurança` | `tests/Modules/PriceTracking.Tests/Features/RemoveTracking/` | Pendente da implementação |
| `AC-DTR-001`/`AC-DTR-004`, `DR-011`/`DR-012`/`DR-013` | `TrackedProduct` | `AC-DTR-001 — TrackedProduct — Remover acompanhamento próprio e dados associados`<br>`AC-DTR-004 — TrackedProduct — Tratar remoção repetida com segurança` | `tests/Modules/PriceTracking.Tests/Domain/TrackedProduct/` | Pendente da implementação |
| `AC-DTR-001`, `DR-013` | `Offer` | `AC-DTR-001 — Offer — Remover acompanhamento próprio e dados associados` | `tests/Modules/PriceTracking.Tests/Domain/Offer/` | Pendente da implementação |
| `AC-DTR-001`, `DR-013` | `PriceObservation` | `AC-DTR-001 — PriceObservation — Remover acompanhamento próprio e dados associados` | `tests/Modules/PriceTracking.Tests/Domain/PriceObservation/` | Pendente da implementação |

## Dados e integrações

- Entrada: identificador do acompanhamento e confirmação explícita.
- Dados removidos: associação privada, produto/ofertas manuais e observações conforme política aprovada.
- Efeitos: nenhum externo no MVP manual.
- Idempotência: repetição não recria conteúdo.

## Requisitos não funcionais

- Exclusão consistente, transacional onde possível e retomável após falha.
- Nenhuma operação futura pode recriar silenciosamente os dados privados removidos.
- Aplicar antifalsificação e autorização por propriedade.

## Questões em aberto

- Existe período de lixeira/desfazer ou remoção imediata?
- Páginas e metadados globais do índice permanecem após apagar o acompanhamento?
- Como demonstrar remoção após falha parcial da persistência?

## Decisões e referências

- [`docs/domain/context.md`](../../../domain/context.md)
