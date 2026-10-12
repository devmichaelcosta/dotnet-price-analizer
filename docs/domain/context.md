# Contexto do domínio — Analisador de preços

**Estado:** contexto inicial em planejamento; conceitos e regras marcados como decisão ainda podem ser detalhados nas especificações.<br>
**Fonte de verdade:** atualizar este documento quando uma regra compartilhada do domínio mudar. Regras específicas de um fluxo pertencem à especificação daquele caso de uso.

## Propósito do domínio

Permitir que uma pessoa acompanhe um produto, registre ofertas de diferentes lojas e compare preços informados ao longo do tempo. O MVP começa com entrada manual. A descoberta e atualização automáticas são uma evolução condicionada a cada domínio HTML passar pelo gate de permissão; o valor central é preservar a proveniência e a data de cada observação para analisar variações.

## Linguagem ubíqua

| Termo | Significado no sistema |
|---|---|
| **Produto** | Item cadastrado pela pessoa que deseja acompanhar, identificado por texto e, quando disponível, GTIN/EAN, marca, modelo e atributos de variante. Não é uma página nem uma oferta. |
| **Consulta de produto** | Texto ou GTIN/EAN informado para identificar o produto no catálogo local. Não dispara pesquisa na Internet no MVP. |
| **Página de produto** | Página pública de loja que pode descrever um produto ou oferta. No MVP manual, o sistema não a consulta; ela só poderá ser fonte futura após aprovação do domínio. |
| **Oferta manual** | Registro de uma oferta/loja informado pela pessoa, com dados para diferenciá-lo. Nome da loja e URL são referências informadas e não verificadas pelo sistema. |
| **Oferta automatizada** | Oferta derivada de uma página HTML consultada por um conector de domínio que passou pelo gate e foi habilitado. Fora do MVP manual. |
| **Fonte independente** | Loja ou marketplace distinto citado em uma oferta. Vendedores diferentes no mesmo marketplace são ofertas distintas, mas não fontes independentes para medir cobertura automatizada. |
| **Fonte/conector** | No MVP, observação informada manualmente pelo usuário. Futuramente, um conector poderá ler páginas HTML públicas de um domínio aprovado. APIs, feeds, agregadores e integrações de terceiros não são fontes de dados. |
| **Vendedor** | Entidade que vende a oferta. Pode ser a própria loja ou um vendedor terceiro em um marketplace; registrar separadamente quando a fonte disponibilizar essa informação. |
| **Observação de preço** | Registro datado com preço do item, moeda, disponibilidade e proveniência. No MVP, o usuário informa os dados; o sistema marca autoria manual e não afirma que a loja os confirmou. |
| **Acompanhamento** | Relação entre a conta local da pessoa e o produto que ela deseja analisar, incluindo ofertas e observações próprias. Atualização automática é futura e condicionada. |
| **Domínio de coleta** | Site considerado para coleta HTML futura, com política de acesso, intervalo, campos, retenção e estratégia de extração. Fica desabilitado sem evidência expressa nos termos. |
| **Comparabilidade** | Condições para comparar preços entre ofertas do mesmo produto e para comparar observações no tempo. Exige moeda, variante e condição compatíveis segundo a spec aprovada. |

## Regras de domínio confirmadas

- **DR-001:** Produto, página de produto, oferta e observação de preço são conceitos diferentes.
- **DR-002:** Uma oferta pode mudar de preço ao longo do tempo; o sistema preserva observações datadas em vez de sobrescrevê-las.
- **DR-003:** Preço é um valor decimal acompanhado de moeda. Não usar ponto flutuante binário para representar dinheiro.
- **DR-004:** Cada observação inclui proveniência e instante persistido em UTC. A captura manual identifica o usuário que informou o dado e o instante registrado; a spec define se também haverá horário de leitura informado pelo usuário.
- **DR-005:** Indisponibilidade é estado da oferta, não preço zero.
- **DR-006:** Não comparar produtos ou ofertas de variantes, condições ou kits incompatíveis. GTIN/EAN é evidência forte; correspondência somente por título é candidata e pode exigir confirmação.
- **DR-007:** A comparação inicial usa apenas o preço do item. Frete e CEP ficam fora do primeiro escopo.
- **DR-008 (revisada em 2026-10-12):** O MVP não faz descoberta automática; a pessoa cadastra manualmente produtos e ofertas. Uma futura busca limita-se a domínios HTML individuais cuja coleta passe pelo gate. A cobertura automatizada limita-se aos domínios habilitados e nunca é descrita como pesquisa exaustiva da Internet.
- **DR-014 (revisada em 2026-10-12):** Não usar APIs, feeds, agregadores ou integrações de terceiros como fonte de dados. A única forma de consulta automatizada considerada é HTML público do próprio domínio da loja, em etapa futura e condicionada à permissão expressa nos termos aplicáveis.
- **DR-015 (revisada em 2026-10-12):** Um domínio HTML só pode ser habilitado quando evidência pública dos termos permitir expressamente a coleta automatizada e o uso pretendido, incluindo campos exibidos e retenção de histórico. Ambiguidade, incompatibilidade ou falta de evidência mantém o domínio desabilitado. `robots.txt` sozinho não prova permissão. Não contatar lojas para pedir esclarecimento ou autorização.
- **DR-017 (revisada em 2026-10-12):** O MVP manual não consulta a Internet. Uma URL informada é apenas referência. No futuro, conectores aprovados farão somente leitura de páginas públicas; não alteram contas, anúncios, pedidos ou transações, não contatam lojas e não contornam autenticação, CAPTCHA, limites ou controles de acesso.
- **DR-018:** Observação manual é identificada como informada por usuário e vinculada à conta/autoria que a registrou. A interface não a descreve como verificada pela loja ou coletada automaticamente.
- **DR-019:** No fluxo manual, endereço da loja/página é dado informado pelo usuário e não é buscado, validado por requisição HTTP nem usado para iniciar coleta.
- **DR-016 (confirmada para coleta automatizada futura):** Para a mesma oferta, leituras automatizadas válidas só criam uma observação quando o preço do item ou a disponibilidade difere da última leitura válida. Leituras automáticas idênticas não criam duplicatas. Esta regra não define se uma captura manual explícita, feita em uma nova ação do usuário, deve gerar uma observação com o mesmo valor; esse comportamento continua pendente na spec manual.
- **DR-009 (revisada em 2026-10-12):** O acompanhamento do MVP manual não cria jobs de descoberta ou atualização. Se um conector HTML for aprovado em etapa futura, o trabalho automatizado e seus estados serão definidos em spec incremental; atualizações não devem repetir busca ampla sem necessidade.
- **DR-010:** A frequência-alvo de seis horas é uma intenção futura de atualização automática, não comportamento do MVP manual. Cada conector futuro deve respeitar os termos, limites e intervalos mínimos do domínio.
- **DR-011:** Dados do acompanhamento são associados à conta local proprietária no backend; a UI não define nem prova a propriedade de um recurso.
- **DR-012:** Acompanhamentos e históricos de preço são privados por conta local. Toda leitura ou alteração exige a identidade autenticada e valida no backend o acesso ao recurso.
- **DR-013:** Observações de preço e dados do acompanhamento são mantidos enquanto o acompanhamento existir e apagados quando ele for removido, respeitados eventuais limites menores ou deveres de retenção aplicáveis à fonte.

## Limites do MVP

- Mercado inicial: Brasil; interface pt-BR; moeda principal BRL.
- Entrada manual do produto por texto e/ou GTIN/EAN; os campos de oferta e o tratamento da URL serão fechados em spec. Uma URL não dispara acesso de rede.
- O MVP registra manualmente ofertas e preços, compara ofertas compatíveis entre lojas e analisa histórico. Não executa busca na Internet.
- A automação futura pode consultar somente páginas HTML públicas cuja coleta automatizada seja expressamente permitida pelos termos do domínio. Não usar APIs, feeds, agregadores ou integrações de terceiros como fonte de dados. Nenhum domínio está aprovado atualmente.
- Comparação de preço do item, sem frete.
- Aplicação de uso próprio em implantação local, sem hospedagem pública; há várias contas locais autenticadas.
- Acesso restrito à máquina hospedeira: host e API não devem aceitar conexões pela rede local ou pela Internet.
- O administrador local provisiona contas e redefine a senha de outro usuário pelo app, definindo uma senha temporária e entregando-a diretamente ao usuário. No próximo login, o usuário precisa escolher uma nova senha antes de continuar. A aplicação não deve expor nem manter a senha temporária em texto puro. A primeira conta administradora é criada por comando local de uso único, executado no host, com senha informada sem valor padrão. Se perder acesso, o administrador recupera a conta por comando local no host, sem depender de e-mail.
- MFA é obrigatória para administradores e opcional para os demais usuários, com TOTP em app autenticador e códigos de recuperação de uso único, sem depender de e-mail. Cada código é consumido quando usado. Se alguém perder o autenticador e todos os códigos, a recuperação de MFA de qualquer conta é feita exclusivamente por comando local no host, disponível a qualquer pessoa com acesso local ao terminal; não existe redefinição de MFA pelo app ou por e-mail. O comando solicita o login, exibe a conta correspondente e exige confirmação explícita antes do reset. Após o reset, a conta precisa cadastrar novamente o TOTP antes do acesso normal quando MFA for obrigatória ou estiver habilitada. O registro de auditoria contém instante UTC, conta alvo, usuário do sistema operacional que executou o comando e resultado, sem segredos.
- API privada, usada pelo próprio sistema; sem cliente terceiro ou aplicativo externo neste momento.
- Credenciais de fonte não fazem parte do MVP manual. Se uma evolução futura exigir segredo para consultar HTML, sua necessidade e armazenamento seguro precisam ser revisados antes de especificar o conector; segredos nunca vão ao GitHub, logs, telemetria ou argumentos de processo.
- Autenticação com ASP.NET Core Identity para contas locais e cookie protegido no navegador. Contas são provisionadas pelo administrador local; JWT/OIDC não são necessários para o escopo atual.
- Acompanhamentos e históricos são privados por conta local; observações e dados do acompanhamento são removidos ao excluir o acompanhamento, observadas as restrições aplicáveis da fonte.
- Alertas não fazem parte do escopo local atual e exigem decisão posterior.

## Hipóteses e decisões ainda a validar

Estes itens não devem ser tratados como regras confirmadas até serem resolvidos nas especificações ou decisões de produto:

- Campos obrigatórios para registrar manualmente produto e oferta, e se a URL será opcional.
- Se a observação usa o instante de entrada no app ou um horário informado pela pessoa, e como exibir autoria e proveniência.
- Critérios de comparabilidade entre ofertas distintas do mesmo produto (variante, condição, moeda, vendedor e tipo de preço).
- Como lidar com GTIN/EAN inválido, ausente, duplicado ou conflitante em dados informados pela pessoa.
- Retenção: dados privados do acompanhamento são removidos com ele; qualquer conector HTML futuro precisa ter compatibilidade de armazenamento validada por domínio.
- Domínios HTML que permitam expressamente o uso local para coleta e retenção de histórico; nenhum domínio foi aprovado.
- A pesquisa documental anterior de APIs e interfaces HTML está registrada em `docs/research/validacao-fontes-fase-0.md`. A diretriz vigente exclui APIs, feeds e integrações de terceiros como fontes; a triagem HTML não aprovou domínios.
- O usuário escolheu a opção C: núcleo manual primeiro; automação HTML apenas depois de gate individual. A decisão e seus impactos estão em `docs/research/revisao-escopo-mvp.md` e `docs/decisions/ADR-003-mvp-hibrido-e-fontes-html.md`; demais propostas estão em `docs/decisions/registro-fase-0.md`.

## Relação com as especificações

- Use este documento para linguagem, conceitos e invariantes compartilhados pelo domínio.
- Use `docs/specs/<Modulo>/<CasoDeUso>/spec.md` para comportamento, cenários e critérios de aceite daquele caso de uso.
- Quando uma especificação revelar uma nova regra compartilhada, atualize este contexto e as especificações impactadas antes de implementar.
- Registre decisões arquiteturais ou de produto transversais com estado, justificativa e consequências, sem misturar hipótese com decisão aprovada.
