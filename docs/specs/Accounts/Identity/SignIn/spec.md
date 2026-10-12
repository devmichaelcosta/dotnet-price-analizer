# Especificação — SignIn

**Estado:** Rascunho — 2026-10-12<br>
**Módulo:** Accounts/Identity<br>
**Responsável pelo domínio:** Produto e segurança

## Problema e objetivo

Autenticar usuários provisionados localmente e estabelecer sessão segura para o Blazor Web App, aplicando troca de senha pendente, MFA e lockout.

## Atores e autorização

- Ator: usuário com conta local ativa.
- Pré-condição: conta criada pelo administrador local; não há auto cadastro.
- A Web API é privada e não emite JWT/OIDC no MVP. A sessão do navegador usa cookie protegido.

## Contexto e regras de domínio

- ASP.NET Core Identity é a fonte de autenticação e credenciais.
- MFA TOTP é obrigatória para administradores e opcional para os demais usuários.
- Após senha temporária, o usuário deve escolher nova senha antes de continuar.
- Valores de lockout, duração de sessão e sequência MFA/troca de senha ainda precisam ser definidos.

## Escopo

### Incluído

- Login de conta provisionada e logout.
- Sessão por cookie protegido para navegador local.
- Aplicação de lockout, troca obrigatória de senha e política de MFA.

### Fora de escopo

- Cadastro público, login social, JWT para consumidor externo ou fluxo de recuperação por e-mail.
- Acesso pela rede local ou Internet.

## Comportamento esperado

O usuário informa as credenciais. O Identity valida a conta, senha, estado e lockout. Se houver troca obrigatória de senha ou desafio TOTP pendente, o usuário só acessa os passos necessários até completar esses requisitos. A sessão normal só é estabelecida após os controles exigidos. O logout encerra a sessão.

## Cenários de aceite

### AC-SLI-001 — Autenticar conta ativa

**Dado** que a conta provisionada está ativa e não bloqueada<br>
**Quando** o usuário fornece credenciais válidas e completa MFA quando exigida<br>
**Então** o sistema estabelece sessão de navegador usando cookie protegido

### AC-SLI-002 — Rejeitar credencial inválida

**Dado** que o login ou senha é inválido<br>
**Quando** o usuário tenta entrar<br>
**Então** a sessão não é criada e a resposta não revela se o login existe

### AC-SLI-003 — Bloquear ou limitar tentativas repetidas

**Dado** que uma conta acumula tentativas inválidas conforme a política configurada<br>
**Quando** o limite de lockout é atingido<br>
**Então** o Identity aplica o bloqueio e o sistema não cria sessão

### AC-SLI-004 — Exigir troca de senha temporária

**Dado** que a conta tem troca de senha obrigatória<br>
**Quando** o usuário autentica com a senha temporária<br>
**Então** só pode acessar o fluxo de troca até definir uma senha válida

### AC-SLI-005 — Exigir MFA administrativa

**Dado** que a conta tem role administrativa e não concluiu TOTP<br>
**Quando** fornece senha válida<br>
**Então** o acesso normal permanece bloqueado até validar o segundo fator

### AC-SLI-006 — Encerrar sessão

**Dado** que existe sessão autenticada<br>
**Quando** o usuário solicita logout<br>
**Então** o cookie/sessão deixa de autorizar novas requisições

## Regras de domínio aplicáveis

- Contas provisionadas localmente, sem cadastro público.
- MFA TOTP obrigatória para administradores e opcional para usuários.
- A API é privada; cookie autenticado exige antifalsificação em operações de alteração.

## Classes de domínio afetadas

| Classe | Responsabilidade/invariante | Critérios/regras |
|---|---|---|
| Nenhuma classe de domínio própria definida | Autenticação delegada ao ASP.NET Core Identity | `AC-SLI-001` a `AC-SLI-006` |

## Rastreabilidade dos testes

| Critério/regra | Caso de uso relacionado | Nome(s) planejado(s) do teste/display name | Caminho no projeto de testes | Estado |
|---|---|---|---|---|
| `AC-SLI-001` a `AC-SLI-006` | Login e sessão | `AC-SLI-001 — Autenticar conta ativa`<br>`AC-SLI-002 — Rejeitar credencial inválida`<br>`AC-SLI-003 — Bloquear ou limitar tentativas repetidas`<br>`AC-SLI-004 — Exigir troca de senha temporária`<br>`AC-SLI-005 — Exigir MFA administrativa`<br>`AC-SLI-006 — Encerrar sessão` | `tests/Modules/Accounts.Identity.Tests/Features/SignIn/` | Pendente da implementação |

## Dados e integrações

- Entradas: identificador, senha e código TOTP quando aplicável.
- Dados lidos/alterados: conta Identity, contadores/estado de lockout e sessão.
- Efeitos: cookie protegido; sem chamadas externas.
- Idempotência: login é uma operação de sessão; tentativas de senha são contabilizadas pelo Identity.

## Requisitos não funcionais

- Cookie com `Secure`, `HttpOnly` e `SameSite` adequados, HTTPS local e chaves Data Protection persistentes/protegidas.
- Respostas de falha não distinguem conta inexistente, senha incorreta ou estado que permita enumeração.
- Requisições autenticadas por cookie que alterem estado devem ter antifalsificação.

## Questões em aberto

- Qual é o identificador de login?
- Quais valores de lockout, duração de sessão e revalidação serão usados?
- A troca obrigatória de senha ocorre antes ou depois do desafio MFA?
- Quais telas e erros do Identity serão personalizados sem alterar os mecanismos de autenticação?

## Decisões e referências

- [`docs/domain/context.md`](../../../../domain/context.md)
- [`docs/arquitetura-inicial.md`](../../../../arquitetura-inicial.md)
