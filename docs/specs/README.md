# Especificações do produto

O projeto seguirá Spec-Driven Development (SDD): comportamento e regras aprovados serão descritos em artefatos versionáveis antes da implementação. O código realiza a especificação; não é a única fonte do conhecimento do produto.

## Artefatos e localização

- `docs/domain/context.md` mantém linguagem ubíqua, conceitos, invariantes compartilhadas, limites e perguntas ainda abertas do domínio.
- `docs/specs/<Modulo>/<CasoDeUso>/spec.md` define uma capacidade/caso de uso. Os artefatos específicos dessa fatia ficam juntos no diretório dela, em alinhamento com a regra de co-localização do código.
- `docs/specs/<Modulo>/<CasoDeUso>/plan.md` registra desenho técnico e impactos em módulos, dados, segurança e integrações, quando necessário.
- `docs/specs/<Modulo>/<CasoDeUso>/tasks.md` decompõe a especificação aceita em entregas implementáveis e revisáveis.
- Decisões transversais devem ser registradas separadamente como ADRs em `docs/decisions/`, com contexto, alternativas, decisão, estado e consequências.
- Testes ficam em projetos separados dos assemblies de produção, mas espelham módulo e caso de uso. Exemplo: `tests/Modules/PriceTracking.Tests/Features/StartTracking/`; todos os testes daquele caso de uso ficam juntos nessa pasta. Cada classe de domínio tem testes associados, agrupados por módulo sob `Domain/`.

Os arquivos `plan.md` e `tasks.md` só são criados quando a especificação os exigir. Não criar documentação vazia para cumprir um ritual.

## Inventário preliminar de specs do MVP

**Estado:** proposta para decomposição; ainda precisa de revisão e aprovação. Os nomes são identificadores provisórios de pastas/use cases.

| Módulo | Spec/caso de uso proposto | Resultado esperado |
|---|---|---|
| Accounts/Identity | `BootstrapLocalAdministrator` | Inicializar com segurança o administrador da instalação local. |
| Accounts/Identity | `ProvisionLocalUser` | Administrador cria/desativa contas locais; não há cadastro público. |
| Accounts/Identity | `SignIn` | Autenticar um usuário provisionado e estabelecer sessão segura do Blazor. |
| Accounts/Identity | `ResetLocalUserCredentials` | Administrador redefine credenciais usando fluxos suportados pelo Identity. |
| Accounts/Identity | `ManageMfa` | Configurar, validar e recuperar segundo fator, se a política MFA do MVP o exigir. |
| PriceTracking | `StartTracking` | Criar acompanhamento a partir de texto ou EAN/GTIN e solicitar busca assíncrona. |
| WebDiscovery | `FindProductCandidates` | Pesquisar o índice próprio e devolver candidatos com origem, dados observados e estado da busca. |
| PriceTracking | `ReviewAndConfirmOffers` | Exibir candidatos e associar à consulta somente as ofertas confirmadas pelo usuário. |
| PriceTracking | `ListTrackedProducts` | Listar acompanhamentos da conta autenticada, respeitando propriedade. |
| PriceTracking | `PauseOrResumeTracking` | Pausar ou reativar atualizações de um acompanhamento. |
| WebDiscovery | `DiscoverAndIndexPages` | Descobrir e indexar páginas elegíveis nos domínios habilitados. |
| WebDiscovery | `ExtractAndNormalizeOffer` | Extrair e normalizar produto/oferta sem misturar variantes ou vendedores. |
| PriceTracking | `RequestManualRefresh` | Enfileirar atualização manual de ofertas confirmadas e retornar o estado do job. |
| PriceTracking | `RecordPriceObservation` | Registrar leitura datada, fonte, moeda, preço do item e disponibilidade. |
| PriceAnalysis | `GetPriceHistory` | Consultar histórico de uma oferta autorizada em um intervalo. |
| PriceAnalysis | `ComparePriceVariation` | Calcular diferença absoluta/percentual entre observações comparáveis. |
| Operations | `GetCollectionJobStatus` | Consultar estado e falhas de jobs sem revelar dados de outra conta. |

O inventário será reduzido, dividido ou ampliado durante a análise das regras, fronteiras dos módulos e fluxos. Especificações são necessárias antes de implementar; esta tabela, sozinha, não é autorização para codificar.

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
