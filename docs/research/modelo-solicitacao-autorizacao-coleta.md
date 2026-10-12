# Modelo de solicitação de autorização para coleta de preços

**Estado:** rascunho para revisão e envio pelo responsável pelo projeto. Não enviado.

## Assunto

Solicitação de autorização para consulta automatizada de páginas públicas de produtos

## Mensagem

Olá,

Estou planejando desenvolver o **Analisador de Preços**, um sistema privado que permite a usuários cadastrados acompanhar produtos e comparar a evolução de preços entre lojas.

Antes de incluir páginas da **[nome da loja]** como fonte, gostaria de confirmar formalmente se a loja autoriza o seguinte uso:

- Acesso automatizado, identificado e de baixa frequência a páginas HTML públicas de busca, catálogo e detalhes de produtos, sem autenticação.
- Consulta desejada de aproximadamente uma vez a cada seis horas, sujeita ao limite de frequência e às regras técnicas que a loja indicar.
- Extração apenas dos dados necessários para identificar a oferta e acompanhar o preço, como título/modelo ou EAN/GTIN, vendedor quando aplicável, preço e moeda, disponibilidade, URL pública e data/hora da observação.
- Armazenamento privado desses dados para comparação e histórico de preço. O acompanhamento e suas observações serão removidos quando o usuário apagar o acompanhamento.
- Exibição dos dados somente ao usuário que criou o acompanhamento, junto com um link para a página original da loja. O sistema não pretende republicar um catálogo público da loja.

O projeto não pretende usar APIs, feeds, programa de afiliados ou integração de terceiros. Também não acessará áreas autenticadas nem contornará CAPTCHA, bloqueios, limites, `robots.txt` ou outras medidas técnicas. Caso a loja estabeleça limites ou condições específicas para o User-Agent, os caminhos consultados, os campos extraídos, a atribuição da fonte ou a retenção, eles serão avaliados antes de qualquer ativação.

Poderiam, por favor, confirmar por escrito:

1. Se os termos atuais da loja permitem esse uso ou se a loja concede autorização expressa para ele.
2. Quais páginas e campos podem ser consultados e armazenados.
3. Qual frequência, concorrência e identificação de User-Agent são permitidas.
4. Se há exigências para atribuição, link, atualização, retenção ou remoção dos dados.
5. Se a autorização cobre o histórico privado de preços descrito acima e se há outras condições necessárias.

Se esta solicitação deve ser encaminhada a outra área, poderiam indicar o canal responsável? Posso fornecer detalhes técnicos adicionais, como o domínio do sistema e a identificação do crawler, antes de qualquer coleta.

Obrigado,

**[Nome do responsável]**

**[E-mail de contato]**

**[Domínio do projeto, se disponível]**

## Orientação de uso

- Enviar individualmente ao canal oficial indicado pela loja e guardar a resposta escrita junto ao registro da fonte.
- Não ativar a fonte enquanto os termos aplicáveis ou uma autorização expressa não cobrirem o uso pretendido.
- Se a loja negar a solicitação ou os termos proibirem esse uso, descartar essa fonte.
- Antes de usar uma autorização recebida, conferir se ela identifica a loja, o responsável que autorizou, as páginas/dados, os limites técnicos, a finalidade e o período de validade.
- Não incluir credenciais, dados pessoais de usuários ou informações confidenciais na solicitação.
