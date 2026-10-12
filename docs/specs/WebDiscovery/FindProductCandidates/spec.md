# Especificação — FindProductCandidates

**Estado:** Rascunho condicional — não compõe o MVP manual; nenhum domínio foi aprovado.<br>
**Módulo:** WebDiscovery<br>
**Responsável pelo domínio:** Produto e fontes

> Esta spec só pode ser promovida para um incremento depois que os termos de um domínio específico permitirem expressamente coleta HTML automatizada, os campos e a retenção pretendidos, e o usuário aprovar a spec incremental. APIs, feeds, agregadores e integrações de terceiros não são fontes permitidas.

## Problema e objetivo

Em evolução futura, permitir que uma conta autenticada procure candidatos nas páginas HTML públicas de domínios de lojas previamente aprovados, retornando informações suficientes para identificar produto, variante, vendedor e preço sem alegar cobertura da Internet inteira.

## Atores e autorização

- Ator: usuário autenticado proprietário do acompanhamento; um Worker futuro poderá executar a consulta em nome de job autorizado.
- A leitura de resultados sempre valida propriedade no backend.
- Apenas conectores HTML instalados, aprovados e habilitados podem consultar seu domínio.

## Contexto e regras de domínio

- `DR-001`: produto, página, oferta e observação são conceitos distintos.
- `DR-006`: não fundir variantes, condições, kits ou vendedores incompatíveis.
- `DR-008`/`DR-014`/`DR-015`/`DR-017`: a automação é HTML público futuro, condicionada a permissão expressa; sem API/feed/terceiro e sem contornar controles.
- `DR-009`: jobs automatizados não são criados pelo MVP manual; detalhes dependem de spec futura.

## Escopo

### Incluído, somente após aprovação incremental

- Consultar páginas públicas de busca/categoria previamente autorizadas para o domínio específico.
- Normalizar respostas HTML em candidatos com origem, URL e instante da consulta.
- Informar domínio consultado, falha ou ausência de cobertura.

### Fora de escopo

- Qualquer busca automática no MVP manual.
- API, feed, agregador, serviço de busca externo, página autenticada ou rota sem permissão expressa.
- Login em loja, CAPTCHA, contorno de bloqueio, ou alteração de conta/anúncio/pedido.
- Confirmar automaticamente correspondências incertas.

## Comportamento esperado

O Worker futuro recebe consulta e conjunto de domínios HTML aprovados, consulta somente os caminhos públicos expressamente autorizados e aplica limites do domínio. Os candidatos mantêm proveniência e diferenças de oferta. Falha de um domínio não descarta resultados válidos dos demais. Apenas campos permitidos são persistidos. A primeira implementação exige plano técnico e critérios específicos do domínio.

## Cenários de aceite

### AC-FPC-001 — Retornar candidatos com origem

**Dado** que um domínio HTML passou pelo gate e está habilitado<br>
**Quando** a consulta termina<br>
**Então** cada candidato tem domínio, URL pública de origem, instante de consulta e campos permitidos pela spec da fonte

### AC-FPC-002 — Preservar diferenças de oferta

**Dado** que a busca encontra vendedores, variantes ou condições distintos<br>
**Quando** os resultados são normalizados<br>
**Então** anúncios incompatíveis permanecem candidatos separados

### AC-FPC-003 — Isolar falha por domínio

**Dado** que um domínio falha e outro conclui<br>
**Quando** o job processa os domínios autorizados<br>
**Então** resultados válidos permanecem disponíveis e a falha fica associada ao domínio afetado

### AC-FPC-004 — Não consultar domínio desabilitado ou ambíguo

**Dado** que o domínio não tem permissão expressa registrada, está desabilitado ou sua evidência expirou<br>
**Quando** a busca é executada<br>
**Então** nenhuma requisição é enviada a ele

### AC-FPC-005 — Não prometer cobertura exaustiva

**Dado** que a consulta termina<br>
**Quando** resultados são mostrados<br>
**Então** a interface informa os domínios consultados e não declara que toda a Internet foi pesquisada

### AC-FPC-006 — Critério substituído

O critério anterior que exigia consulta a APIs próprias foi substituído em 2026-10-12 pela restrição vigente de HTML-only futuro. Ele não autoriza nem descreve comportamento implementável.

### AC-FPC-007 — Usar somente HTML permitido

**Dado** que a aplicação tem conectores instalados para domínios aprovados<br>
**Quando** executa uma consulta automatizada<br>
**Então** lê somente HTML público dos caminhos autorizados e não chama APIs, feeds, agregadores ou serviços de terceiros

## Regras de domínio aplicáveis

- `DR-001`, `DR-006`, `DR-008`, `DR-009`, `DR-014`, `DR-015`, `DR-017`.

## Classes de domínio afetadas

| Classe/conceito | Responsabilidade/invariante | Critérios/regras |
|---|---|---|
| `ProductCandidate` (proposta) | Resultado externo ainda não confirmado | `AC-FPC-001` a `AC-FPC-005`, `AC-FPC-007`, `DR-006` |
| `OfferCandidate` (contrato) | Oferta HTML ainda não confirmada; distinta de `Offer` de PriceTracking | `AC-FPC-001`/`AC-FPC-002`, `DR-001`, `DR-006` |
| `CollectionJob` (proposta futura) | Estado e falhas por domínio | `AC-FPC-003` a `AC-FPC-005` |

## Rastreabilidade dos testes

| Critério/regra | Caso de uso relacionado | Nome(s) planejado(s) do teste/display name | Caminho no projeto de testes | Estado |
|---|---|---|---|---|
| `AC-FPC-001` a `AC-FPC-005`, `AC-FPC-007` | Busca de candidatos | `AC-FPC-001 — Retornar candidatos com origem`<br>`AC-FPC-002 — Preservar diferenças de oferta`<br>`AC-FPC-003 — Isolar falha por domínio`<br>`AC-FPC-004 — Não consultar domínio desabilitado ou ambíguo`<br>`AC-FPC-005 — Não prometer cobertura exaustiva`<br>`AC-FPC-007 — Usar somente HTML permitido` | `tests/Modules/WebDiscovery.Tests/Features/FindProductCandidates/` | Pendente de fonte elegível/spec aprovada |
| `AC-FPC-006` | Critério histórico substituído; não descreve comportamento vigente | — (critério substituído; sem teste) | Não aplicável | Substituído por `AC-FPC-007`; sem teste |
| `AC-FPC-001` a `AC-FPC-005`, `AC-FPC-007`, `DR-006` | `ProductCandidate` | `AC-FPC-001 — ProductCandidate — Retornar candidatos com origem`<br>`AC-FPC-002 — ProductCandidate — Preservar diferenças de oferta`<br>`AC-FPC-003 — ProductCandidate — Isolar falha por domínio`<br>`AC-FPC-004 — ProductCandidate — Não consultar domínio desabilitado ou ambíguo`<br>`AC-FPC-005 — ProductCandidate — Não prometer cobertura exaustiva`<br>`AC-FPC-007 — ProductCandidate — Usar somente HTML permitido` | `tests/Modules/WebDiscovery.Tests/Domain/ProductCandidate/` | Pendente de fonte elegível/spec aprovada |
| `AC-FPC-001`/`AC-FPC-002`, `DR-001`/`DR-006` | `OfferCandidate` | `AC-FPC-001 — OfferCandidate — Retornar candidatos com origem`<br>`AC-FPC-002 — OfferCandidate — Preservar diferenças de oferta` | `tests/Modules/WebDiscovery.Tests/Domain/OfferCandidate/` | Pendente de fonte elegível/spec aprovada |
| `AC-FPC-003` a `AC-FPC-005` | `CollectionJob` | `AC-FPC-003 — CollectionJob — Isolar falha por domínio`<br>`AC-FPC-004 — CollectionJob — Não consultar domínio desabilitado ou ambíguo`<br>`AC-FPC-005 — CollectionJob — Não prometer cobertura exaustiva` | `tests/Modules/WebDiscovery.Tests/Domain/CollectionJob/` | Pendente de fonte elegível/spec aprovada |

## Dados e integrações

- Entrada: consulta de texto/EAN/GTIN e domínio aprovado.
- Dados: candidatos e campos permitidos pelo domínio, URL pública e instante UTC.
- Efeitos: somente leitura de páginas HTML públicas expressamente permitidas.
- Idempotência: reprocessamento não deve duplicar candidatos equivalentes; chave depende do domínio aprovado.

## Requisitos não funcionais

- Timeouts, limites de concorrência e ritmo por domínio; respeitar respostas de bloqueio e não contornar controles.
- Respeitar retenção, atribuição e direitos de armazenamento documentados por domínio.
- Se houver jobs, resultados e estado precisam sobreviver a reinícios.

## Questões em aberto

- Que páginas de busca/categoria cada domínio permitirá consultar?
- O que identifica um candidato ao deduplicar URLs?
- Como tratar candidatos sem preço, com dados contraditórios ou campos ausentes?
- Quais evidências e prazo de revisão permitem manter um domínio habilitado?

## Decisões e referências

- [`docs/domain/context.md`](../../../domain/context.md)
- [`docs/research/validacao-fontes-fase-0.md`](../../../research/validacao-fontes-fase-0.md)
- [`ADR-003 — Núcleo manual e automação HTML condicionada`](../../../decisions/ADR-003-mvp-hibrido-e-fontes-html.md)
- [`DiscoverAndIndexPages`](../DiscoverAndIndexPages/spec.md)
