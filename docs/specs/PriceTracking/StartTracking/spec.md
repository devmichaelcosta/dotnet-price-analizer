# Especificação — StartTracking

**Estado:** Rascunho — 2026-10-12<br>
**Escopo:** MVP manual<br>
**Módulo:** PriceTracking<br>
**Responsável pelo domínio:** Produto

## Problema e objetivo

Permitir que uma conta autenticada crie um acompanhamento privado para um produto informado manualmente, por texto e/ou GTIN/EAN. A operação não pesquisa a Internet nem cria job de coleta.

## Atores e autorização

- Ator: usuário local autenticado.
- O acompanhamento pertence à conta identificada pelo principal autenticado no backend.
- A UI não envia `UserId` como prova de propriedade.

## Contexto e regras de domínio

- `DR-006`: não fundir produtos com variantes, condições ou kits incompatíveis.
- `DR-008`, `DR-009`, `DR-017`: o MVP manual não pesquisa páginas nem cria jobs.
- `DR-011`/`DR-012`: propriedade e privacidade por conta.

## Escopo

### Incluído

- Criar acompanhamento privado a partir de dados informados pelo usuário.
- Solicitar ao ProductCatalog o cadastro/resolução local do produto conforme `ResolveCanonicalProduct`.

### Fora de escopo

- Busca de candidatos, scraping, chamadas a lojas ou validação de URL.
- Criar jobs, consultar fontes externas ou confirmar anúncios automaticamente.
- Comparar frete ou pedir CEP.

## Comportamento esperado

O backend valida a entrada e cria ou associa o produto e acompanhamento à conta autenticada. Nenhum job é criado. A tela pode conduzir o usuário em seguida ao cadastro manual de uma oferta.

## Cenários de aceite

### AC-STT-001 — Iniciar por texto

**Dado** que o usuário está autenticado e informa uma descrição válida<br>
**Quando** solicita acompanhar o produto<br>
**Então** o produto e acompanhamento privados são registrados e nenhum job de busca é criado

### AC-STT-002 — Iniciar por GTIN/EAN

**Dado** que o usuário informa um GTIN/EAN válido<br>
**Quando** solicita acompanhar o produto<br>
**Então** o identificador informado fica associado ao produto e nenhum job de busca é criado

### AC-STT-003 — Validar entrada vazia ou inválida

**Dado** que os dados exigidos estão ausentes, inválidos ou conflitantes<br>
**Quando** o usuário solicita acompanhamento<br>
**Então** a operação é recusada sem criar produto ou acompanhamento

### AC-STT-004 — Isolar por conta

**Dado** que o usuário A inicia um acompanhamento<br>
**Quando** o usuário B consulta sua lista<br>
**Então** o acompanhamento de A não é exibido nem acessível a B

### AC-STT-005 — Exigir autenticação

**Dado** que a requisição não possui usuário autenticado<br>
**Quando** solicita acompanhamento<br>
**Então** a operação é recusada e nenhum dado é criado

## Regras de domínio aplicáveis

- `DR-006`, `DR-008`, `DR-009`, `DR-011`, `DR-012`, `DR-017`.

## Classes de domínio afetadas

| Classe/conceito | Responsabilidade/invariante | Critérios/regras |
|---|---|---|
| `TrackedProduct` (proposta) | Acompanhamento e proprietário local | `AC-STT-001` a `AC-STT-005`, `DR-011`, `DR-012` |
| `Product` (proposta) | Identidade baseada nos dados informados | `AC-STT-001` a `AC-STT-003`, `DR-006` |

## Rastreabilidade dos testes

| Critério/regra | Caso de uso relacionado | Nome(s) planejado(s) do teste/display name | Caminho no projeto de testes | Estado |
|---|---|---|---|---|
| `AC-STT-001` a `AC-STT-005` | Início de acompanhamento | `AC-STT-001 — Iniciar por texto`<br>`AC-STT-002 — Iniciar por GTIN/EAN`<br>`AC-STT-003 — Validar entrada vazia ou inválida`<br>`AC-STT-004 — Isolar por conta`<br>`AC-STT-005 — Exigir autenticação` | `tests/Modules/PriceTracking.Tests/Features/StartTracking/` | Pendente da implementação |
| `AC-STT-001` a `AC-STT-005`, `DR-011`/`DR-012` | `TrackedProduct` | `AC-STT-001 — TrackedProduct — Iniciar por texto`<br>`AC-STT-002 — TrackedProduct — Iniciar por GTIN/EAN`<br>`AC-STT-003 — TrackedProduct — Validar entrada vazia ou inválida`<br>`AC-STT-004 — TrackedProduct — Isolar por conta`<br>`AC-STT-005 — TrackedProduct — Exigir autenticação` | `tests/Modules/PriceTracking.Tests/Domain/TrackedProduct/` | Pendente da implementação |
| `AC-STT-001` a `AC-STT-003`, `DR-006` | `Product` (propriedade de ProductCatalog) | `AC-STT-001 — Product — Iniciar por texto`<br>`AC-STT-002 — Product — Iniciar por GTIN/EAN`<br>`AC-STT-003 — Product — Validar entrada vazia ou inválida` | `tests/Modules/ProductCatalog.Tests/Domain/Product/` | Pendente da implementação |

## Dados e integrações

- Entrada: texto e/ou GTIN/EAN conforme política ainda proposta em F0-05.
- Dados criados: produto e acompanhamento com proprietário derivado do principal.
- Efeitos externos: nenhum. Nenhum job é criado.
- Duplicatas: política de rejeição/reuso na mesma conta depende da decisão F0-05.

## Requisitos não funcionais

- Endpoint autenticado; aplicar antifalsificação se a operação REST for chamada por cookie.
- Persistência e vínculo de proprietário devem ser consistentes.

## Questões em aberto

- Pode informar texto e EAN juntos? Qual prevalece e como tratar conflito?
- Duplicatas de produtos/acompanhamentos na mesma conta serão recusadas ou reutilizadas?
- Qual limite de tamanho/formato de texto e quais regras de validação de GTIN/EAN?

## Decisões e referências

- [`docs/domain/context.md`](../../../domain/context.md)
- [`ResolveCanonicalProduct`](../../ProductCatalog/ResolveCanonicalProduct/spec.md)
- Decisões propostas em [`docs/decisions/registro-fase-0.md`](../../../decisions/registro-fase-0.md).
