# Especificação — DiscoverAndIndexPages

**Estado:** Rascunho condicional — não compõe o MVP manual; nenhum domínio HTML elegível em 2026-10-12<br>
**Módulo:** WebDiscovery<br>
**Responsável pelo domínio:** Produto e fontes

> Esta fatia só poderá ser implementada depois que os termos permitirem expressamente coleta HTML automatizada no domínio e o gate individual for aprovado. A triagem atual não aprovou nenhum domínio. Não usar APIs, feeds, agregadores ou integrações de terceiros como fonte.

## Problema e objetivo

Localizar e indexar páginas de produto/oferta em domínios habilitados para que consultas posteriores reutilizem um índice próprio.

## Atores e autorização

- Ator: Worker local que executa jobs persistidos.
- A operação só considera domínio habilitado após evidência de permissão expressa nos termos públicos aplicáveis.
- A aplicação não contata loja nem automatiza área autenticada.

## Contexto e regras de domínio

- `DR-008`: índice próprio e cobertura incremental.
- `DR-014`/`DR-015`/`DR-017`: esta é a única forma automatizada considerada, para domínio HTML expressamente elegível e por leitura de páginas públicas; não usar API/feed nem contatar loja.
- `DR-009`: descoberta ampla ocorre em busca inicial, não a cada atualização.

## Escopo

### Incluído

- Descoberta limitada a sitemaps, catálogos, links ou busca pública do próprio domínio, conforme evidência registrada.
- Persistir referências a páginas e resultado por domínio.

### Fora de escopo

- Domínios sem aprovação, páginas autenticadas, APIs, feeds, CAPTCHA ou bypass de bloqueios.
- Indexar toda a Internet.

## Comportamento esperado

O Worker processa um job de descoberta e somente executa GETs nos caminhos públicos autorizados para o domínio. Usa timeout, concorrência limitada, identificação clara e ritmo configurado. Páginas novas são adicionadas ao índice sem duplicar referências. Falhas e recusas são classificadas; não se tenta contorná-las.

## Cenários de aceite

### AC-DIP-001 — Exigir evidência antes de habilitar domínio

**Dado** que o domínio não tem evidência de permissão expressa registrada<br>
**Quando** um job de descoberta é executado<br>
**Então** nenhuma requisição é enviada e o domínio permanece desabilitado

### AC-DIP-002 — Descobrir páginas públicas

**Dado** que o domínio está habilitado e um ponto de descoberta público permitido está configurado<br>
**Quando** o Worker executa a descoberta<br>
**Então** referências acessíveis são persistidas no índice com domínio e instante de coleta

### AC-DIP-003 — Não contornar bloqueios

**Dado** que a fonte apresenta bloqueio, CAPTCHA, exige login ou recusa acesso<br>
**Quando** o Worker recebe esse resultado<br>
**Então** registra a falha e não tenta contornar o controle

### AC-DIP-004 — Isolar falhas por domínio

**Dado** que um domínio falha durante uma execução com vários domínios elegíveis<br>
**Quando** os demais são processados<br>
**Então** falha de uma fonte não descarta resultados dos outros domínios

### AC-DIP-005 — Evitar duplicar páginas

**Dado** que uma URL normalizada já consta no índice do domínio<br>
**Quando** é reencontrada<br>
**Então** não é criada uma segunda página equivalente

## Regras de domínio aplicáveis

- `DR-008`, `DR-009`, `DR-014`, `DR-015`, `DR-017`.

## Classes de domínio afetadas

| Classe/conceito | Responsabilidade/invariante | Critérios/regras |
|---|---|---|
| `SourceDomain` (proposta) | Estado, evidência e política por domínio | `AC-DIP-001` a `AC-DIP-004`, `DR-015` |
| `SourcePage` (proposta) | Referência indexada e origem | `AC-DIP-002`, `AC-DIP-005`, `DR-008` |
| `CollectionJob` (proposta) | Estado e resultados parciais | `AC-DIP-002` a `AC-DIP-004`, `DR-009` |

## Rastreabilidade dos testes

| Critério/regra | Caso de uso relacionado | Nome(s) planejado(s) do teste/display name | Caminho no projeto de testes | Estado |
|---|---|---|---|---|
| `AC-DIP-001` a `AC-DIP-005` | Descoberta/indexação | `AC-DIP-001 — Exigir evidência antes de habilitar domínio`<br>`AC-DIP-002 — Descobrir páginas públicas`<br>`AC-DIP-003 — Não contornar bloqueios`<br>`AC-DIP-004 — Isolar falhas por domínio`<br>`AC-DIP-005 — Evitar duplicar páginas` | `tests/Modules/WebDiscovery.Tests/Features/DiscoverAndIndexPages/` | Pendente da implementação |
| `AC-DIP-001` a `AC-DIP-004`, `DR-015` | `SourceDomain` | `AC-DIP-001 — SourceDomain — Exigir evidência antes de habilitar domínio`<br>`AC-DIP-002 — SourceDomain — Descobrir páginas públicas`<br>`AC-DIP-003 — SourceDomain — Não contornar bloqueios`<br>`AC-DIP-004 — SourceDomain — Isolar falhas por domínio` | `tests/Modules/WebDiscovery.Tests/Domain/SourceDomain/` | Pendente da implementação |
| `AC-DIP-002`/`AC-DIP-005`, `DR-008` | `SourcePage` | `AC-DIP-002 — SourcePage — Descobrir páginas públicas`<br>`AC-DIP-005 — SourcePage — Evitar duplicar páginas` | `tests/Modules/WebDiscovery.Tests/Domain/SourcePage/` | Pendente da implementação |
| `AC-DIP-002` a `AC-DIP-004`, `DR-009` | `CollectionJob` | `AC-DIP-002 — CollectionJob — Descobrir páginas públicas`<br>`AC-DIP-003 — CollectionJob — Não contornar bloqueios`<br>`AC-DIP-004 — CollectionJob — Isolar falhas por domínio` | `tests/Modules/WebDiscovery.Tests/Domain/CollectionJob/` | Pendente da implementação |

## Dados e integrações

- Entrada: job, domínio habilitado, limites, User-Agent, ponto de descoberta.
- Dados: URL normalizada, tipo de página, fonte, instante, estado.
- Efeitos: somente HTTP GET a HTML público de fonte elegível.
- Idempotência: chave por domínio + URL normalizada; jobs persistidos e retomáveis.

## Requisitos não funcionais

- Timeout, limite de concorrência, backoff e estado por domínio.
- Não armazenar HTML bruto sem justificativa e permissão de retenção.
- Job sobrevivente a reinício de API/Worker.

## Questões em aberto

- Quais fontes piloto terão evidência expressa? Atualmente nenhuma está habilitada.
- Qual política de normalização de URL, query string e redirecionamento?
- Qual retenção é permitida para URL, HTML e metadados por domínio?
- A busca por texto será feita via página pública de busca do site ou somente por índice previamente conhecido?

## Decisões e referências

- [`docs/domain/context.md`](../../../domain/context.md)
- [`docs/research/validacao-fontes-fase-0.md`](../../../research/validacao-fontes-fase-0.md)
