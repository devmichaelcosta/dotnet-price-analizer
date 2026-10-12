# Especificação — <nome do caso de uso>

**Estado:** Rascunho  
**Módulo:** <módulo dono>  
**Responsável pelo domínio:** <papel ou pessoa>  
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

### AC-001 — <resultado esperado>

**Dado** <contexto inicial>  
**Quando** <ação>  
**Então** <resultado observável>

### AC-002 — <falha esperada>

**Dado** <contexto inicial>  
**Quando** <ação que falha>  
**Então** <resposta segura e estado esperado>

## Regras de domínio aplicáveis

- `DR-...` — <regra de `docs/domain/context.md` ou regra aprovada em uma especificação relacionada>

## Classes de domínio afetadas

| Classe | Responsabilidade/invariante | Critérios `AC-...` / regras `DR-...` |
|---|---|---|
| <classe de domínio> | <regra que protege> | `AC-...`, `DR-...` |

## Rastreabilidade dos testes

| Critério/regra | Classe(s) ou caso de uso relacionado | Teste(s) automatizado(s) | Estado |
|---|---|---|---|
| `AC-001` / `DR-...` | <domínio/caso de uso> | <nome do teste e caminho no projeto de testes> | Pendente |

Todo critério `AC-...` e toda regra de domínio aplicável devem apontar para teste(s). Cada classe de domínio afetada deve aparecer na tabela e ter teste(s) cobrindo os critérios/regras que lhe cabem e seus comportamentos/invariantes.

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
