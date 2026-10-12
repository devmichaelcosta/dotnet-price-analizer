# Pesquisa preliminar de fontes — Fase 0

**Estado:** rascunho; análise documental/manual, não é autorização para coletar.  
**Consulta:** análise documental em 11/10/2026. Em 12/10/2026, o usuário decidiu que o projeto não contatará lojas. Revalidar termos, `robots.txt` e páginas por pesquisa documental antes de habilitar qualquer fonte.

## Objetivo e limite

Avaliar se páginas HTML públicas de diferentes lojas podem servir à descoberta e ao acompanhamento de preços. A decisão do produto é não usar APIs, feeds ou integrações de terceiros como fonte de dados, nem enviar mensagens, formulários, chamados ou pedidos às lojas. A aplicação fará somente requisições automatizadas de leitura a páginas públicas de domínios elegíveis. Esta análise não executou crawler, parser, automação de navegador nem coleta de produto. A leitura de `robots.txt` serve como sinal técnico de caminhos e regras declarados para agentes; não é, por si só, autorização ou interpretação jurídica dos termos.

## Achados iniciais

| Fonte candidata | Evidência consultada | O que a evidência indica | Estado preliminar |
|---|---|---|---|
| Magazine Luiza | [`robots.txt`](https://www.magazineluiza.com.br/robots.txt) | Há caminhos explicitamente desautorizados. A regra `User-agent: *` não mostra bloqueio geral de páginas de produto; regras específicas de `Googlebot` bloqueiam alguns formatos de busca. Isso não confirma permissão para nosso uso nem responde sobre retenção dos dados. | Candidata para análise adicional dos termos e de uma página de produto; não habilitada. |
| Mercado Livre | [Termos para Desenvolvedores](https://developers.mercadolivre.com.br/pt_br/termos-e-condicoes) e [`robots.txt`](https://www.mercadolivre.com.br/robots.txt) | Os termos do Programa de Desenvolvedores vedam robôs, spiders e scraping para acesso ao site/conteúdo no âmbito descrito pelo programa. Como o MVP não usará a API nem participará desse programa, a aplicação dessa cláusula a uma coleta HTML independente não ficou confirmada. O `robots.txt` não substitui autorização. | Elegibilidade não comprovada; manter desabilitado enquanto os termos públicos aplicáveis não permitirem expressamente a coleta. |
| Amazon Brasil | [Condições de Uso do Amazon Relay](https://relay.amazon.com/terms) e [Políticas do Programa de Associados](https://associados.amazon.com.br/help/operating/policies/) | As condições do Amazon Relay proíbem mineração de dados e robôs no contexto do serviço Relay; não comprovam, por si sós, quais condições regem páginas de produto de `amazon.com.br`. Separadamente, as políticas de Associados limitam exibição de preços e vedam recurso de rastreamento/alerta sem aprovação prévia no contexto daquele programa. O projeto não adotou o programa nem suas APIs. | Elegibilidade para HTML público não comprovada; manter desabilitada até que os termos públicos aplicáveis permitam expressamente a coleta. |
| KaBuM! | [Página pública](https://www.kabum.com.br/), [Políticas do site e marketplace](https://www.kabum.com.br/politicas) e [`robots.txt`](https://www.kabum.com.br/robots.txt) | A página inicial e as políticas de consumo são públicas. A consulta de `robots.txt` retornou 403 na ferramenta de pesquisa, portanto suas regras não foram verificadas. As políticas consultadas não bastam para concluir se coleta automatizada é permitida. | Evidência insuficiente; localizar termos aplicáveis, obter `robots.txt` por canal autorizado e inspecionar página de produto antes de classificar. |
| Carrefour | [Termos e Condições de Uso](https://www.carrefour.com.br/termos-de-uso) e [página de produto pública](https://www.carrefour.com.br/produto/spidey-e-seus-amigos-incriveis-marvel-speak-e-go-trace-e-bot-electronic-spider-bot-toy-sound-activated-crawls-for-kids-ages-and-up-326215124) | Os termos do site mencionam proibição de mineração de dados e spiders. A página de produto é visível, mas a regra publicada não permite classificá-la como fonte autorizada para este projeto. | Fora do escopo atual, salvo mudança dos termos que permita esse uso. |
| Fast Shop | [Termos e Condições de Uso da Plataforma](https://site.fastshop.com.br/termos-e-condicoes-de-uso) | Os termos proíbem pesquisas por robôs, bots, spiders, crawlers e outras ferramentas de captura de dados. | Fora do escopo atual. |
| TerabyteShop | [`robots.txt`](https://www.terabyteshop.com.br/robots.txt), [página de produto pública](https://www.terabyteshop.com.br/produto/42144/pc-gamer-captain-amd-ryzen-5-9600x-nvidia-rtx-2060-16gb-ddr5-ssd-480gb) e [Termos e Condições de Venda](https://www.terabyteshop.com.br/site/termos-e-condicoes-gerais-de-venda-de-produtos) | O `robots.txt` permite caminhos `/produto/` e divulga sitemap; os termos consultados tratam de venda e não esclarecem se um crawler de comparação de preços pode automatizar o acesso. A permissão no `robots.txt` é uma indicação técnica, não autorização suficiente por si só. | Candidata técnica; confirmar se os termos permitem esse uso antes de classificar como elegível. |
| Pichau | [`robots.txt`](https://www.pichau.com.br/robots.txt), [Termos de uso do site](https://www.pichau.com.br/termos-de-aceite) | O `robots.txt` permite `/` para `User-agent: *`, bloqueia rotas e URLs com query string, e aponta sitemap. Os termos restringem bots/scripts usados para automatizar compras ou burlar o fluxo, mas não esclarecem expressamente o rastreamento de páginas de produto para comparação de preços. | Candidata técnica; confirmar o alcance dos termos para esse caso antes de classificar como elegível. |

## Revalidação manual adicional — 11/10/2026

Esta revisão consultou documentos oficiais dos oito candidatos da triagem. Algumas páginas não puderam ser lidas diretamente pela ferramenta de pesquisa; nesses casos, o resultado está registrado como inconclusivo, sem presumir autorização ou proibição. A consulta foi manual; não houve crawler, parser ou coleta automatizada de páginas de produto.

| Fonte | Evidência atual consultada | Resultado para o critério aprovado |
|---|---|---|
| Carrefour | [Termos e Condições de Uso](https://www.carrefour.com.br/termos-de-uso) | O texto proíbe mineração de dados e spiders na plataforma. Não elegível segundo os termos consultados; reavaliar somente se a regra aplicável mudar. |
| Fast Shop | [Termos e Condições de Uso da Plataforma](https://site.fastshop.com.br/termos-e-condicoes-de-uso), cláusula 6.1(c) | Proíbe pesquisas por robôs, bots, spiders, crawlers e outras ferramentas de captação de dados. Não elegível segundo os termos consultados; reavaliar somente se a regra aplicável mudar. A página indica atualização em 23/08/2024, então deve ser revalidada antes de qualquer decisão futura. |
| Mercado Livre | [Termos e Condições do Programa de Desenvolvedores](https://developers.mercadolivre.com.br/pt_br/termos-e-condicoes), cláusula 7.6 | A vedação a robôs, harvesters, spiders e scraping aparece nos termos do programa de API. A aplicação dessa cláusula à navegação HTML independente não ficou estabelecida nesta revisão. Como não há permissão expressa comprovada nos termos públicos aplicáveis ao caso, manter desabilitado. |
| Amazon Brasil | [Políticas do Programa de Associados](https://associados.amazon.com.br/help/operating/policies/), regras 2 e 5(y); [Condições de Uso do Amazon Relay](https://relay.amazon.com/terms) | As regras de Associados exigem fontes específicas para exibir preços e vedam rastreamento/alerta sem aprovação dentro do programa. As Condições de Uso abertas são do Amazon Relay. Nenhuma dessas duas evidências, isoladamente, confirma permissão para coleta HTML de páginas de produto do varejo Amazon fora do programa; manter desabilitada. |
| Pichau | [Termos de uso](https://www.pichau.com.br/termos-de-aceite), cláusula 6.1; [`robots.txt`](https://www.pichau.com.br/robots.txt) | A cláusula localizada trata de bots/scripts para automatizar compras com o objetivo de burlar o processo regular. Ela não concede permissão expressa para consultar páginas de produto e comparar preços. O `robots.txt` permite a raiz e aponta um sitemap, mas isso é somente uma regra técnica. Manter desabilitada. |
| TerabyteShop | [Termos e Condições de Venda](https://www.terabyteshop.com.br/site/termos-e-condicoes-gerais-de-venda-de-produtos); [`robots.txt`](https://www.terabyteshop.com.br/robots.txt) | Na página de termos revisada não foi encontrada permissão expressa para crawler ou comparação de preços. O `robots.txt` permite algumas rotas `/produto/`, bloqueia `/busca` e lista um sitemap; isso não substitui autorização. Manter desabilitada. |
| Magazine Luiza | [Termo de Compra e Venda](https://atendimento.magazineluiza.com.br/hc/pt-br/articles/360046742752-Termo-de-Compra-e-Venda); [`robots.txt`](https://www.magazineluiza.com.br/robots.txt) | O resultado oficial disponível descreve regras de compra e venda, mas não comprova permissão para coleta automatizada. A página integral redirecionou para uma tela de acesso na consulta; o conteúdo completo não foi confirmado. Manter desabilitada. |
| KaBuM! | [Políticas do site e marketplace](https://www.kabum.com.br/politicas); [`robots.txt`](https://www.kabum.com.br/robots.txt) | A ferramenta de pesquisa recebeu HTTP 403 ao tentar ler os dois documentos. Isso não prova proibição nem permissão. Nenhuma permissão expressa foi verificada; manter desabilitada. |

**Resultado desta revisão:** nenhum dos oito candidatos atende, com a evidência disponível, ao requisito de permissão expressa nos termos públicos aplicáveis. Dois candidatos têm proibições explícitas relevantes (Carrefour e Fast Shop); para os demais, a permissão não foi comprovada ou o escopo dos documentos é insuficiente. Nenhum domínio está aprovado para coleta automatizada.

## Consequência para o plano

- A hipótese “páginas acessíveis publicamente podem ser rastreadas” não está validada. Visibilidade no navegador ou ausência de bloqueio em `robots.txt` não implica autorização para automação, armazenamento ou republicação.
- Não há, nesta análise, três domínios aprovados para coleta. A meta de três domínios do roadmap continua sendo critério de viabilidade a comprovar, não uma premissa.
- Há proibições explícitas para coleta automatizada nos termos consultados do Carrefour e da Fast Shop. As regras de scraping do Mercado Livre são parte dos termos do Programa de Desenvolvedores, e as regras da Amazon citadas são do Amazon Relay e do Programa de Associados; a aplicação delas à coleta HTML independente do varejo não foi confirmada. Magalu, KaBuM!, TerabyteShop e Pichau seguem sem permissão expressa comprovada. Nenhum dos candidatos verificados atende ao critério de elegibilidade e a hipótese de três fontes HTML elegíveis continua sem comprovação.
- A fonte deve ser HTML público e a coleta automatizada precisa ser permitida expressamente pelos termos públicos aplicáveis. APIs, feeds e integrações de terceiros estão fora do escopo. Se os termos não forem claros, manter o domínio desabilitado; se proibirem a coleta, descartá-lo.
- Revalidar por fonte: termos aplicáveis ao caso de uso; `robots.txt` para o User-Agent identificado; disponibilidade de páginas e meios de descoberta; campos de preço/variante/vendedor; frequência e concorrência; armazenamento, atribuição e retenção. Não contatar a loja.
- Não contornar login, CAPTCHA, bloqueios, limites técnicos ou restrições do site. A retenção decidida para o produto (apagar dados ao remover o acompanhamento) não substitui limites de retenção da fonte; se as condições da fonte forem incompatíveis, não habilitar essa fonte.

## Opções para revisão de viabilidade

1. **Manter a decisão atual:** habilitar somente domínios cujos termos públicos permitam expressamente a coleta automatizada de HTML. Não contatar lojas.
2. **Revisar a viabilidade:** se não houver três domínios elegíveis, registrar a limitação e pedir ao usuário uma decisão sobre a meta de cobertura, sem alterar a proibição de contato.
3. **Revisar a restrição de fontes:** avaliar, somente se o usuário mudar essa decisão, fontes oficiais como APIs ou feeds autorizados e suas condições, dependências e custos.

Nenhuma opção de mudança de escopo está aprovada. A Fase 0 e a implementação permanecem pendentes até que a viabilidade ou os critérios do MVP sejam revisados com o usuário.

## Próximas verificações manuais

1. Consultar documentalmente os termos públicos aplicáveis e registrar URL, data, escopo e conclusão; não enviar perguntas às lojas.
2. Verificar `robots.txt` e, por navegação manual normal, a existência de páginas públicas de busca/catálogo e de produto, sem autenticação e sem contornar mecanismos técnicos.
3. Inspecionar manualmente exemplos representativos e registrar URL, data, campos visíveis/estruturados e variantes/vendedor, sem automatizar requisições.
4. Classificar cada domínio como elegível somente se houver permissão expressa nos termos; termos silenciosos ou ambíguos mantêm o domínio desabilitado. Se não houver três elegíveis, registrar a inviabilidade e rever o MVP antes de qualquer implementação.

## Decisão de produto — sem contato com lojas (12/10/2026)

Em etapa anterior, foram mapeados canais de atendimento e preparado um modelo de solicitação, mas nenhum contato ou pedido foi enviado. Após a decisão do usuário, esse levantamento é apenas histórico: os canais não devem ser usados e nenhum processo do projeto deve enviar mensagens, formulários, chamados ou pedidos às lojas. A coleta prevista consiste exclusivamente em requisições automatizadas de leitura a páginas HTML públicas de domínios elegíveis.

## Critério de autorização aprovado pelo usuário

Só classificar um domínio como elegível quando os termos públicos aplicáveis permitirem expressamente a coleta automatizada de HTML público. Se forem silenciosos ou ambíguos, manter a fonte desabilitada; se proibirem, descartá-la. Não contatar a loja nem tratar ausência de proibição ou `robots.txt` isolado como autorização.

## Referências

As páginas acima são fontes primárias dos próprios sites e foram consultadas em 11/10/2026. Elas podem mudar; o registro é evidência preliminar e deve guardar data e revisão quando atualizado. Esta pesquisa não é parecer jurídico.
