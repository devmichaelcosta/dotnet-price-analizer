# Especificação — GetCollectionJobStatus

**Estado:** Rascunho — 2026-10-12<br>
**Escopo:** Condicional — não compõe o MVP manual; exige gate e spec incremental aprovada.<br>
**Módulo:** Operations<br>
**Responsável pelo domínio:** Operação e produto

## Problema e objetivo

Permitir que o usuário acompanhe estado e falhas resumidas de uma busca/atualização sem expor dados privados de outras contas ou segredos operacionais.

## Atores e autorização

- Ator: usuário autenticado proprietário do acompanhamento associado ao job.
- Diagnóstico operacional agregado não deve expor acompanhamentos ou histórico de outras contas; se necessário, será uma capacidade separada.
- Nunca expor credenciais, tokens, cabeçalhos, HTML bruto ou stack trace ao usuário.

## Contexto e regras de domínio

- `DR-011`/`DR-012`: job ligado ao acompanhamento privado por conta.
- Jobs são persistidos, idempotentes e retomáveis após reinício.

## Escopo

### Incluído

- Consultar estado atual, instante de início/fim e resultado resumido do job.
- Informar erro recuperável e se nova tentativa está prevista.

### Fora de escopo

- Exibir dados de infraestrutura ou segredos.
- Consultar job pertencente a outra conta.

## Comportamento esperado

O sistema consulta job pelo ID e exige autorização pelo recurso relacionado. A resposta apresenta estado conhecido e resumo seguro. A taxonomia final de estados deve cobrir fila, execução, sucesso parcial/final, adiamento, falha e cancelamento, sem publicar detalhes internos.

## Cenários de aceite

### AC-GCJ-001 — Consultar job próprio

**Dado** que o job pertence a um acompanhamento do usuário<br>
**Quando** consulta o estado<br>
**Então** recebe estado e horários relevantes de forma segura

### AC-GCJ-002 — Recusar job de outra conta

**Dado** que o job pertence a outro usuário<br>
**Quando** o usuário tenta consultá-lo<br>
**Então** o backend recusa sem revelar que o job existe

### AC-GCJ-003 — Representar resultado parcial/falha

**Dado** que uma fonte falhou ou o job teve sucesso parcial<br>
**Quando** o estado é consultado<br>
**Então** a resposta informa status resumido e ação/retentativa possível

### AC-GCJ-004 — Não revelar segredo ou detalhe interno

**Dado** que logs internos contêm exceções e configuração de conector<br>
**Quando** o resultado é mostrado ao usuário<br>
**Então** não inclui credenciais, tokens, cabeçalhos, payload sensível ou stack trace

## Regras de domínio aplicáveis

- `DR-011`, `DR-012` e persistência/idempotência de jobs definidas na arquitetura.

## Classes de domínio afetadas

| Classe/conceito | Responsabilidade/invariante | Critérios/regras |
|---|---|---|
| `CollectionJob` (proposta) | Estado persistido, tentativas e relação com recurso | `AC-GCJ-001` a `AC-GCJ-004` |

## Rastreabilidade dos testes

| Critério/regra | Caso de uso relacionado | Nome(s) planejado(s) do teste/display name | Caminho no projeto de testes | Estado |
|---|---|---|---|---|
| `AC-GCJ-001` a `AC-GCJ-004` | Consulta de job | `AC-GCJ-001 — Consultar job próprio`<br>`AC-GCJ-002 — Recusar job de outra conta`<br>`AC-GCJ-003 — Representar resultado parcial/falha`<br>`AC-GCJ-004 — Não revelar segredo ou detalhe interno` | `tests/Modules/Operations.Tests/Features/GetCollectionJobStatus/` | Pendente da implementação |
| `DR-009`/`AC-GCJ-001` a `AC-GCJ-004` | `CollectionJob` (propriedade de WebDiscovery) | `AC-GCJ-001 — CollectionJob — Consultar job próprio`<br>`AC-GCJ-002 — CollectionJob — Recusar job de outra conta`<br>`AC-GCJ-003 — CollectionJob — Representar resultado parcial/falha`<br>`AC-GCJ-004 — CollectionJob — Não revelar segredo ou detalhe interno` | `tests/Modules/WebDiscovery.Tests/Domain/CollectionJob/` | Pendente da implementação |

## Dados e integrações

- Entrada: ID do job e principal autenticado.
- Dados lidos: estado, datas, tentativas resumidas, vínculo ao acompanhamento.
- Efeitos: somente leitura.
- Idempotência: leitura sem efeito.

## Requisitos não funcionais

- Filtrar autorização no backend por proprietário.
- Resposta segura e estável, sem exposição de dados técnicos sensíveis.

## Questões em aberto

- Quais estados e transições exatos serão persistidos?
- O usuário vê falha por conector ou apenas uma mensagem resumida?

## Decisões e referências

- [`docs/domain/context.md`](../../../domain/context.md)
- [`docs/arquitetura-inicial.md`](../../../arquitetura-inicial.md)
