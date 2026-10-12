# Revisão de escopo do MVP — decisão híbrida

**Estado:** direção de escopo escolhida; roadmap e specs detalhados ainda aguardam aprovação.<br>
**Data:** 2026-10-12<br>
**Decisão do usuário:** opção C — núcleo manual primeiro; conectores automáticos permanecem desabilitados até passarem por um gate individual.

## Contexto e evidência

A pesquisa documental anterior avaliou APIs de Mercado Livre, Amazon, Shopee, AliExpress, Magalu, KaBuM!, Grupo Casas Bahia, Carrefour, eBay e Etsy. Nenhuma foi aprovada para o uso e a retenção de histórico pretendidos. A triagem de páginas HTML também não aprovou nenhum domínio. Os achados e referências primárias estão em [`validacao-fontes-fase-0.md`](validacao-fontes-fase-0.md).

A diretriz vigente para fontes, registrada no `AGENTS.md`, é mais restrita: não usar APIs, feeds, agregadores ou integrações de terceiros como fonte de dados. Uma futura coleta automatizada pode usar somente páginas HTML públicas cuja coleta esteja expressamente permitida pelos termos aplicáveis. Não contatar lojas. Um endpoint ou página acessível não demonstra autorização.

## Decisão de escopo

O MVP inicial será híbrido em duas etapas:

1. **Núcleo manual:** usuário autenticado cadastra um produto, registra ofertas de lojas e informa observações de preço. O sistema armazena a origem manual, compara ofertas compatíveis entre lojas e apresenta o histórico temporal e a variação.
2. **Automação condicionada:** descoberta e atualização por HTML não estarão habilitadas no MVP manual. Um domínio só poderá receber um conector em incremento futuro, depois de documentação de termos públicos provar expressamente que o uso local, os campos extraídos/apresentados e a retenção pretendida são permitidos. Um conector que não passar no gate permanece ausente ou desabilitado.

O usuário escolheu essa direção; isso **não aprova** os detalhes das novas specs, o roadmap completo nem o início da implementação. Os artefatos revisados continuam sujeitos à aprovação explícita do pacote do MVP, conforme o SDD.

## Limites preservados

- Aplicação local, com várias contas provisionadas pelo administrador, acesso apenas pela máquina hospedeira e dados privados por conta.
- Nenhuma comunicação com lojas; nenhuma operação de escrita em contas, anúncios, pedidos ou transações.
- Nenhuma consulta automatizada no fluxo manual. Uma URL informada pelo usuário é referência textual e não autoriza nem dispara uma requisição HTTP.
- Fontes futuras: somente HTML público com permissão expressa nos termos; `robots.txt` isoladamente não é autorização. Sem APIs, feeds, agregadores ou integrações de terceiros como fonte.
- Entrada do produto por texto e/ou EAN/GTIN; preço do item sem frete; histórico removido junto com o acompanhamento.
- Observações informadas pela pessoa devem ser identificadas como manuais e não podem ser apresentadas como verificadas pela loja.

## Escopo das capacidades

| Capacidade | MVP manual | Etapa futura condicionada |
|---|---|---|
| Cadastro de produto e acompanhamento | Incluído | — |
| Cadastro manual de oferta de uma loja | Incluído | — |
| Registro manual de preço e disponibilidade | Incluído | — |
| Comparação entre ofertas e variação temporal | Incluído | — |
| Busca de candidatos em páginas de lojas | — | Somente depois de aprovar domínio HTML específico |
| Extração e normalização automatizada | — | Somente depois do gate individual do domínio |
| Agendamento e atualização automática | — | Somente após fonte elegível e spec incremental aprovada |
| API, feed, agregador ou serviço terceiro como fonte | Fora do escopo | Não planejado; exige mudança explícita de requisito |

## Impacto nas specs

- `PriceTracking/StartTracking`: cadastrar acompanhamento a partir dos dados do produto, sem criar job de busca.
- `ProductCatalog/ResolveCanonicalProduct`: resolver/cadastrar o produto com dados informados pelo usuário, sem candidato externo.
- `PriceTracking/AddManualOffer` (nova): registrar uma oferta manual associada ao produto acompanhado e sua observação inicial.
- `PriceTracking/RecordPriceObservation`: registrar atualizações manuais com autoria e proveniência explícitas; processamento automático fica fora do escopo atual.
- `PriceTracking/ListTrackedProducts`, `PriceTracking/RemoveTracking`, `PriceAnalysis/GetPriceHistory`, `PriceAnalysis/ComparePriceVariation` e uma nova `PriceAnalysis/CompareCurrentOffers`: compõem o núcleo de uso manual.
- `WebDiscovery/*`, configuração/gestão de conectores e jobs de coleta ficam condicionais para uma etapa posterior ao gate por domínio.
- Atualizar o contexto, roadmap, arquitetura, inventário de specs e backlog local para refletir essa separação.

## Questões ainda abertas

As decisões detalhadas para o fluxo manual e outros aspectos do MVP estão em [`docs/decisions/registro-fase-0.md`](../decisions/registro-fase-0.md). Em especial, precisam ser fechados os campos obrigatórios de produto/oferta, como informar a loja e URL, qual horário será usado para a observação, as regras de comparação entre ofertas e os períodos do histórico.

## Referências

- [`docs/domain/context.md`](../domain/context.md)
- [`docs/roadmap.md`](../roadmap.md)
- [`docs/specs/README.md`](../specs/README.md)
- [`docs/decisions/ADR-003-mvp-hibrido-e-fontes-html.md`](../decisions/ADR-003-mvp-hibrido-e-fontes-html.md)
- [`docs/research/validacao-fontes-fase-0.md`](validacao-fontes-fase-0.md)
