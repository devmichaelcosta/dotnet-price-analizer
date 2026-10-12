# Especificação — ManageSourceConnectors

**Estado:** Rascunho condicional — não compõe o MVP manual; nenhum domínio foi aprovado.<br>
**Módulo:** Operations<br>
**Responsável pelo domínio:** Operação e segurança

> Só detalhar/implementar após a aprovação de ao menos uma spec incremental de domínio HTML. A direção vigente não permite APIs, feeds, agregadores ou integrações de terceiros como fonte de dados.

## Problema e objetivo

Permitir que o administrador local veja o estado de conectores HTML que foram incluídos na aplicação após revisão documental individual do domínio e spec aprovada.

## Atores e autorização

- Ator: administrador autenticado local.
- Somente conectores HTML incorporados e aprovados podem ser habilitados.
- Usuário comum pode ver quais domínios foram consultados, sem alterar configuração global.

## Contexto e regras de domínio

- `DR-014`: somente HTML público do domínio da loja pode ser fonte automatizada futura.
- `DR-015`: evidência expressa para coleta, campos, exibição e retenção é obrigatória; ambiguidade mantém domínio desabilitado.
- `DR-017`: operação automatizada é somente leitura; sem contato ou alteração de dados da loja.

## Escopo

### Incluído, se aprovado em incremento futuro

- Listar conectores HTML conhecidos pela versão instalada, estado de aprovação e limites.
- Habilitar ou desabilitar conector cujo registro de evidência esteja válido.
- Isolar estado, falhas e limites por domínio.

### Fora de escopo

- Adicionar código, script, extensão ou URL arbitrária pelo usuário.
- Habilitar uma fonte sem evidência documental expressa ou fora do domínio aprovado.
- APIs, feeds, agregadores, páginas autenticadas ou operações de escrita.
- Configuração de credenciais de lojas.

## Comportamento esperado

O administrador escolhe somente um conector HTML incorporado à aplicação. A ativação é recusada quando a evidência aprovada estiver ausente, ambígua ou incompatível com uso/campos/retenção. Desabilitar a fonte impede novas consultas e jobs; dados existentes seguem a política documentada para aquele domínio.

## Cenários de aceite

### AC-MSC-001 — Exibir somente conectores incorporados

**Dado** que a versão instalada conhece conectores HTML aprovados ou pendentes<br>
**Quando** o administrador abre fontes<br>
**Então** vê somente os domínios incorporados e seu estado/capacidades

### AC-MSC-002 — Bloquear domínio sem permissão expressa

**Dado** que um domínio não passou pelo gate ou sua evidência não permite expressamente o uso pretendido<br>
**Quando** o administrador tenta habilitá-lo ou um job tenta consultá-lo<br>
**Então** a operação é recusada e nenhuma requisição é enviada

### AC-MSC-003 — Ativar somente domínio aprovado

**Dado** que há evidência válida, revisão registrada e spec específica aprovada<br>
**Quando** o administrador habilita o conector<br>
**Então** novas consultas ficam limitadas aos caminhos HTML públicos aprovados e aos limites registrados

### AC-MSC-004 — Desabilitar consultas futuras

**Dado** que um conector está habilitado<br>
**Quando** o administrador o desabilita<br>
**Então** nenhum novo job consulta o domínio e jobs pendentes seguem política documentada

### AC-MSC-005 — Isolar indisponibilidade por domínio

**Dado** que um domínio está indisponível<br>
**Quando** há uma consulta de outros domínios habilitados<br>
**Então** a falha fica associada somente ao domínio afetado

### AC-MSC-006 — Auditar mudança sem segredos

**Dado** que o administrador habilita ou desabilita um conector<br>
**Quando** a alteração é concluída<br>
**Então** instante UTC, conta administradora, domínio, estado e resultado são registrados sem segredos

## Regras de domínio aplicáveis

- `DR-014`, `DR-015`, `DR-017`.

## Classes de domínio afetadas

| Classe/conceito | Responsabilidade/invariante | Critérios/regras |
|---|---|---|
| `SourceConnector` (proposta) | Estado e política de um conector HTML incorporado, habilitado só após todos os gates | `AC-MSC-001` a `AC-MSC-006`, `DR-014`, `DR-015`, `DR-017` |

## Rastreabilidade dos testes

| Critério/regra | Caso de uso/classe | Nome(s) planejado(s) do teste/display name | Caminho no projeto de testes | Estado |
|---|---|---|---|---|
| `AC-MSC-001` a `AC-MSC-006` | Gestão futura de conectores | `AC-MSC-001 — Exibir somente conectores incorporados`<br>`AC-MSC-002 — Bloquear domínio sem permissão expressa`<br>`AC-MSC-003 — Ativar somente domínio aprovado`<br>`AC-MSC-004 — Desabilitar consultas futuras`<br>`AC-MSC-005 — Isolar indisponibilidade por domínio`<br>`AC-MSC-006 — Auditar mudança sem segredos` | `tests/Modules/Operations.Tests/Features/ManageSourceConnectors/` | Pendente de fonte elegível/spec aprovada |
| `AC-MSC-001` a `AC-MSC-006`, `DR-014`/`DR-015`/`DR-017` | `SourceConnector` | `AC-MSC-001 — SourceConnector — Exibir somente conectores incorporados`<br>`AC-MSC-002 — SourceConnector — Bloquear domínio sem permissão expressa`<br>`AC-MSC-003 — SourceConnector — Ativar somente domínio aprovado`<br>`AC-MSC-004 — SourceConnector — Desabilitar consultas futuras`<br>`AC-MSC-005 — SourceConnector — Isolar indisponibilidade por domínio`<br>`AC-MSC-006 — SourceConnector — Auditar mudança sem segredos` | `tests/Modules/Operations.Tests/Domain/SourceConnector/` | Pendente de fonte elegível/spec aprovada |

## Dados e integrações

- Entrada: identificador de conector incorporado e ação habilitar/desabilitar.
- Dados: estado, capacidades, evidência/revisão, limites e último resultado resumido.
- Efeitos: somente consulta HTML pública ao domínio aprovado, após ativação.
- Idempotência: repetir ação mantém estado solicitado sem duplicar jobs.

## Requisitos não funcionais

- Somente administrador local pode alterar configuração global.
- Mostrar domínio, estado e data da revisão; não mostrar segredos.
- Mudança de termos/revogação deve permitir desabilitação rápida e auditável.

## Questões em aberto

- Como distribuir atualização de evidência dos termos e quem confirma sua revisão?
- Ao desabilitar, jobs pendentes são cancelados ou aguardam nova aprovação?
- Se termos mudarem e exigirem remoção do histórico, qual fluxo deve ocorrer?

## Decisões e referências

- [`docs/domain/context.md`](../../../domain/context.md)
- [`ADR-003 — Núcleo manual e automação HTML condicionada`](../../../decisions/ADR-003-mvp-hibrido-e-fontes-html.md)
- [`docs/research/validacao-fontes-fase-0.md`](../../../research/validacao-fontes-fase-0.md)
