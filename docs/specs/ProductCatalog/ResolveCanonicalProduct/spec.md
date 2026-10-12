# Especificação — ResolveCanonicalProduct

**Estado:** Rascunho — 2026-10-12<br>
**Escopo:** MVP manual<br>
**Módulo:** ProductCatalog<br>
**Responsável pelo domínio:** Produto

## Problema e objetivo

Representar localmente o produto que a pessoa quer acompanhar a partir de dados fornecidos por ela. A resolução não depende de candidato encontrado na Internet.

## Atores e autorização

- Ator: caso de uso `StartTracking`, iniciado por usuário autenticado.
- O chamador deriva o proprietário do principal autenticado e não confia em `UserId` enviado pelo cliente.
- O catálogo do MVP é privado por conta; não revelar nem reutilizar dados de produto de outra conta sem decisão explícita.

## Contexto e regras de domínio

- `DR-001`: produto, oferta e observação são conceitos separados.
- `DR-006`: não consolidar variantes, condições ou kits incompatíveis.
- `DR-011`/`DR-012`: propriedade e privacidade por conta.

## Escopo

### Incluído

- Criar ou localizar a identidade local do produto a partir de texto e/ou GTIN/EAN informado.
- Preservar identificadores e atributos efetivamente fornecidos, sem inventar valores.
- Manter os dados no escopo de privacidade da conta.

### Fora de escopo

- Resolver um candidato obtido por busca automática.
- Buscar ou validar dados em sites, APIs, feeds ou outra fonte externa.
- Mesclar produtos entre contas.

## Comportamento esperado

O caso de uso recebe campos fornecidos pela pessoa, aplica as regras aprovadas de validação e duplicidade e devolve um produto local. Se as informações forem conflitantes ou insuficientes, não consolida silenciosamente identidades diferentes.

## Cenários de aceite

### AC-RCP-001 — Registrar produto informado

**Dado** que o usuário autenticado informa dados válidos de um produto<br>
**Quando** o caso de uso resolve o produto local<br>
**Então** existe um registro associado à conta e somente com campos fornecidos ou derivados por regra aprovada

### AC-RCP-002 — Manter conceitos separados

**Dado** que os dados do produto e de uma oferta manual são registrados<br>
**Quando** o produto é criado ou localizado<br>
**Então** a identidade do produto não incorpora a loja, URL, vendedor, oferta ou observação

### AC-RCP-003 — Não fundir identidades conflitantes

**Dado** que dois registros têm GTIN/EAN ou atributos de variante incompatíveis<br>
**Quando** são avaliados para resolução<br>
**Então** o caso de uso não os consolida automaticamente no mesmo produto

### AC-RCP-004 — Preservar identificador validado

**Dado** que a entrada contém GTIN/EAN válido e não conflitante<br>
**Quando** o produto local é resolvido<br>
**Então** o identificador informado fica preservado como dado de identidade

### AC-RCP-005 — Manter catálogo privado por conta

**Dado** que outra conta possui um produto com os mesmos identificadores informados<br>
**Quando** o produto é resolvido para a conta atual<br>
**Então** o sistema não revela nem reutiliza o registro privado da outra conta

## Regras de domínio aplicáveis

- `DR-001`, `DR-006`, `DR-011`, `DR-012`.

## Classes de domínio afetadas

| Classe/conceito | Responsabilidade/invariante | Critérios/regras |
|---|---|---|
| `Product` (proposta) | Identidade do produto e atributos fornecidos | `AC-RCP-001` a `AC-RCP-004`, `DR-001`, `DR-006` |

## Rastreabilidade dos testes

| Critério/regra | Caso de uso/classe | Nome(s) planejado(s) do teste/display name | Caminho no projeto de testes | Estado |
|---|---|---|---|---|
| `AC-RCP-001` a `AC-RCP-005`; `DR-011`/`DR-012` | Resolução/cadastro do produto | `AC-RCP-001 — Registrar produto informado`<br>`AC-RCP-002 — Manter conceitos separados`<br>`AC-RCP-003 — Não fundir identidades conflitantes`<br>`AC-RCP-004 — Preservar identificador validado`<br>`AC-RCP-005 — Manter catálogo privado por conta` | `tests/Modules/ProductCatalog.Tests/Features/ResolveCanonicalProduct/` | Pendente da implementação |
| `AC-RCP-001` a `AC-RCP-004`, `DR-001`/`DR-006` | `Product` | `AC-RCP-001 — Product — Registrar produto informado`<br>`AC-RCP-002 — Product — Manter conceitos separados`<br>`AC-RCP-003 — Product — Não fundir identidades conflitantes`<br>`AC-RCP-004 — Product — Preservar identificador validado` | `tests/Modules/ProductCatalog.Tests/Domain/Product/` | Pendente da implementação |

## Dados e integrações

- Entrada: nome/descrição e/ou GTIN/EAN e demais atributos que F0-05/F0-16 aprovarem.
- Dados criados/lidos: produto local privado no escopo da conta.
- Efeitos externos: nenhum.
- Concorrência/duplicidade: regra depende da decisão F0-05.

## Requisitos não funcionais

- Não consultar a Internet nem confiar em um `OwnerId` informado pelo cliente.
- Preservar proveniência dos campos e não expor produtos privados de outra conta.

## Questões em aberto

- Catálogo privado por conta ou entidade pertencente ao acompanhamento?
- Regras de normalização/validação de GTIN/EAN, marca, modelo e variantes?
- Como resolver conflito ou duplicidade de identificador sem fonte externa?

## Decisões e referências

- [`docs/domain/context.md`](../../../domain/context.md)
- [`StartTracking`](../../PriceTracking/StartTracking/spec.md)
- Decisões propostas em [`docs/decisions/registro-fase-0.md`](../../../decisions/registro-fase-0.md).
