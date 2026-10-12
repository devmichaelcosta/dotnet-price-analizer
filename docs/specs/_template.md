# Especificação — <nome do caso de uso>

**Estado:** Rascunho<br>
**Módulo:** <módulo dono><br>
**Responsável pelo domínio:** <papel ou pessoa><br>
**Atualizado em:** <AAAA-MM-DD>

## Problema e objetivo

Que necessidade do usuário ou regra do domínio este caso de uso atende?

## Atores e autorização

- Ator(es):
- Pré-condições de autenticação/autorização:
- Recursos e proprietário(s):

## Contexto e regras de domínio

- Conceitos do domínio envolvidos, conforme [`context.md`](../domain/context.md):
- Regras confirmadas:
- Hipóteses a validar:

## Escopo

### Incluído

-

### Fora de escopo

-

## Comportamento esperado

Descrever fluxo normal, alternativas, estados e efeitos observáveis. Não prescrever estrutura de classes antes de haver uma razão arquitetural.

## Cenários de aceite

### AC-<SIGLA-DO-USE-CASE>-001 — <resultado esperado>

**Dado** <contexto inicial><br>
**Quando** <ação><br>
**Então** <resultado observável>

### AC-<SIGLA-DO-USE-CASE>-002 — <falha esperada>

**Dado** <contexto inicial><br>
**Quando** <ação que falha><br>
**Então** <resposta segura e estado esperado>

## Regras de domínio aplicáveis

- `DR-...` — <regra de `docs/domain/context.md` ou regra aprovada em uma especificação relacionada>

## Classes de domínio afetadas

| Classe | Responsabilidade/invariante | Critérios `AC-...` / regras `DR-...` |
|---|---|---|
| <classe de domínio> | <regra que protege> | `AC-...`, `DR-...` |

## Rastreabilidade dos testes

| Critério/regra | Classe/caso de uso | Nome(s) planejado(s) do teste/display name | Caminho no projeto de testes | Estado |
|---|---|---|---|---|
| `AC-XXX-001` | <caso de uso> | `AC-XXX-001 — <título do critério>`; método `AC_XXX_001_<Behavior>` | `tests/Modules/<Modulo>.Tests/Features/<UseCase>/` | Planejado |
| `AC-XXX-001` / `DR-...` | <classe de domínio> | `AC-XXX-001_<Class>_<Behavior>` | `tests/Modules/<Modulo>.Tests/Domain/<Classe>/` | Planejado |

Todo critério `AC-...` e toda regra de domínio aplicável devem apontar para teste(s) planejados com nome identificável. Cada classe de domínio afetada deve aparecer numa linha própria e ter teste(s) cobrindo os critérios/regras que lhe cabem e seus comportamentos/invariantes. IDs agrupados numa linha correspondem a testes planejados individuais, salvo justificativa explícita de cobertura conjunta.

O display name do teste de caso de uso deve carregar o ID `AC-...` correspondente. Testes de domínio também incluem o AC relacionado e a classe testada. Se um teste cobrir vários critérios, liste todos no nome/matriz e justifique a cobertura conjunta. O estado só passa a aprovado depois de implementar e executar os testes.

## Dados e integrações

- Entradas e validação:
- Dados lidos/criados/alterados:
- Eventos/jobs/efeitos externos:
- Idempotência e concorrência:

## Requisitos não funcionais

- Segurança e privacidade:
- Desempenho/capacidade:
- Observabilidade/auditoria:
- Disponibilidade/retentativas:

## Questões em aberto

-

## Decisões e referências

- ADRs relacionados:
- Especificações relacionadas:
- Referências de produto/domínio:
