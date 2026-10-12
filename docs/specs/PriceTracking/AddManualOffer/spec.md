# Especificação — AddManualOffer

**Estado:** Rascunho — 2026-10-12<br>
**Escopo:** MVP manual<br>
**Módulo:** PriceTracking<br>
**Responsável pelo domínio:** Produto

## Problema e objetivo

Permitir que uma pessoa registre manualmente uma oferta de loja para um produto acompanhado e informe seu primeiro preço/disponibilidade, sem o sistema consultar a loja.

## Atores e autorização

- Ator: usuário local autenticado e proprietário do acompanhamento.
- O backend deriva o usuário do principal e valida a propriedade em cada operação.
- A UI não envia um `UserId` como prova de propriedade.

## Contexto e regras de domínio

- `DR-001`: produto, oferta e observação são conceitos diferentes.
- `DR-003`/`DR-004`/`DR-005`: moeda e valor decimal, proveniência/instante UTC e indisponibilidade distinta de preço zero.
- `DR-011`/`DR-012`: propriedade e privacidade por conta.
- `DR-017`/`DR-019`: nenhuma consulta de rede no MVP; URL é somente referência textual.
- `DR-018`: dado manual é rotulado como informado pelo usuário e não verificado pela loja.

## Escopo

### Incluído

- Cadastrar uma oferta manual vinculada a produto/acompanhamento do usuário.
- Registrar a observação inicial manual de preço/moeda/disponibilidade junto à oferta.
- Guardar autoria/proveniência e instante conforme as decisões aprovadas.

### Fora de escopo

- Abrir, validar ou consultar a URL.
- Pesquisar candidatos, acessar lojas, usar API/feed/agregador, ou afirmar que os dados vieram da loja.
- Habilitar coleta automática ou criar job.

## Comportamento esperado

O usuário informa os campos de oferta aprovados por F0-06/F0-16 e os dados da observação inicial. O backend valida autorização e invariantes, cria a oferta e a observação manual de forma atômica e apresenta a origem como informada pelo usuário. Campos obrigatórios e semântica do horário ainda precisam ser decididos antes da aprovação da spec.

## Cenários de aceite

### AC-AMO-001 — Cadastrar oferta e primeira observação

**Dado** que o usuário possui acompanhamento e informa uma oferta válida com seu estado/preço inicial<br>
**Quando** confirma o cadastro<br>
**Então** a oferta e a observação inicial são persistidas vinculadas ao produto/acompanhamento

### AC-AMO-002 — Identificar a origem manual

**Dado** que os dados da oferta e do preço foram informados pelo usuário<br>
**Quando** o cadastro é concluído<br>
**Então** oferta e observação indicam origem manual e autoria da conta, sem rótulo de verificação pela loja

### AC-AMO-003 — Rejeitar dados inválidos sem gravação parcial

**Dado** que falta um campo obrigatório ou preço/moeda/disponibilidade viola regra aprovada<br>
**Quando** o usuário tenta cadastrar a oferta<br>
**Então** nenhuma oferta ou observação parcial é persistida

### AC-AMO-004 — Impedir acesso a acompanhamento de outra conta

**Dado** que o identificador informado pertence a outro usuário<br>
**Quando** a oferta é cadastrada<br>
**Então** a operação é recusada sem revelar os dados do recurso alheio

### AC-AMO-005 — Não acessar URL informada

**Dado** que o usuário informa URL de referência para a oferta<br>
**Quando** salva o cadastro<br>
**Então** o sistema armazena a URL somente como dado e não realiza requisição HTTP para ela

### AC-AMO-006 — Exigir autenticação

**Dado** que a solicitação não tem usuário autenticado<br>
**Quando** tenta criar uma oferta<br>
**Então** a operação é recusada e nenhum dado é gravado

## Regras de domínio aplicáveis

- `DR-001`, `DR-003`, `DR-004`, `DR-005`, `DR-011`, `DR-012`, `DR-017`, `DR-018`, `DR-019`.

## Classes de domínio afetadas

| Classe/conceito | Responsabilidade/invariante | Critérios/regras |
|---|---|---|
| `Offer` (proposta) | Referência manual de oferta vinculada ao produto e proprietário | `AC-AMO-001` a `AC-AMO-006`, `DR-001`, `DR-011`, `DR-012`, `DR-019` |
| `PriceObservation` (proposta) | Primeiro preço manual, moeda, disponibilidade, instante e proveniência | `AC-AMO-001` a `AC-AMO-003`, `DR-003` a `DR-005`, `DR-018` |

## Rastreabilidade dos testes

| Critério/regra | Caso de uso relacionado | Nome(s) planejado(s) do teste/display name | Caminho no projeto de testes | Estado |
|---|---|---|---|---|
| `AC-AMO-001` a `AC-AMO-006` | Cadastro manual de oferta | `AC-AMO-001 — Cadastrar oferta e primeira observação`<br>`AC-AMO-002 — Identificar a origem manual`<br>`AC-AMO-003 — Rejeitar dados inválidos sem gravação parcial`<br>`AC-AMO-004 — Impedir acesso a acompanhamento de outra conta`<br>`AC-AMO-005 — Não acessar URL informada`<br>`AC-AMO-006 — Exigir autenticação` | `tests/Modules/PriceTracking.Tests/Features/AddManualOffer/` | Pendente da implementação |
| `AC-AMO-001`/`AC-AMO-002`/`AC-AMO-003`, `DR-001`/`DR-003`/`DR-004`/`DR-005`/`DR-018`/`DR-019` | `Offer` | `AC-AMO-001 — Offer — Cadastrar oferta e primeira observação`<br>`AC-AMO-002 — Offer — Identificar a origem manual`<br>`AC-AMO-003 — Offer — Rejeitar dados inválidos sem gravação parcial` | `tests/Modules/PriceTracking.Tests/Domain/Offer/` | Pendente da implementação |
| `AC-AMO-001`/`AC-AMO-002`/`AC-AMO-003`, `DR-003`/`DR-004`/`DR-005`/`DR-018` | `PriceObservation` | `AC-AMO-001 — PriceObservation — Cadastrar oferta e primeira observação`<br>`AC-AMO-002 — PriceObservation — Identificar a origem manual`<br>`AC-AMO-003 — PriceObservation — Rejeitar dados inválidos sem gravação parcial` | `tests/Modules/PriceTracking.Tests/Domain/PriceObservation/` | Pendente da implementação |

## Dados e integrações

- Entrada proposta: nome da loja, título ou descrição da oferta, referência URL opcional, atributos de variante/condição, preço, moeda e disponibilidade. Campos finais dependem de F0-06/F0-16.
- Dados criados: `Offer` com origem manual e primeira `PriceObservation` com autoria/proveniência acordadas.
- Efeitos externos: nenhum. Não há rede, credenciais, API, feed ou job.
- Atomicidade: a oferta não deve persistir sem o resultado de observação definido pela spec.

## Requisitos não funcionais

- Validar a propriedade no backend antes de criar dados.
- Usar transação para evitar oferta criada sem estado inicial consistente.
- Instantes são persistidos em UTC; a regra de qual instante registrar depende de F0-16.

## Questões em aberto

- Quais campos são obrigatórios? O nome da loja e título são ambos necessários?
- URL deve ser opcional? Como normalizar/validar texto sem realizar requisição?
- Como representar vendedor, condição, variante, preço promocional e indisponibilidade?
- O instante é o momento de salvar ou um horário informado pelo usuário?
- Duplicatas para a mesma URL/loja serão rejeitadas, mescladas ou mantidas separadas?

## Decisões e referências

- [`docs/domain/context.md`](../../../domain/context.md)
- [`docs/decisions/registro-fase-0.md`](../../../decisions/registro-fase-0.md) — F0-06, F0-07 e F0-16.
- [`RecordPriceObservation`](../RecordPriceObservation/spec.md) — atualizações posteriores da observação.
