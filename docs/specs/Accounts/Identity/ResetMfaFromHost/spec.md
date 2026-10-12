# Especificação — ResetMfaFromHost

**Estado:** Rascunho — 2026-10-12<br>
**Módulo:** Accounts/Identity<br>
**Responsável pelo domínio:** Produto e segurança

## Problema e objetivo

Recuperar o acesso MFA de qualquer conta quando a pessoa perdeu o autenticador e todos os códigos de recuperação, sem depender de e-mail.

## Atores e autorização

- Ator: qualquer pessoa com acesso local ao terminal do host.
- O comando é local, não uma rota Web/API nem tela do aplicativo.
- Conta alvo: qualquer conta local identificada pelo login informado.

## Contexto e regras de domínio

- A decisão aprovada exige login informado, exibição da conta correspondente e confirmação explícita.
- O reset é auditado com instante UTC, conta alvo, usuário do sistema operacional executor e resultado, sem segredos.
- Após reset, a conta deve cadastrar TOTP novamente antes do acesso normal se MFA for obrigatória ou estiver habilitada.

## Escopo

### Incluído

- Remover estado MFA e códigos de recuperação para uma conta selecionada no terminal.
- Exigir novo cadastro TOTP para restaurar acesso normal.

### Fora de escopo

- Reset de senha, mudança de role ou reset por interface web.
- Envio de e-mail ou contato com provedor externo.

## Comportamento esperado

O comando solicita login, localiza a conta, mostra ao operador a conta correspondente e exige confirmação explícita. Só então limpa o estado MFA e recovery codes usando mecanismos suportados pelo Identity. O comando grava auditoria sem segredos. Se não houver confirmação, conta ou persistência, não altera MFA.

## Cenários de aceite

### AC-RMF-001 — Exigir confirmação do alvo

**Dado** que o operador informa login de uma conta existente<br>
**Quando** o comando exibe a conta<br>
**Então** o reset só ocorre após confirmação explícita

### AC-RMF-002 — Reset local da MFA

**Dado** que o operador confirmou a conta-alvo<br>
**Quando** o reset termina com sucesso<br>
**Então** autenticador e códigos antigos deixam de validar e a conta precisa registrar TOTP novamente quando aplicável

### AC-RMF-003 — Cancelar sem alteração

**Dado** que o operador não confirma o reset<br>
**Quando** encerra o fluxo<br>
**Então** o estado MFA e códigos permanecem intactos

### AC-RMF-004 — Auditar sem segredo

**Dado** que o comando tenta executar um reset<br>
**Quando** registra o resultado<br>
**Então** a auditoria inclui instante UTC, conta alvo, identidade do sistema operacional e resultado, sem TOTP, seed ou recovery code

### AC-RMF-005 — Tratar conta inexistente

**Dado** que o login informado não corresponde a uma conta local<br>
**Quando** o comando tenta localizar o alvo<br>
**Então** nenhum estado é alterado, o resultado é auditado sem segredos e o comando encerra com falha

## Regras de domínio aplicáveis

- Recuperação de MFA de qualquer conta exclusivamente por comando local.
- Confirmação explícita e auditoria de resultado obrigatórias.

## Classes de domínio afetadas

| Classe | Responsabilidade/invariante | Critérios/regras |
|---|---|---|
| Nenhuma classe de domínio própria definida | Usar Identity para reset e um contrato de auditoria sem segredos | `AC-RMF-001` a `AC-RMF-005` |

## Rastreabilidade dos testes

| Critério/regra | Caso de uso relacionado | Nome(s) planejado(s) do teste/display name | Caminho no projeto de testes | Estado |
|---|---|---|---|---|
| `AC-RMF-001` a `AC-RMF-005` | Reset de MFA por host | `AC-RMF-001 — Exigir confirmação do alvo`<br>`AC-RMF-002 — Reset local da MFA`<br>`AC-RMF-003 — Cancelar sem alteração`<br>`AC-RMF-004 — Auditar sem segredo`<br>`AC-RMF-005 — Tratar conta inexistente` | `tests/Modules/Accounts.Identity.Tests/Features/ResetMfaFromHost/` | Pendente da implementação |

## Dados e integrações

- Entradas: login alvo e confirmação explícita no terminal.
- Dados alterados: estado TOTP, códigos de recuperação, sinalizador de novo cadastro e registro de auditoria.
- Auditoria: UTC, alvo, usuário do SO e resultado.
- Idempotência: novo reset da conta sem MFA continua seguro, mas deve ser auditado como resultado distinto.

## Requisitos não funcionais

- Comando sem senha ou segredo em argumento.
- Operação disponível mesmo sem API/Web, desde que o banco local esteja acessível.
- Não registrar seed, TOTP, recovery codes nem material de provisioning.

## Questões em aberto

- A auditoria deve guardar o login digitado quando a conta não existe? Deve haver limite/rotação dessa auditoria?
- O comando revoga sessões ativas imediatamente ou apenas impede novo login até recadastro?
- Como tratar reset sob indisponibilidade do banco?

## Decisões e referências

- [`docs/domain/context.md`](../../../../domain/context.md)
- [`docs/arquitetura-inicial.md`](../../../../arquitetura-inicial.md)
