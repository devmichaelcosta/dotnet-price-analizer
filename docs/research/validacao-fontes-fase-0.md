# Pesquisa preliminar de fontes — Fase 0

**Estado:** rascunho; análise documental/manual, não é autorização para coletar.  
**Consulta:** 11/10/2026. Revalidar termos, `robots.txt` e páginas antes de habilitar qualquer fonte.

## Objetivo e limite

Avaliar se páginas HTML públicas de diferentes lojas podem servir à descoberta e ao acompanhamento de preços. A decisão do produto é não usar APIs, feeds ou integrações de terceiros como fonte de dados. Esta análise não executou crawler, parser, automação de navegador nem coleta de produto. A leitura de `robots.txt` serve como sinal técnico de caminhos e regras declarados para agentes; não é, por si só, autorização ou interpretação jurídica dos termos.

## Achados iniciais

| Fonte candidata | Evidência consultada | O que a evidência indica | Estado preliminar |
|---|---|---|---|
| Magazine Luiza | [`robots.txt`](https://www.magazineluiza.com.br/robots.txt) | Há caminhos explicitamente desautorizados. A regra `User-agent: *` não mostra bloqueio geral de páginas de produto; regras específicas de `Googlebot` bloqueiam alguns formatos de busca. Isso não confirma permissão para nosso uso nem responde sobre retenção dos dados. | Candidata para análise adicional dos termos e de uma página de produto; não habilitada. |
| Mercado Livre | [Termos para Desenvolvedores](https://developers.mercadolivre.com.br/pt_br/termos-e-condicoes) e [`robots.txt`](https://www.mercadolivre.com.br/robots.txt) | Os termos do programa de desenvolvedores vedam robôs, spiders e scraping para acessar conteúdo fora do que é fornecido pelo programa. O `robots.txt` também define caminhos e atrasos por agente, mas não substitui a restrição contratual. | Fora do escopo atual: não usar coleta automatizada de HTML sem evidência de permissão para esse uso. |
| Amazon Brasil | [Condições de uso dos serviços Amazon](https://relay.amazon.com/terms) e [Políticas do Programa de Associados](https://associados.amazon.com.br/help/operating/policies/) | As condições publicadas vedam mineração de dados/robôs e exploração comercial de conteúdo sem consentimento expresso. Nas regras do Programa de Associados, a função de rastrear preços/alertas requer aprovação salvo autorização da Amazon; o conteúdo do programa tem restrições próprias de uso e armazenamento. O alcance exato de cada condição ao produto planejado precisa ser confirmado diretamente com a Amazon. | Não habilitar sem evidência de que a coleta automatizada de HTML público é permitida para este uso. |
| KaBuM! | [Página pública](https://www.kabum.com.br/), [Políticas do site e marketplace](https://www.kabum.com.br/politicas) e [`robots.txt`](https://www.kabum.com.br/robots.txt) | A página inicial e as políticas de consumo são públicas. A consulta de `robots.txt` retornou 403 na ferramenta de pesquisa, portanto suas regras não foram verificadas. As políticas consultadas não bastam para concluir se coleta automatizada é permitida. | Evidência insuficiente; localizar termos aplicáveis, obter `robots.txt` por canal autorizado e inspecionar página de produto antes de classificar. |
| Carrefour | [Termos e Condições de Uso](https://www.carrefour.com.br/termos-de-uso) e [página de produto pública](https://www.carrefour.com.br/produto/spidey-e-seus-amigos-incriveis-marvel-speak-e-go-trace-e-bot-electronic-spider-bot-toy-sound-activated-crawls-for-kids-ages-and-up-326215124) | Os termos do site mencionam proibição de mineração de dados e spiders. A página de produto é visível, mas a regra publicada não permite classificá-la como fonte autorizada para este projeto. | Fora do escopo atual, salvo mudança dos termos que permita esse uso. |
| Fast Shop | [Termos e Condições de Uso da Plataforma](https://site.fastshop.com.br/termos-e-condicoes-de-uso) | Os termos proíbem pesquisas por robôs, bots, spiders, crawlers e outras ferramentas de captura de dados. | Fora do escopo atual. |
| TerabyteShop | [`robots.txt`](https://www.terabyteshop.com.br/robots.txt), [página de produto pública](https://www.terabyteshop.com.br/produto/42144/pc-gamer-captain-amd-ryzen-5-9600x-nvidia-rtx-2060-16gb-ddr5-ssd-480gb) e [Termos e Condições de Venda](https://www.terabyteshop.com.br/site/termos-e-condicoes-gerais-de-venda-de-produtos) | O `robots.txt` permite caminhos `/produto/` e divulga sitemap; os termos consultados tratam de venda e não esclarecem se um crawler de comparação de preços pode automatizar o acesso. A permissão no `robots.txt` é uma indicação técnica, não autorização suficiente por si só. | Candidata técnica; confirmar se os termos permitem esse uso antes de classificar como elegível. |
| Pichau | [`robots.txt`](https://www.pichau.com.br/robots.txt), [Termos de uso do site](https://www.pichau.com.br/termos-de-aceite) | O `robots.txt` permite `/` para `User-agent: *`, bloqueia rotas e URLs com query string, e aponta sitemap. Os termos restringem bots/scripts usados para automatizar compras ou burlar o fluxo, mas não esclarecem expressamente o rastreamento de páginas de produto para comparação de preços. | Candidata técnica; confirmar o alcance dos termos para esse caso antes de classificar como elegível. |

## Consequência para o plano

- A hipótese “páginas acessíveis publicamente podem ser rastreadas” não está validada. Visibilidade no navegador ou ausência de bloqueio em `robots.txt` não implica autorização para automação, armazenamento ou republicação.
- Não há, nesta análise, três domínios aprovados para coleta. A meta de três domínios do roadmap continua sendo critério de viabilidade a comprovar, não uma premissa.
- A triagem encontrou restrições explícitas no Mercado Livre, Amazon, Carrefour e Fast Shop. Magalu e KaBuM! ainda têm evidência insuficiente; TerabyteShop e Pichau permitem caminhos de produto no `robots.txt`, mas os termos consultados não esclarecem o uso de crawler para comparação de preços. Portanto, a hipótese de três fontes HTML elegíveis continua sem comprovação.
- A fonte deve ser HTML público e a coleta automatizada precisa ser permitida pelas regras aplicáveis. APIs, feeds e integrações de terceiros estão fora do escopo. Se a permissão não estiver clara, manter o domínio desabilitado até esclarecimento; se não for permitida, descartá-lo.
- Revalidar por fonte: termos aplicáveis ao caso de uso; `robots.txt` para o User-Agent identificado; disponibilidade de páginas e meios de descoberta; campos de preço/variante/vendedor; frequência e concorrência; armazenamento, atribuição e retenção; contato para pedir esclarecimento ou permissão.
- Não contornar login, CAPTCHA, bloqueios, limites técnicos ou restrições do site. A retenção decidida para o produto (apagar dados ao remover o acompanhamento) não substitui limites de retenção da fonte; se as condições da fonte forem incompatíveis, não habilitar essa fonte.

## Próximas verificações manuais

1. Obter as condições de uso atuais diretamente para cada loja candidata, incluindo regras para comparação de preços e uso comercial; pedir esclarecimento quando os termos forem silenciosos ou ambíguos.
2. Confirmar acesso permitido a página de busca/catálogo e página de produto, sem autenticação e sem contornar mecanismos técnicos.
3. Inspecionar manualmente exemplos representativos e registrar URL, data, campos visíveis/estruturados e variantes/vendedor, sem automatizar requisições.
4. Somente então classificar cada domínio como elegível para coleta automatizada de HTML público ou descartado; escolher pelo menos três elegíveis antes de aprovar a saída da Fase 0. Se não houver três, rever a viabilidade do MVP antes de qualquer implementação.

## Critério de autorização ainda a definir

Decisão do usuário: só classificar um domínio como elegível quando os termos aplicáveis permitirem expressamente a coleta automatizada de HTML público ou quando houver autorização escrita da loja. Se os termos forem silenciosos, pedir autorização escrita; se proibirem, descartar a fonte. Não tratar ausência de proibição ou `robots.txt` isolado como autorização.

## Referências

As páginas acima são fontes primárias dos próprios sites e foram consultadas em 11/10/2026. Elas podem mudar; o registro é evidência preliminar e deve guardar data e revisão quando atualizado. Esta pesquisa não é parecer jurídico.
