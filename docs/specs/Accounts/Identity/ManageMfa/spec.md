# Especificação — ManageMfa

**Estado:** Rascunho — 2026-10-12<br>
**Módulo:** Accounts/Identity<br>
**Responsável pelo domínio:** Produto e segurança

## Problema e objetivo

Permitir cadastrar e validar TOTP para proteger o login e administrar códigos de recuperação sem e-mail.

## Atores e autorização

- Ator: usuário autenticado que administra o próprio segundo fator.
- Administradores devem concluir MFA para acesso normal; usuários comuns podem habilitá-la opcionalmente.
- O app não oferece reset de MFA para outra conta.

## Contexto e regras de domínio

- Usar TOTP em aplicativo autenticador.
- Códigos de recuperação são de uso único, exibidos com segurança e sem dependência de e-mail.
- Perda do autenticador e de todos os códigos é recuperada somente por comando local no host, conforme `ResetMfaFromHost`.
- Códigos/seeds nunca devem aparecer em logs, telemetria ou respostas depois da configuração inicial.

## Escopo

### Incluído

- Iniciar configuração TOTP, validar um código e ativar MFA.
- Exibir códigos de recuperação uma vez e consumi-los individualmente.
- Desativar MFA por iniciativa do usuário comum, caso a conta não tenha exigência administrativa.

### Fora de escopo

- E-mail, SMS, push ou serviço externo.
- Administrador resetar MFA pelo app.

## Comportamento esperado

O usuário inicia o cadastro e recebe secret/URI de provisioning uma única vez para inserir no autenticador. O sistema não considera MFA habilitada até validar um código TOTP. Após validação, códigos de recuperação são gerados e apresentados uma vez; sua persistência usa o mecanismo suportado pelo Identity. Usuários comuns podem desativar a própria MFA com reautenticação, se a política final permitir. Administradores não podem desativá-la.

## Cenários de aceite

### AC-MFA-001 — Validar cadastro TOTP antes de habilitar

**Dado** que o usuário iniciou o cadastro do autenticador<br>
**Quando** envia um código TOTP válido<br>
**Então** o segundo fator é habilitado para a conta

### AC-MFA-002 — Rejeitar código inválido

**Dado** que o usuário está no fluxo de cadastro<br>
**Quando** envia um código inválido ou expirado<br>
**Então** MFA permanece desabilitada e o erro não revela o secret

### AC-MFA-003 — Exigir MFA para administrador

**Dado** que a conta tem role administrativa<br>
**Quando** conclui cadastro/login sem MFA válida<br>
**Então** o acesso administrativo normal permanece bloqueado até ativar e validar TOTP

### AC-MFA-004 — Emitir códigos de recuperação de uso único

**Dado** que MFA foi habilitada<br>
**Quando** o sistema apresenta os códigos de recuperação<br>
**Então** eles são exibidos uma vez e cada código válido só pode ser usado uma vez

### AC-MFA-005 — Não permitir desativar MFA obrigatória

**Dado** que a conta é administrativa<br>
**Quando** tenta desativar MFA pelo app<br>
**Então** a operação é recusada

### AC-MFA-006 — Não permitir reset de terceiro no app

**Dado** que um usuário ou administrador acessa o app<br>
**Quando** tenta remover MFA da conta de outra pessoa<br>
**Então** não existe fluxo de reset pelo app e nenhuma credencial MFA alheia é alterada

## Regras de domínio aplicáveis

- MFA administrativa obrigatória; MFA de usuários comuns opcional.
- Código de recuperação é de uso único.
- Perda completa exige comando local auditado.

## Classes de domínio afetadas

| Classe | Responsabilidade/invariante | Critérios/regras |
|---|---|---|
| Nenhuma classe de domínio própria definida | Delegar TOTP e recovery codes ao Identity/Microsoft Authenticator mechanisms; evitar criptografia/algoritmos próprios | `AC-MFA-001` a `AC-MFA-006` |

## Rastreabilidade dos testes

| Critério/regra | Caso de uso relacionado | Nome(s) planejado(s) do teste/display name | Caminho no projeto de testes | Estado |
|---|---|---|---|---|
| `AC-MFA-001` a `AC-MFA-006` | Configuração e uso de MFA | `AC-MFA-001 — Validar cadastro TOTP antes de habilitar`<br>`AC-MFA-002 — Rejeitar código inválido`<br>`AC-MFA-003 — Exigir MFA para administrador`<br>`AC-MFA-004 — Emitir códigos de recuperação de uso único`<br>`AC-MFA-005 — Não permitir desativar MFA obrigatória`<br>`AC-MFA-006 — Não permitir reset de terceiro no app` | `tests/Modules/Accounts.Identity.Tests/Features/ManageMfa/` | Pendente da implementação |

## Dados e integrações

- Entradas: código TOTP, confirmação de reautenticação e código de recuperação.
- Dados alterados: estado MFA, seed protegida pelo mecanismo do Identity e códigos não consumidos.
- Efeitos: sem comunicação externa.
- Idempotência: consumir recovery code é operação atômica; segundo uso falha.

## Requisitos não funcionais

- Segredos TOTP e códigos de recuperação não são registrados nem exibidos novamente após a configuração.
- A UI deve explicar como guardar os códigos sem salvá-los no servidor em texto puro.

## Questões em aberto

- Qual mecanismo Microsoft suportado e versão do Identity serão adotados para TOTP/recovery codes?
- Exigir reautenticação para cadastrar, renovar ou desativar MFA?
- Como mostrar URI/QR sem transmitir segredo além da sessão local autenticada?
- A troca de senha temporária precede o cadastro MFA obrigatório?

## Decisões e referências

- [`docs/domain/context.md`](../../../../domain/context.md)
- [MFA no ASP.NET Core](https://learn.microsoft.com/aspnet/core/security/authentication/mfa)
