# ADR-002 — APIs oficiais como caminho principal de descoberta

**Estado:** Substituído por [`ADR-003`](ADR-003-mvp-hibrido-e-fontes-html.md) em 2026-10-12<br>
**Data:** 2026-10-12<br>
**Decisor:** Usuário do projeto

## Contexto

O MVP precisa buscar produtos e preços em mais de uma loja. A decisão anterior de usar apenas HTML foi revista pelo usuário, que escolheu APIs como caminho principal. O sistema continua local, privado e sem contato com lojas por mensagens, formulários, chamados ou pedidos. O usuário também pediu configuração simples e segura de credenciais e que valores reais nunca sejam enviados ao GitHub.

Uma API oficial pode impor limites de finalidade, acesso, elegibilidade, campos, exibição e retenção. A existência de documentação ou endpoint não comprova que um comparador local possa guardar histórico de preços.

## Decisão

1. Usar como caminho principal somente APIs oficiais publicadas e operadas pela própria loja ou marketplace, por conectores independentes.
2. APIs de vitrine fornecidas por plataformas de e-commerce, ainda que específicas ao domínio de uma loja, ficam fora do escopo aprovado. Também ficam fora agregadores/provedores centrais que devolvam dados de várias lojas. A interface informa quais fontes foram consultadas; não promete cobertura de toda a Internet.
3. Cada conector passa por uma aprovação própria baseada em documentação e termos públicos para o uso pretendido, elegibilidade, autenticação, operações de leitura, campos, cotas, custos, apresentação e retenção do histórico.
4. Operações que criem ou alterem contas, anúncios, pedidos ou transações ficam fora do escopo. O sistema não contata lojas por mensagens, formulários, chamados ou pedidos de autorização.
5. APIs oficiais são a prioridade. A decisão anterior de permitir HTML público cuja coleta esteja expressamente autorizada permanece válida como caminho secundário, sujeito ao mesmo gate individual; nenhum domínio foi aprovado na triagem atual. Feeds não estão aprovados.
6. Credenciais exigidas são configuradas localmente, sem alteração de código ou arquivos versionados, e guardadas fora do repositório em mecanismo protegido pelo sistema operacional. Valores nunca entram em GitHub, logs, telemetria, argumentos de processo ou UI depois de salvos.

## Alternativas consideradas

- **HTML/crawling como método principal:** a estratégia HTML-only anterior foi substituída. Fontes HTML aprovadas podem continuar como complemento secundário, sem assumir que algum site esteja elegível.
- **API central de agregador/provedor de dados:** não escolhida como fonte do MVP; criaria concentração de fornecedor e condições próprias de uso/armazenamento.
- **API de vitrine fornecida por plataforma de e-commerce:** não escolhida, mesmo quando o endpoint é específico ao domínio da loja, pois o usuário limitou APIs às que são próprias da loja/marketplace.
- **Misturar API e HTML desde o início:** HTML pode ser considerado somente como complemento quando um domínio passar pelo gate de permissão expressa; nenhuma fonte está aprovada hoje.

## Consequências

- `WebDiscovery` coordena consultas somente de leitura e normaliza APIs oficiais próprias das lojas/marketplaces. Interfaces de storefront fornecidas por plataformas de e-commerce são excluídas. HTML público permanece um complemento condicionado ao gate de permissão expressa já definido.
- Falhas, credenciais, limites e disponibilidade são isolados por fonte; os resultados podem ser parciais.
- Só persistimos campos e históricos quando a fonte permite esse armazenamento. Se a política da API impedir retenção compatível com o produto, ela permanece desabilitada.
- A estratégia API-first e a restrição a APIs próprias das lojas/marketplaces foram aprovadas em 12/10/2026, mas nenhuma API ou domínio está aprovado individualmente para uso e retenção atuais. A segunda rodada encontrou interfaces públicas de vitrine documentadas para Tray, Jumpseller, VTEX, Shopify e WooCommerce; elas ficam fora do escopo por serem fornecidas por plataformas. A Pichau permanece sem documentação pública oficial de API localizada para busca de ofertas por consumidor; a API da Loja ASSIST pesquisada não fornece preço observável de forma consistente. Consultar `docs/research/validacao-fontes-fase-0.md`.
- O roadmap deve reavaliar a viabilidade se não houver pelo menos três fontes independentes aptas a suportar o MVP, sem implementar coleta antes dessa decisão.

## Revisão

Este ADR registra a decisão anterior API-first para manter o histórico de planejamento. A orientação vigente está no ADR-003. Não usar este ADR como autorização para consultar APIs.
