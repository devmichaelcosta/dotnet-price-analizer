# Especificação — BootstrapLocalAdministrator

**Estado:** Rascunho — 2026-10-12<br>
**Módulo:** Accounts/Identity<br>
**Responsável pelo domínio:** Produto e segurança

## Problema e objetivo

Inicializar a instalação local criando a primeira conta administradora sem senha padrão, cadastro público ou exposição da senha.

## Atores e autorização

- Ator: operador com acesso ao terminal do host.
- A autenticação web ainda não existe durante o bootstrap.
- A operação só é permitida enquanto não houver administrador inicializado.

## Contexto e regras de domínio

- Regras confirmadas: sem cadastro público; primeiro administrador por comando local de uso único; senha informada sem valor padrão e sem exposição em argumentos, logs ou arquivos versionados.
- Hipótese: o comando solicita login e senha interativamente no terminal. O identificador de login ainda precisa ser decidido.

## Escopo

### Incluído

- Comando local de bootstrap da primeira conta administradora.
- Verificação segura de que o bootstrap ainda não foi concluído.

### Fora de escopo

- Recuperação posterior de credenciais ou MFA.
- Criação de usuários adicionais.
- Escrita de segredos em Git, configuração versionada ou logs.

## Comportamento esperado

O operador inicia o comando no host. O comando verifica a instalação e solicita os dados necessários sem ecoar a senha. Em caso de sucesso, cria uma conta administradora pelo ASP.NET Core Identity e registra que o bootstrap foi concluído. Uma tentativa posterior informa que o bootstrap já ocorreu e não altera a conta existente.

## Cenários de aceite

### AC-BLA-001 — Criar o primeiro administrador

**Dado** que a instalação ainda não foi inicializada<br>
**Quando** o operador informa os dados válidos pelo fluxo local<br>
**Então** a conta é criada como administradora usando o fluxo suportado pelo Identity e o bootstrap passa a constar como concluído

### AC-BLA-002 — Impedir reutilização do bootstrap

**Dado** que já existe administrador inicializado<br>
**Quando** o comando é executado novamente<br>
**Então** a operação é recusada e nenhuma conta ou credencial existente é alterada

### AC-BLA-003 — Não expor a senha

**Dado** que o operador fornece uma senha no bootstrap<br>
**Quando** o comando é executado<br>
**Então** a senha não aparece em argumentos, saída do terminal, logs, telemetria nem arquivo versionado

### AC-BLA-004 — Recusar dados inválidos sem inicializar parcialmente

**Dado** que o login ou senha não atende às validações do Identity<br>
**Quando** o operador tenta concluir o bootstrap<br>
**Então** a conta não é criada parcialmente, erros não revelam segredos e uma nova tentativa pode ser feita

## Regras de domínio aplicáveis

- A decisão de bootstrap único e senha sem valor padrão está registrada no contexto do domínio e no `AGENTS.md`.

## Classes de domínio afetadas

| Classe | Responsabilidade/invariante | Critérios/regras |
|---|---|---|
| Nenhuma classe de domínio própria definida | O fluxo usa ASP.NET Core Identity; não criar abstração de domínio sem regra demonstrada | `AC-BLA-001` a `AC-BLA-004` |

## Rastreabilidade dos testes

| Critério/regra | Caso de uso relacionado | Nome(s) planejado(s) do teste/display name | Caminho no projeto de testes | Estado |
|---|---|---|---|---|
| `AC-BLA-001` | Bootstrap local | `AC-BLA-001 — Criar o primeiro administrador` | `tests/Modules/Accounts.Identity.Tests/Features/BootstrapLocalAdministrator/` | Pendente da implementação |
| `AC-BLA-002` | Bootstrap local | `AC-BLA-002 — Impedir reutilização do bootstrap` | `tests/Modules/Accounts.Identity.Tests/Features/BootstrapLocalAdministrator/` — repetição e concorrência do comando | Pendente da implementação |
| `AC-BLA-003` | Bootstrap local | `AC-BLA-003 — Não expor a senha` | `tests/Modules/Accounts.Identity.Tests/Features/BootstrapLocalAdministrator/` — verificação de saída/logs | Pendente da implementação |
| `AC-BLA-004` | Bootstrap local | `AC-BLA-004 — Recusar dados inválidos sem inicializar parcialmente` | `tests/Modules/Accounts.Identity.Tests/Features/BootstrapLocalAdministrator/` — validação e atomicidade | Pendente da implementação |

## Dados e integrações

- Entradas: identificador de login e senha digitados localmente; formato de login pendente de decisão.
- Dados criados: usuário de Identity, role administrativa e estado de bootstrap concluído.
- Efeitos: persistência local no PostgreSQL.
- Idempotência: somente uma inicialização pode vencer, inclusive sob execução concorrente.

## Requisitos não funcionais

- Segurança e privacidade: usar Identity; nunca implementar hash próprio; senha somente em memória durante o fluxo e sem log.
- Operação: falhar de modo seguro se o armazenamento não puder confirmar criação atômica.

## Questões em aberto

- Qual identificador será usado no login: nome de usuário, e-mail local sem envio, ou outro?
- Qual será a política de senha do Identity?
- Como o comando verificará atomicamente que outra instância não concluiu bootstrap ao mesmo tempo?

## Decisões e referências

- [`docs/domain/context.md`](../../../../domain/context.md)
- [`docs/arquitetura-inicial.md`](../../../../arquitetura-inicial.md)
