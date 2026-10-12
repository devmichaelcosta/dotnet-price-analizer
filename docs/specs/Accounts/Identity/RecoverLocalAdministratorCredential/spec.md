# Especificação — RecoverLocalAdministratorCredential

**Estado:** Rascunho — 2026-10-12<br>
**Módulo:** Accounts/Identity<br>
**Responsável pelo domínio:** Produto e segurança

## Problema e objetivo

Permitir que o administrador local recupere acesso após perder a credencial, sem e-mail ou dependência de serviço externo.

## Atores e autorização

- Ator: pessoa que executa o comando no terminal do host.
- Não exige sessão web, que pode estar inacessível.
- A extensão da autorização a qualquer usuário do sistema operacional ainda precisa ser decidida para este comando.

## Contexto e regras de domínio

- Regra confirmada: recuperação da credencial administrativa por comando local no host, sem e-mail.
- Regras confirmadas de segurança: usar fluxos suportados pelo Identity; não expor senha em argumentos, logs ou arquivos versionados.
- Hipótese: o operador escolhe a conta administrativa por login e define nova senha por entrada local segura.

## Escopo

### Incluído

- Redefinição local da credencial de uma conta administrativa existente.

### Fora de escopo

- Recuperação de MFA, provisionamento de contas ou contato por e-mail.
- Bypass de políticas de senha, lockout ou auditoria do Identity.

## Comportamento esperado

O comando identifica a conta administrativa alvo e solicita uma nova senha por canal local que não a exiba nem a coloque em argumentos. O mecanismo de reset deve usar as APIs suportadas do Identity. Em qualquer falha, nenhuma senha parcial é mantida e a saída não revela a senha.

## Cenários de aceite

### AC-RLA-001 — Recuperar a credencial administrativa localmente

**Dado** que existe uma conta administrativa e o operador tem acesso ao terminal do host<br>
**Quando** conclui o fluxo local com uma nova senha válida<br>
**Então** o Identity atualiza a credencial e o usuário pode autenticar com a nova senha

### AC-RLA-002 — Não depender de e-mail

**Dado** que o host está sem acesso a serviços externos<br>
**Quando** o operador executa a recuperação local<br>
**Então** o fluxo pode concluir sem envio ou validação por e-mail

### AC-RLA-003 — Proteger a nova senha

**Dado** que o operador informa uma nova senha<br>
**Quando** o comando executa a redefinição<br>
**Então** o valor não aparece em argumentos, logs, telemetria, saída do terminal ou arquivos versionados

### AC-RLA-004 — Recusar alvo inexistente ou não administrativo

**Dado** que o login não corresponde a uma conta administrativa elegível<br>
**Quando** o operador tenta redefinir a credencial<br>
**Então** nenhuma conta é alterada e a resposta não expõe informações desnecessárias sobre outras contas

## Regras de domínio aplicáveis

- Conta administrativa provisionada localmente; senha e política geridas pelo ASP.NET Core Identity.

## Classes de domínio afetadas

| Classe | Responsabilidade/invariante | Critérios/regras |
|---|---|---|
| Nenhuma classe de domínio própria definida | Delegar gestão de senha e validação ao Identity | `AC-RLA-001` a `AC-RLA-004` |

## Rastreabilidade dos testes

| Critério/regra | Caso de uso relacionado | Nome(s) planejado(s) do teste/display name | Caminho no projeto de testes | Estado |
|---|---|---|---|---|
| `AC-RLA-001` | Recuperação local de credencial | `AC-RLA-001 — Recuperar a credencial administrativa localmente` | `tests/Modules/Accounts.Identity.Tests/Features/RecoverLocalAdministratorCredential/` | Pendente da implementação |
| `AC-RLA-002` | Recuperação local de credencial | `AC-RLA-002 — Não depender de e-mail` | `tests/Modules/Accounts.Identity.Tests/Features/RecoverLocalAdministratorCredential/` — execução sem dependência externa | Pendente da implementação |
| `AC-RLA-003` | Recuperação local de credencial | `AC-RLA-003 — Proteger a nova senha` | `tests/Modules/Accounts.Identity.Tests/Features/RecoverLocalAdministratorCredential/` — inspeção de saída e logs | Pendente da implementação |
| `AC-RLA-004` | Recuperação local de credencial | `AC-RLA-004 — Recusar alvo inexistente ou não administrativo` | `tests/Modules/Accounts.Identity.Tests/Features/RecoverLocalAdministratorCredential/` — alvo inexistente/não admin | Pendente da implementação |

## Dados e integrações

- Entradas: login alvo e nova senha em entrada local segura.
- Dados alterados: credencial da conta Identity e, se necessário, estado de troca obrigatória de senha, a definir.
- Efeitos: auditoria local sem segredo; campos exatos pendentes.
- Idempotência: chamada repetida com nova senha repete redefinição de forma explícita, sem criar contas.

## Requisitos não funcionais

- Segurança: não implementar hashing, redefinição nem política de senha próprios.
- Observabilidade: nunca incluir valores de senha ou tokens de reset no registro de auditoria.

## Questões em aberto

- O comando pode ser executado por qualquer usuário com acesso ao terminal ou exige confirmação adicional de operador?
- Deve pedir confirmação do login e mostrar a conta-alvo antes da alteração, como decidido para reset de MFA?
- A operação remove lockout e/ou exige troca de senha no próximo login?
- Que dados de auditoria, política de retenção e mecanismo resistente a adulteração serão usados?

## Decisões e referências

- [`docs/domain/context.md`](../../../../domain/context.md)
- [`docs/arquitetura-inicial.md`](../../../../arquitetura-inicial.md)
