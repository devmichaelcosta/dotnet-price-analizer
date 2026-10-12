# Especificação — ResetLocalUserCredentials

**Estado:** Rascunho — 2026-10-12<br>
**Módulo:** Accounts/Identity<br>
**Responsável pelo domínio:** Produto e segurança

## Problema e objetivo

Permitir que um administrador redefina a credencial de um usuário local pelo app, sem e-mail, e exija troca no próximo login.

## Atores e autorização

- Ator: administrador autenticado, autorizado e com MFA concluída quando exigida.
- Alvo: usuário local provisionado.
- O usuário que iniciou a sessão nunca é aceito como prova de autorização apenas por um identificador fornecido pelo cliente.

## Contexto e regras de domínio

- O administrador define uma senha temporária e a entrega diretamente ao usuário.
- No próximo login, o usuário deve escolher uma nova senha antes de continuar.
- Não expor nem manter senha temporária em logs ou texto puro.
- O canal de entrega é fora do app e não depende de e-mail.

## Escopo

### Incluído

- Selecionar conta alvo e redefinir sua credencial usando ASP.NET Core Identity.
- Marcar troca de senha obrigatória no próximo login.

### Fora de escopo

- Recuperação automática por e-mail, link externo ou API pública.
- Exibição posterior da senha temporária.

## Comportamento esperado

O administrador seleciona uma conta autorizada, define uma senha temporária por campo protegido e confirma a operação. O backend aplica a operação pelo Identity, marca a troca obrigatória e retorna apenas confirmação. O administrador entrega a senha diretamente ao usuário por um canal que ele controla. A senha não fica disponível novamente na UI nem em log.

## Cenários de aceite

### AC-RUC-001 — Redefinir senha de usuário

**Dado** que o ator é administrador autorizado e o alvo é uma conta local elegível<br>
**Quando** informa uma senha temporária válida e confirma<br>
**Então** o Identity atualiza a credencial e a conta fica marcada para troca no próximo login

### AC-RUC-002 — Exigir nova senha antes do uso normal

**Dado** que a credencial temporária foi definida<br>
**Quando** o usuário autentica pela primeira vez depois da redefinição<br>
**Então** só pode acessar o fluxo para escolher uma nova senha até concluir a troca

### AC-RUC-003 — Proteger o segredo

**Dado** que o administrador informa a senha temporária<br>
**Quando** o reset é executado<br>
**Então** a senha não aparece em logs, telemetria, respostas posteriores ou armazenamento em texto puro

### AC-RUC-004 — Recusar operação não autorizada

**Dado** que o ator não tem role administrativa válida ou antifalsificação adequada<br>
**Quando** tenta redefinir uma credencial<br>
**Então** a operação é recusada sem alterar a conta

### AC-RUC-005 — Recusar alvo inválido

**Dado** que a conta alvo não existe, está desativada ou não pode ser administrada<br>
**Quando** o administrador solicita o reset<br>
**Então** nenhuma credencial é alterada e a resposta não revela dados de outra conta

## Regras de domínio aplicáveis

- Credenciais são geridas pelo Identity.
- Sessões e recursos permanecem privados por conta autenticada.

## Classes de domínio afetadas

| Classe | Responsabilidade/invariante | Critérios/regras |
|---|---|---|
| Nenhuma classe de domínio própria definida | Usar APIs suportadas do Identity | `AC-RUC-001` a `AC-RUC-005` |

## Rastreabilidade dos testes

| Critério/regra | Caso de uso relacionado | Nome(s) planejado(s) do teste/display name | Caminho no projeto de testes | Estado |
|---|---|---|---|---|
| `AC-RUC-001` a `AC-RUC-005` | Redefinição administrativa | `AC-RUC-001 — Redefinir senha de usuário`<br>`AC-RUC-002 — Exigir nova senha antes do uso normal`<br>`AC-RUC-003 — Proteger o segredo`<br>`AC-RUC-004 — Recusar operação não autorizada`<br>`AC-RUC-005 — Recusar alvo inválido` | `tests/Modules/Accounts.Identity.Tests/Features/ResetLocalUserCredentials/` | Pendente da implementação |

## Dados e integrações

- Entradas: conta alvo e senha temporária.
- Dados alterados: credencial Identity e indicador de troca obrigatória.
- Efeitos: auditoria da operação sem segredo; canal de entrega é externo ao sistema e não é automatizado.
- Idempotência: cada solicitação confirmada é uma redefinição; requisições repetidas exigem nova confirmação.

## Requisitos não funcionais

- Usar mecanismos suportados pelo Identity e antifalsificação para alteração autenticada por cookie.
- Não enviar mensagem ou e-mail e não persistir senha temporária em texto puro.

## Questões em aberto

- Quais eventos e campos serão auditados, e por quanto tempo?
- A redefinição revoga sessões e códigos de recuperação existentes?

## Decisões e referências

- [`docs/domain/context.md`](../../../../domain/context.md)
- [`docs/arquitetura-inicial.md`](../../../../arquitetura-inicial.md)
