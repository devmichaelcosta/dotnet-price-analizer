# Especificação — ProvisionLocalUser

**Estado:** Rascunho — 2026-10-12<br>
**Escopo:** MVP manual<br>
**Módulo:** Accounts/Identity<br>
**Responsável pelo domínio:** Produto e segurança

## Problema e objetivo

Permitir que o administrador local crie e desative contas para as pessoas que usarão a instalação, sem cadastro público.

## Atores e autorização

- Ator: administrador autenticado e autorizado.
- Acesso exige MFA concluída quando aplicável à conta administrativa.
- Recursos administrados: contas locais, não acompanhamentos de produtos.

## Contexto e regras de domínio

- Regras confirmadas: contas provisionadas por administrador; sem cadastro público ou envio de e-mail.
- A senha temporária é definida pelo administrador, entregue diretamente ao usuário e trocada no próximo login.
- Identificador de login e política de desativação/exclusão ainda não foram fechados.

## Escopo

### Incluído

- Criar uma conta local sem role administrativa por padrão.
- Desativar uma conta local conforme política a decidir.

### Fora de escopo

- Cadastro aberto, convite por e-mail ou login por provedor externo.
- Acesso à conta de outro usuário ou a dados privados dele.

## Comportamento esperado

O administrador informa os dados mínimos da conta e uma senha temporária. O sistema valida unicidade e política do Identity, cria a conta e sinaliza a necessidade de troca da senha antes do acesso normal. A senha só é entregue fora do sistema, diretamente pelo administrador ao usuário. A desativação impede novos acessos e deve invalidar sessões segundo política pendente.

## Cenários de aceite

### AC-PLU-001 — Criar uma conta local

**Dado** que um administrador autenticado e autorizado informa dados válidos<br>
**Quando** solicita provisionar uma conta<br>
**Então** a conta local é criada e não recebe privilégio administrativo por padrão

### AC-PLU-002 — Exigir troca da senha temporária

**Dado** que a conta foi criada com senha temporária<br>
**Quando** o usuário conclui o primeiro login<br>
**Então** deve escolher uma senha nova antes de acessar funcionalidades normais

### AC-PLU-003 — Impedir cadastro sem autorização

**Dado** que o ator não é administrador autorizado<br>
**Quando** tenta criar ou desativar uma conta<br>
**Então** a operação é recusada e nenhuma conta é alterada

### AC-PLU-004 — Impedir login duplicado

**Dado** que já existe conta com o identificador informado<br>
**Quando** o administrador solicita outra conta com o mesmo identificador<br>
**Então** a duplicação é recusada sem revelar dados sensíveis da conta existente

### AC-PLU-005 — Desativar uma conta

**Dado** que o administrador seleciona uma conta elegível<br>
**Quando** confirma a desativação<br>
**Então** a conta não consegue iniciar novas sessões e os dados associados seguem a política de retenção aprovada

## Regras de domínio aplicáveis

- Identidade autenticada e autorização são conferidas no backend.
- Conta local é provisionada pelo administrador; cadastro público não existe.

## Classes de domínio afetadas

| Classe | Responsabilidade/invariante | Critérios/regras |
|---|---|---|
| Nenhuma classe de domínio própria definida | Gestão de conta usa ASP.NET Core Identity; regras adicionais aguardam modelagem de conta | `AC-PLU-001` a `AC-PLU-005` |

## Rastreabilidade dos testes

| Critério/regra | Caso de uso relacionado | Nome(s) planejado(s) do teste/display name | Caminho no projeto de testes | Estado |
|---|---|---|---|---|
| `AC-PLU-001` a `AC-PLU-005` | Provisionamento local | `AC-PLU-001 — Criar uma conta local`<br>`AC-PLU-002 — Exigir troca da senha temporária`<br>`AC-PLU-003 — Impedir cadastro sem autorização`<br>`AC-PLU-004 — Impedir login duplicado`<br>`AC-PLU-005 — Desativar uma conta` | `tests/Modules/Accounts.Identity.Tests/Features/ProvisionLocalUser/` | Pendente da implementação |

## Dados e integrações

- Entradas: identificador, role permitida, senha temporária; campos mínimos e identificador ainda abertos.
- Dados criados/alterados: conta Identity, role e estado de troca obrigatória de senha.
- Efeitos: auditoria de operações administrativas sem registrar senha.
- Idempotência: impedir criação duplicada por identificador único.

## Requisitos não funcionais

- Segurança: usar mecanismos de Identity; nunca registrar senha temporária em texto puro.
- Autorização: acesso administrativo verificado no servidor; mudanças devem ser auditáveis.

## Questões em aberto

- Login será username, e-mail local sem envio ou outro identificador?
- Administrador pode definir qualquer role? Existe somente `Administrator` e `User` no MVP?
- Desativação invalida sessões já abertas? Há exclusão permanente no MVP?

## Decisões e referências

- [`docs/domain/context.md`](../../../../domain/context.md)
- [`docs/arquitetura-inicial.md`](../../../../arquitetura-inicial.md)
