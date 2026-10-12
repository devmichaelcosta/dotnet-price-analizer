# Contexto do domínio — Analisador de preços

**Estado:** contexto inicial em planejamento; conceitos e regras marcados como decisão ainda podem ser detalhados nas especificações.  
**Fonte de verdade:** atualizar este documento quando uma regra compartilhada do domínio mudar. Regras específicas de um fluxo pertencem à especificação daquele caso de uso.

## Propósito do domínio

Permitir que uma pessoa acompanhe um produto, encontre ofertas públicas em diferentes sites de compras e compare preços observados ao longo do tempo. O valor central é preservar observações com origem e data confiáveis para responder se o preço mudou.

## Linguagem ubíqua

| Termo | Significado no sistema |
|---|---|
| **Produto** | Item canônico que a pessoa quer acompanhar, identificado por nome e, quando disponível, GTIN/EAN, marca, modelo e atributos de variante. Não é uma página de loja nem um anúncio. |
| **Consulta de produto** | Texto ou GTIN/EAN fornecido para procurar candidatos. A URL não é entrada do primeiro escopo. |
| **Página de produto** | Página pública localizada em um site e que pode descrever um produto ou uma oferta. É uma fonte observada; pode estar incompleta ou desatualizada. |
| **Oferta/anúncio** | Uma página de venda com preço, condição, variante e disponibilidade. Um produto pode ter diversas ofertas; anúncios distintos não devem ser fundidos só por terem títulos parecidos. |
| **Loja/fonte web** | Site ou domínio em que uma página pública é localizada. Cada domínio tem suas próprias capacidades, políticas, limites e estado de coleta. |
| **Vendedor** | Entidade que vende a oferta. Pode ser a própria loja ou um vendedor terceiro em um marketplace; registrar separadamente quando a fonte disponibilizar essa informação. |
| **Observação de preço** | Leitura válida de uma oferta em um instante: preço do item, moeda, disponibilidade, URL/fonte e horário de coleta. Observações formam o histórico e não substituem leituras anteriores. |
| **Acompanhamento** | Relação entre a conta de uma pessoa e o produto/consulta que ela quer monitorar, incluindo estado ativo/pausado e frequência desejada. |
| **Domínio de coleta** | Site configurado para descoberta ou atualização, com política própria de acesso, intervalo, concorrência, dados permitidos e estratégia de extração. |
| **Correspondência** | Decisão de que uma oferta é do produto acompanhado e da mesma variante/condição. Um candidato incerto precisa ser confirmado antes de entrar na comparação. |

## Regras de domínio confirmadas

- **DR-001:** Produto, página de produto, oferta e observação de preço são conceitos diferentes.
- **DR-002:** Uma oferta pode mudar de preço ao longo do tempo; o sistema preserva observações datadas em vez de sobrescrevê-las.
- **DR-003:** Preço é um valor decimal acompanhado de moeda. Não usar ponto flutuante binário para representar dinheiro.
- **DR-004:** Cada observação inclui a fonte e o instante da coleta; horários são persistidos em UTC.
- **DR-005:** Indisponibilidade é estado da oferta, não preço zero.
- **DR-006:** Não comparar produtos ou ofertas de variantes, condições ou kits incompatíveis. GTIN/EAN é evidência forte; correspondência somente por título é candidata e pode exigir confirmação.
- **DR-007:** A comparação inicial usa apenas o preço do item. Frete e CEP ficam fora do primeiro escopo.
- **DR-008:** O sistema pesquisa um índice próprio formado por páginas HTML públicas de múltiplos domínios configuráveis cuja coleta automatizada seja permitida. A cobertura é incremental e não promete indexar toda a Internet.
- **DR-014:** O sistema não usa APIs, feeds ou integrações de terceiros para coletar dados de produtos. A visibilidade pública de uma página ou ausência de bloqueio em `robots.txt` não prova permissão; domínios com permissão proibida são descartados e os de permissão incerta ficam desabilitados até esclarecimento.
- **DR-015:** Um domínio só pode ser habilitado quando os termos aplicáveis permitirem expressamente a coleta automatizada de HTML público ou houver autorização escrita da loja. Se os termos forem silenciosos, solicitar autorização escrita; não inferir autorização do silêncio ou do `robots.txt`.
- **DR-016:** Para a mesma oferta, registrar uma nova observação de preço somente quando o preço do item ou a disponibilidade diferir da última observação válida. Leituras sem essas mudanças não criam observações duplicadas.
- **DR-009:** Ao iniciar um acompanhamento, o sistema procura candidatos no índice e pode descobrir novas páginas. Atualizações periódicas usam páginas/ofertas confirmadas; não repetem uma varredura ampla da web a cada ciclo.
- **DR-010:** A frequência pretendida é de seis horas, mas cada coleta deve respeitar as políticas e limites do domínio.
- **DR-011:** Dados do acompanhamento são associados à conta proprietária no backend; a UI não define nem prova a propriedade de um recurso.
- **DR-012:** Acompanhamentos e históricos de preço são privados por conta. Toda leitura ou alteração verifica no backend a propriedade do recurso pela conta autenticada.
- **DR-013:** Observações de preço e dados do acompanhamento são mantidos enquanto o acompanhamento existir e apagados quando ele for removido, respeitados eventuais limites menores ou deveres de retenção aplicáveis à fonte.

## Limites do MVP

- Mercado inicial: Brasil; interface pt-BR; preço principal em BRL.
- Entrada por texto ou GTIN/EAN; entrada direta de URL fica fora do primeiro escopo.
- Pesquisa em múltiplos sites, usando somente HTML público cuja coleta automatizada seja permitida; sem APIs, feeds ou integrações de terceiros como fonte de dados, e sem fixar um único ecommerce.
- Comparação de preço do item, sem frete.
- API privada, usada pelo próprio sistema; sem cliente terceiro ou aplicativo externo neste momento.
- Autenticação com ASP.NET Core Identity local e cookie protegido no navegador. JWT/OIDC não são necessários para o escopo atual.
- Acompanhamentos e históricos são privados por conta; observações e dados do acompanhamento são removidos ao excluir o acompanhamento, observadas as restrições aplicáveis da fonte.
- Alertas entram depois da coleta, do histórico e da comparação básica.

## Hipóteses e decisões ainda a validar

Estes itens não devem ser tratados como regras confirmadas até serem resolvidos nas especificações ou decisões de produto:

- Se produtos canônicos podem ser compartilhados internamente entre contas. Acompanhamentos e históricos permanecem privados por conta.
- Como lidar com GTIN/EAN inválido, ausente, duplicado ou conflitante entre páginas.
- Qual nível de confiança exige confirmação explícita da oferta e quais campos participam da correspondência.
- Retenção de dados coletados por domínio: a regra do produto é apagar observações e dados do acompanhamento junto com sua remoção; cada domínio ainda precisa ser verificado para limites ou requisitos adicionais.
- Domínios piloto que comprovem permissão para coleta automatizada de HTML público, conteúdo armazenável e formato disponível em cada fonte.
- Como representar preço promocional, preço parcelado, faixa de preço, condição do produto, variante e vendedores múltiplos quando as páginas divergirem.
- Regras para falhas de coleta, dados ausentes e preços que não possam ser validados.

## Relação com as especificações

- Use este documento para linguagem, conceitos e invariantes compartilhados pelo domínio.
- Use `docs/specs/<Modulo>/<CasoDeUso>/spec.md` para comportamento, cenários e critérios de aceite daquele caso de uso.
- Quando uma especificação revelar uma nova regra compartilhada, atualize este contexto e as especificações impactadas antes de implementar.
- Registre decisões arquiteturais ou de produto transversais com estado, justificativa e consequências, sem misturar hipótese com decisão aprovada.
