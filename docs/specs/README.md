# Especificações do produto

O projeto seguirá Spec-Driven Development (SDD): comportamento e regras aprovados serão descritos em artefatos versionáveis antes da implementação. O código realiza a especificação; não é a única fonte do conhecimento do produto.

## Artefatos e localização

- `docs/domain/context.md` mantém linguagem ubíqua, conceitos, invariantes compartilhadas, limites e perguntas ainda abertas do domínio.
- `docs/specs/<Modulo>/<CasoDeUso>/spec.md` define uma capacidade/caso de uso. Os artefatos específicos dessa fatia ficam juntos no diretório dela, em alinhamento com a regra de co-localização do código.
- `docs/specs/<Modulo>/<CasoDeUso>/plan.md` registra desenho técnico e impactos em módulos, dados, segurança e integrações, quando necessário.
- `docs/specs/<Modulo>/<CasoDeUso>/tasks.md` decompõe a especificação aceita em entregas implementáveis e revisáveis.
- Decisões transversais devem ser registradas separadamente como ADRs em `docs/decisions/`, com contexto, alternativas, decisão, estado e consequências.
- Testes ficam em projetos separados dos assemblies de produção, mas espelham módulo e caso de uso. Exemplo: `tests/Modules/PriceTracking.Tests/Features/StartTracking/`; todos os testes daquele caso de uso ficam juntos nessa pasta. Cada classe de domínio tem testes associados, agrupados por módulo sob `Domain/`.
- Planejar um teste por critério `AC-...` como padrão. O display name segue `AC-XXX-001 — <título do critério>` e o método usa `AC_XXX_001_<Behavior>`; assim, o ID permite localizar a cobertura sem depender da ordem dos testes.
- Cada linha da matriz de rastreabilidade deve apontar o nome/display name planejado e o caminho do teste. É permitido agrupar IDs numa linha somente quando houver um teste planejado por ID no mesmo diretório, conforme a convenção acima. Se um teste cobrir vários critérios, listar todos os IDs no nome e justificar a cobertura conjunta.
- Cada classe concreta de domínio aparece numa linha própria de matriz com seus AC/DR, nomes planejados de testes e pasta `tests/Modules/<Modulo>.Tests/Domain/<Classe>/` do módulo proprietário. Classes de outro módulo são cobertas no projeto de testes do módulo dono, não por cópia de classe.
- Uma spec em rascunho pode ter testes apenas planejados. O status deve continuar pendente até os testes serem implementados e aprovados; critérios substituídos são explicitamente marcados como tais e não geram teste de comportamento.
- Nomes de classes de domínio nas specs podem ser conceitos/propostas até aprovação do desenho; se uma classe concreta for introduzida, seus testes ficam em `tests/Modules/<Modulo>.Tests/Domain/<Classe>/`.

Os arquivos `plan.md` e `tasks.md` só são criados quando a especificação os exigir. Não criar documentação vazia para cumprir um ritual.

## Inventário preliminar de specs

**Estado:** proposta para decomposição; ainda precisa de revisão e aprovação. A direção C foi escolhida pelo usuário em 2026-10-12: MVP manual primeiro, automação condicionada a uma aprovação individual de domínio. Os nomes são identificadores provisórios de pastas/use cases.

### Núcleo do MVP manual

| Módulo | Spec/caso de uso proposto | Resultado esperado |
|---|---|---|
| Accounts/Identity | `BootstrapLocalAdministrator` | Inicializar com segurança o administrador local por comando de uso único. |
| Accounts/Identity | `RecoverLocalAdministratorCredential` | Recuperar credencial administrativa por comando no host, sem e-mail. |
| Accounts/Identity | `ProvisionLocalUser` | Administrador provisiona contas locais, sem cadastro público. |
| Accounts/Identity | `SignIn` | Autenticar conta provisionada e estabelecer sessão segura do Blazor. |
| Accounts/Identity | `ResetLocalUserCredentials` | Administrador define senha temporária pelo Identity e exige troca no próximo login. |
| Accounts/Identity | `ManageMfa` | Administrar TOTP e códigos de recuperação conforme política aprovada. |
| Accounts/Identity | `ResetMfaFromHost` | Recuperar MFA de qualquer conta por comando local auditado. |
| ProductCatalog | `ResolveCanonicalProduct` | Registrar/resolver identidade do produto com dados informados pelo usuário, sem candidato externo. |
| PriceTracking | `StartTracking` | Criar acompanhamento privado por texto e/ou EAN/GTIN, sem job de busca. |
| PriceTracking | `AddManualOffer` **(nova)** | Registrar oferta de loja manualmente e a observação inicial, sem acessar a URL. |
| PriceTracking | `RecordPriceObservation` | Registrar alteração manual de preço/disponibilidade, com autoria e origem identificadas. |
| PriceTracking | `ListTrackedProducts` | Listar acompanhamentos, ofertas e preço resumido da conta autenticada. |
| PriceTracking | `RemoveTracking` | Remover acompanhamento e dados privados vinculados. |
| PriceAnalysis | `GetPriceHistory` | Consultar histórico manual de uma oferta em intervalo. |
| PriceAnalysis | `ComparePriceVariation` | Calcular variação temporal de uma oferta. |
| PriceAnalysis | `CompareCurrentOffers` **(nova)** | Comparar os últimos preços informados de ofertas compatíveis, sempre com data e sem alegar atualidade na loja. |

### Automação futura condicionada

| Módulo | Spec/caso de uso proposto | Condição |
|---|---|---|
| WebDiscovery | `FindProductCandidates` | Desabilitado até uma spec de busca HTML e gate aprovado por domínio. |
| PriceTracking | `ReviewAndConfirmOffers` | Necessário quando uma fonte HTML aprovada devolver candidatos. |
| PriceTracking | `PauseOrResumeTracking` | Reavaliar quando houver atualização automática agendada. |
| PriceTracking | `RequestManualRefresh` | Reavaliar quando houver um conector HTML aprovado para consulta manual. |
| WebDiscovery | `DiscoverAndIndexPages` | Somente HTML público com permissão expressa; nenhum domínio aprovado atualmente. |
| WebDiscovery | `ExtractAndNormalizeOffer` | Somente para domínio HTML individual aprovado; sem APIs/feed como fallback. |
| PriceTracking | `RecordPriceObservation` | Reuso futuro para dados coletados só após spec aprovada; origem automática deve ficar distinta da manual. |
| Operations | `GetCollectionJobStatus` | Apenas quando existirem jobs automatizados aprovados. |
| Operations | `ManageSourceConnectors` | Apenas conectores HTML aprovados e instalados; sem fontes API/feed/terceiro. |
| Operations | `ConfigureSourceCredentials` | Fora do escopo vigente; só reabrir após mudança explícita que permita fontes autenticadas. |

Notificações seguem fora do escopo. O Worker e a fila de coleta também são condicionais à necessidade de uma fonte HTML aprovada. A classificação das specs não é autorização para implementar: o usuário precisa aprovar o roadmap e o conjunto de specs do MVP.

O inventário será reduzido, dividido ou ampliado durante a análise das regras, fronteiras dos módulos e fluxos. Especificações são necessárias antes de implementar; esta tabela, sozinha, não é autorização para codificar.

Cada spec usa IDs estáveis no formato `AC-<SIGLA-DO-USE-CASE>-###` (por exemplo, `AC-SLI-001` para `SignIn`) para evitar colisões entre arquivos. IDs publicados não devem ser renumerados; critérios removidos ficam marcados como substituídos.

## Ciclo de mudança

1. Delimitar problema, usuários, objetivo e fora de escopo.
2. Consultar o contexto do domínio e registrar perguntas/hipóteses; atualizá-lo se surgir uma regra compartilhada nova.
3. Escrever a especificação do caso de uso, incluindo autorização, validação, estados, erros, dados, integrações e critérios de aceite verificáveis. Dar IDs estáveis aos critérios (`AC-...`) e referenciar as regras de domínio aplicáveis (`DR-...`).
4. Descrever cenários principais, alternativos e de falha em linguagem observável, preferencialmente Given/When/Then.
5. Revisar dependências e desenho dos módulos, impactos de segurança, persistência, operação e privacidade; registrar ADR quando houver decisão transversal relevante.
6. Depois da especificação aprovada, criar plano técnico e tarefas dentro da pasta do caso de uso.
7. Iniciar implementação somente quando o planejamento/roadmap e o conjunto de especificações do MVP estiverem documentados e aprovados pelo usuário; para cada incremento posterior, sua especificação também deve estar aprovada antes do código.
8. Identificar na spec as classes de domínio afetadas e relacioná-las a critérios `AC-...` e regras `DR-...`. Criar ou atualizar testes derivados dessas referências: toda classe de domínio tem testes para os critérios/regras que lhe cabem, e todo caso de uso tem testes para seus cenários de aceite e falha.
9. Verificar a entrega contra a especificação, exigir que todos os critérios aplicáveis estejam cobertos por testes aprovados e atualizar os artefatos se o comportamento ou o conhecimento de domínio mudar.

## Estrutura de uma especificação

Use [`_template.md`](_template.md) como base. Cada especificação deve deixar claro o que foi decidido, o que é hipótese, o que ficou fora e como o resultado será reconhecido. Critérios devem descrever comportamento, não amarrar a especificação desnecessariamente a detalhes internos da implementação. A matriz de rastreabilidade liga cada critério e regra a pelo menos um teste.

## Estado dos artefatos

Marcar cada documento como **Rascunho**, **Em revisão**, **Aprovado** ou **Substituído** e indicar data/versão quando aprovado. Uma especificação em rascunho não autoriza implementação. Alterações em requisito aprovado devem atualizar a especificação e identificar impactos antes de mudar código.
