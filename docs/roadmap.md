# Roadmap inicial — Analisador de preços

**Estado:** rascunho para planejamento; não há aplicação implementada.  
**Sequência proposta:** concluir decisões e validação documental das fontes antes de iniciar a implementação.

## Premissas do primeiro lançamento

- Mercado inicial: Brasil, interface pt-BR, preço principal em BRL.
- Uso e implantação: aplicação local para uso próprio, sem hospedagem pública. Há múltiplos usuários autenticados; o administrador local provisiona suas contas, sem cadastro público.
- Não usar APIs, feeds ou integrações de terceiros para coletar dados de produtos.
- Entradas aceitas no primeiro corte: texto e EAN/GTIN. URL fica fora do primeiro escopo.
- Comparação inicial usa somente o preço anunciado do item, sem somar frete e sem pedir CEP.
- Frequência desejada de atualização: a cada seis horas, configurável e sujeita às regras e limites de cada domínio. Atualização manual também deve existir.
- O caminho de descoberta e coleta é um crawler e índice próprios, com parsers genéricos e adaptadores por domínio, limitado a páginas HTML públicas cuja coleta automatizada seja expressamente permitida pelos termos públicos aplicáveis. O projeto não contata lojas por mensagens, formulários, chamados ou pedidos de autorização. Se os termos forem silenciosos ou ambíguos, manter a fonte desabilitada; se proibirem a coleta, descartá-la. `robots.txt` sozinho não prova autorização.
- O primeiro corte deve pesquisar e comparar ofertas em vários domínios públicos elegíveis; a meta de validação é pelo menos três domínios, escolhidos na Fase 0 e não codificados como dependência fixa.
- O sistema não promete indexar toda a Internet: a cobertura começa nos domínios configurados e se expande conforme páginas acessíveis, regras de coleta e capacidade.
- Interface escolhida: Blazor Web App Interactive Server com MudBlazor, no mesmo host ASP.NET Core que expõe a Web API.
- A Web API é privada, exposta apenas para uso local do próprio sistema; sem clientes terceiros, aplicativos externos ou hospedagem pública no MVP.
- O usuário confirma anúncios ambíguos; correspondência errada comprometeria a análise de preço.
- Histórico: para a mesma oferta, criar uma nova observação somente quando preço ou disponibilidade mudarem; não duplicar leituras idênticas.
- Acompanhamentos e históricos são privados por conta local. As observações são mantidas enquanto o acompanhamento existir e apagadas com ele, respeitados limites ou requisitos aplicáveis à fonte.
- Alertas e notificações externas ficam fora do escopo da instalação local planejada; qualquer canal de notificação exige nova decisão do usuário.
- Arquitetura: monólito modular organizado por capacidades de negócio e fatias verticais; Clean Architecture/DDD orientam dependências e regras de domínio.
- Processo de desenvolvimento: Spec-Driven Development (SDD), com contexto persistente do domínio, especificações por caso de uso, planos/tarefas co-localizados e decisões transversais registradas.
- Testes: derivados dos critérios de aceite da spec; cada caso de uso e cada classe de domínio deve ter testes automatizados associados e rastreáveis.
- Bibliotecas: priorizar componentes oficiais/suportados pela Microsoft; MudBlazor é a escolha explícita para UI. Outras dependências de terceiros exigem uma lacuna concreta, justificativa e avaliação de suporte, manutenção, segurança e licença.
- Coesão de código: cada caso de uso tem uma pasta própria com seus arquivos específicos juntos, sem separá-los em pastas globais por tipo de arquivo.
- Blazor é apresentação: regras de negócio, cálculos, autorização de recursos e persistência ficam nos módulos/casos de uso do backend.
- Identidade: ASP.NET Core Identity com PostgreSQL para contas locais; cookie protegido para a sessão do Blazor.
- Tokens: JWT/OIDC não são necessários no MVP porque a API não terá clientes externos; reavaliar se o sistema ganhar aplicativo mobile, frontend separado ou integrações de terceiros.

## Fase 0 — Planejamento e validação de fontes (atual)

**Objetivo:** fechar escopo do MVP e avaliar, por pesquisa documental e análise manual de páginas públicas, a viabilidade de descobrir e extrair informações em vários domínios sem depender de uma API comercial de busca. Não implementar crawler, parser ou aplicação nesta fase.

**Entregas:**

- Requisitos funcionais e não funcionais aprovados.
- Contexto do domínio com linguagem ubíqua, conceitos, invariantes, limites, hipóteses e questões abertas revisado e aprovado.
- Fluxo SDD e template de especificação definidos; especificações de todos os casos de uso do MVP revisadas e aprovadas antes de iniciar implementação.
- Estratégia de testes definida: IDs `AC-...`/`DR-...`, matriz critério-regra-teste, estrutura de projetos de teste espelhando módulo/caso de uso e gates de execução.
- Diagrama/contratos, modelo de dados e fluxos aprovados.
- Lista piloto de pelo menos três domínios de compras, não codificada como fonte única.
- Inventariar por domínio sitemaps, páginas HTML de busca/catálogo acessíveis, dados `schema.org/Product`/`Offer`, campos de preço/moeda/disponibilidade, frequência e termos que permitam coleta automatizada.
- Registrar achados e evidências por fonte em [`docs/research/validacao-fontes-fase-0.md`](research/validacao-fontes-fase-0.md); só considerar elegível um domínio cujos termos públicos permitam expressamente a coleta automatizada de HTML público e cuja retenção esteja dentro dos limites.
- Situação da pesquisa manual: oito lojas candidatas foram revisadas e nenhuma tem permissão expressa comprovada para este projeto; duas têm proibição explícita nos termos consultados. Nenhum domínio está habilitado. A decisão de produto é não contatar lojas: domínios com termos silenciosos ou ambíguos permanecem desabilitados, e a viabilidade deve ser revista se não houver fontes elegíveis.
- Analisar manualmente páginas de exemplo em pelo menos três domínios: registrar como URLs podem ser descobertas, se as páginas são acessíveis, quais campos estruturados/visíveis existem, se o parser genérico parece suficiente e que exceções exigiriam adaptador. Guardar URLs de referência e evidências/documentação, sem escrever crawler ou parser nesta fase.
- Estimar custo e escala de crawler, indexação própria e atualização a cada seis horas, sem fonte de dados por API ou feed.
- Fechar fronteiras dos módulos, convenção das fatias verticais e regras de dependência entre Domain, Application/Features e Infrastructure.
- Definir padrões visuais compartilhados do MudBlazor (tema, tipografia, responsividade, acessibilidade, comportamento de formulários, feedback, loading/empty/error states) sem colocar regras de negócio nos componentes.
- Definir a estrutura dos projetos de teste e selecionar o framework priorizando tooling Microsoft; cada classe de domínio e cada caso de uso terão arquivos de teste organizados pela própria unidade funcional no projeto de testes.
- Especificar o comando local de uso único para criar o primeiro administrador e o comando local de recuperação administrativa (ambos sem dependência de e-mail), além do fluxo no app em que o administrador define uma senha temporária, entrega-a diretamente ao usuário e exige a troca antes de continuar no próximo login. Proteger a senha temporária contra exposição e armazenamento em texto puro; detalhar o provisionamento de usuários, sem cadastro público.
- Especificar a ativação de TOTP e a exibição/consumo dos códigos de recuperação de uso único, aplicar MFA obrigatória a administradores e opcional aos demais usuários, e recuperar MFA perdida (autenticador e todos os códigos) exclusivamente por comando local no host, disponível a qualquer pessoa com acesso local ao terminal e aplicável a qualquer conta. O comando solicita o login, exibe a conta correspondente e exige confirmação explícita antes do reset. Após o reset, exigir novo cadastro de TOTP antes do acesso normal quando MFA for obrigatória ou estiver habilitada; definir os registros de auditoria do comando. Também definir políticas locais de sessão, lockout, antifalsificação, autorização dos endpoints e proteção das chaves Data Protection; configurar e verificar que host/API aceitam conexões somente por loopback/localhost.
- Registrar como decisão futura que qualquer cliente externo (mobile, frontend separado ou integração de terceiro) exige reavaliar OAuth/OIDC e emissão/validação de JWT; priorizar opções Microsoft e só adotar emissor de terceiros quando houver justificativa. Não criar emissor JWT caseiro nem tratar os tokens proprietários da Identity API como JWT padrão.
- Documentar como a UI Blazor e endpoints internos usam a identidade autenticada e cookies, aplicando antifalsificação quando requisições por cookie alterarem dados.
- Verificar, por domínio piloto, se os termos/limites permitem a retenção prevista: observações permanecem enquanto o acompanhamento existir e são apagadas junto com ele.

**Critério de saída:** para pelo menos três domínios elegíveis há evidência documental/manual de que os termos públicos permitem expressamente a coleta, além da forma de descoberta, acessibilidade e campos de produto/preço; limites e estratégia de extração estão registrados; frequência de seis horas e custo/escala foram estimados e ajustados quando necessário; administração segura das contas locais, privacidade e retenção estão definidos; o contexto do domínio, a estratégia de testes e todas as especificações do MVP estão aprovados. Nenhuma loja é contatada. Nenhum código de aplicação, crawler ou parser é escrito antes desse gate.

**Risco prioritário:** não existe uma forma simples de pesquisar exaustivamente toda a web sem consultar um índice externo ou construir e manter um índice próprio. O plano evita dependência de API comercial mantendo um crawler/indexador próprio, mas a cobertura será incremental e limitada a domínios públicos acessíveis e compatíveis com as regras de coleta.

## Fase 1 — Fundação técnica e segurança

**Objetivo:** estabelecer a aplicação, a autenticação confiável, as fronteiras modulares, a fila durável, o crawler e o índice próprio.

**Escopo:** solução .NET organizada em módulos e slices co-localizadas por caso de uso (endpoint/entrada, validação, handler, resultado e componente relacionado no mesmo diretório da fatia); `Server` hospeda Blazor Interactive Server com MudBlazor, endpoints REST e Identity no mesmo host; registrar os serviços MudBlazor, assets e providers no mesmo modo interativo dos componentes consumidores; centralizar tema e padrões visuais; PostgreSQL/EF Core local; ASP.NET Core Identity para contas provisionadas pelo administrador local, sem cadastro público; comando local de uso único para criar o primeiro administrador e comando local para recuperação administrativa, sem dependência de e-mail; fluxo no app em que o administrador define uma senha temporária, entrega-a diretamente ao usuário e exige a troca antes de continuar no próximo login; senha temporária protegida contra exposição e armazenamento em texto puro; MFA TOTP obrigatória para administradores e opcional aos demais usuários, com códigos de recuperação de uso único e sem dependência de e-mail; perda do autenticador e de todos os códigos recuperada exclusivamente por comando local no host, disponível a qualquer pessoa com acesso local ao terminal e aplicável a qualquer conta, sem redefinição pelo app ou por e-mail; o comando solicita o login, exibe a conta correspondente e exige confirmação explícita antes do reset; novo cadastro de TOTP é exigido antes do acesso normal quando MFA for obrigatória ou estiver habilitada; a spec define os registros de auditoria do comando; cookie com HTTPS, `Secure`, `HttpOnly` e `SameSite` apropriados; fluxo seguro de criação e redefinição administrativa de credenciais, lockout e política MFA; antifalsificação em operações cookie-authenticated; política padrão de autorização e checagem de propriedade por usuário no servidor; API interna sem emissão JWT/OIDC no MVP; fila/job persistente; cadastro de domínios e políticas de coleta; contratos versionados da API; log sem segredos e status de saúde. Preparar projetos de teste separados, selecionar o framework priorizando tooling Microsoft, organizar os testes espelhando módulo/caso de uso e classe de domínio, e configurar sua execução automática no pipeline; as specs fornecem IDs de aceite e domínio para a matriz de rastreabilidade.

**Critério de saída:** comando local de uso único cria o primeiro administrador somente antes da inicialização, solicita senha sem valor padrão e não a expõe em argumentos, logs ou arquivos versionados; comando local no host recupera a credencial administrativa sem depender de e-mail e protege a senha contra registro; administrador local consegue provisionar usuários sem cadastro público; no app, administrador define senha temporária e entrega-a diretamente ao usuário, cuja primeira sessão deve exigir uma nova senha antes do acesso normal; a senha temporária não é exposta nem mantida em texto puro; MFA TOTP é obrigatória para administradores e opcional aos demais usuários; códigos de recuperação são de uso único e não dependem de e-mail; perda do autenticador e de todos os códigos só é recuperável por comando local no host, disponível a qualquer pessoa com acesso local ao terminal e aplicável a qualquer conta, sem redefinição pelo app ou e-mail; o comando solicita o login, exibe a conta correspondente e exige confirmação explícita antes do reset; a conta precisa cadastrar novamente o TOTP antes do acesso normal quando MFA for obrigatória ou estiver habilitada; a spec define os registros de auditoria do comando; usuários conseguem entrar e sair; sessão do Blazor usa cookie protegido; endpoints internos exigem autenticação e autorização apropriadas; um usuário não acessa dados de outra conta; operações autenticadas por cookie têm proteção antifalsificação; MudBlazor está integrado conforme seu modo de renderização e padrões visuais, formulários têm validação apropriada e a UI trata estados de carregamento, vazio e erro; cada classe de domínio e caso de uso desta fase tem testes associados; todos os critérios de aceite e regras de domínio aplicáveis apontam para testes aprovados; nenhum componente Blazor contém regra de negócio ou acesso direto ao banco; cada caso de uso tem sua pasta e mantém nela todos os arquivos específicos do fluxo; módulos e slices respeitam as dependências definidas; aplicação e Worker conectam ao banco; segredos/chaves não estão em arquivos versionados.

## Fase 2 — Produto acompanhado e primeira busca

**Objetivo:** transformar um termo/EAN em candidatos de produto encontrados em vários domínios.

**Escopo:** criar/listar/pausar acompanhamento por texto ou EAN/GTIN; consultar índice próprio; descobrir URLs via sitemaps, catálogos, páginas públicas de busca dos próprios sites e links; crawler HTTP educado; parser genérico para Schema.org Product/Offer; adaptadores por domínio quando necessário; normalização, deduplicação, tela de resultados e confirmação de oferta; acompanhar estado/falhas.

**Critério de saída:** uma conta pesquisa um produto e vê ofertas com preço, site, vendedor quando disponível e link, originadas em pelo menos três domínios piloto; confirma as ofertas e consegue retomar uma busca que falhou. O mecanismo permanece sem dependência de API comercial de busca. Cada use case e classe de domínio desta fase tem testes associados, cobrindo seus critérios `AC-...` e regras `DR-...` aplicáveis.

## Fase 3 — Preço atual e histórico

**Objetivo:** começar a responder “subiu ou caiu desde quando?”.

**Escopo:** primeira coleta e coletas manuais; observações imutáveis datadas; tela de detalhe da oferta; gráfico por intervalo; mostrar fonte, horário, moeda e disponibilidade; calcular variação absoluta e percentual sobre o preço do item. Frete não participa do MVP.

**Critério de saída:** para uma oferta confirmada, a interface exibe todas as observações guardadas no período e calcula a diferença entre duas datas válidas sem misturar moedas, variantes ou anúncios. A comparação usa somente o preço do item. Cada use case e classe de domínio desta fase tem testes associados, cobrindo seus critérios `AC-...` e regras `DR-...` aplicáveis.

## Fase 4 — Monitoramento automático e comparação

**Objetivo:** manter histórico sem ação manual e comparar as ofertas equivalentes.

**Escopo:** agendamento persistido com intervalo-alvo de seis horas para URLs de ofertas confirmadas; distribuir horários ao longo do dia; não recrawlear toda a web em cada atualização; periodicidade, concorrência e limites configuráveis por domínio; backoff em `429`; retentativas; alertas de job atrasado; tabela de ofertas atuais; estatísticas de mínimo/máximo e evolução no período.

**Critério de saída:** reiniciar API ou Worker não perde jobs; falha de um conector não interrompe os demais; cada preço indica quando foi coletado; comparações deixam claras a variante e a disponibilidade; a frequência respeita a cota observada. Cada use case e classe de domínio desta fase tem testes associados, cobrindo seus critérios `AC-...` e regras `DR-...` aplicáveis.

## Fase 5 — Alertas para o usuário

**Objetivo:** fase futura opcional, fora da instalação local planejada atualmente.

**Escopo:** não incluído no escopo atual. Alertas externos, fila de notificações e integração de e-mail só serão planejados mediante nova decisão do usuário.

**Critério de saída:** nenhum no escopo atual. Se a fase for aprovada futuramente, seus critérios serão especificados antes de qualquer implementação.

## Fase 6 — Novas lojas e preparação para produção

**Objetivo:** ampliar cobertura para novos domínios e operar com segurança.

**Escopo:** adicionar domínios e adaptadores pela demanda e viabilidade, desde que a coleta automatizada de HTML público seja permitida; otimizar paralelismo e capacidade de indexação; monitoramento operacional; cópias de segurança e retenção; exclusão de conta; revisão de privacidade, termos por domínio e capacidade/custo.

**Critério de saída:** cada integração tem documentação de acesso e limites; há alertas de falha, recuperação de dados e processo para remover conta e seus dados; custo e frequência de coleta são conhecidos. Cada use case e classe de domínio desta fase tem testes associados, cobrindo seus critérios `AC-...` e regras `DR-...` aplicáveis.

## Backlog priorizado

| Prioridade | Épico | Depende de | Valor entregue |
|---|---|---|---|
| P0 | Definir limites dos módulos, vertical slices e dependências Clean Architecture | — | Evita acoplamento e lógica de negócio na UI |
| P0 | Fechar ASP.NET Core Identity, cookie Blazor e políticas de autorização para API interna | — | Estabelece uma base de segurança antes dos recursos do produto |
| P0 | Selecionar domínios piloto elegíveis e validar manualmente os meios autorizados de descoberta/extração | P0 | Reduz risco de cobertura limitada, acesso vedado ou dados não extraíveis |
| P1 | Identidade, isolamento de dados e preferências básicas | P0 | Usuários podem ter dados próprios com acesso restrito |
| P1 | Cadastro e busca de produto com estado de job | Fonte validada | Inicia o fluxo principal sem requisição longa |
| P1 | Seleção e confirmação de oferta | Busca | Evita comparar anúncios/variantes errados |
| P1 | Persistência de ofertas e observações | Seleção | Cria histórico confiável |
| P1 | Comparação e gráfico de preço | Observações | Entrega o propósito central do produto |
| P2 | Agendamento, retentativas e cotas | Busca e histórico | Mantém acompanhamento contínuo |
| P2 | Alertas por preço | Agendamento | Avisa sobre oportunidades |
| P2 | Expandir domínios e parsers específicos | Resultados da coleta inicial | Aumenta cobertura sem trocar o núcleo do sistema |

## Dependências e riscos do cronograma

- **Cobertura de crawling:** sites podem não ter sitemap/dados estruturados, renderizar preços com JavaScript, limitar robôs ou bloquear coleta. O roadmap não deve prometer toda a web.
- **Manutenção por domínio:** páginas mudam de formato; parsers podem precisar de manutenção. Erros de um domínio devem ser isolados e observáveis.
- **Regras de coleta:** `robots.txt`, termos de uso, limites, copyright e regras de retenção devem ser avaliados por domínio. Um `robots.txt` não é mecanismo de autorização nem substitui análise das regras aplicáveis.
- **Correspondência de produto:** títulos similares, modelos, tamanhos e kits causam falsos positivos; confirmar candidatos é essencial.
- **Frete:** fica fora do primeiro escopo; os preços comparados são os dos itens, sem custo de entrega.
- **Limites/custos:** a frequência desejada é seis horas; quantidade de usuários, domínios e páginas afeta banda, armazenamento e despesas. Distribuir jobs, limitar concorrência e ajustar frequência após medir capacidade e regras reais.
- **Retenção e termos:** cada domínio pode impor regras próprias de armazenamento, atribuição e redistribuição; registrar essas regras antes de habilitar coleta.
- **Privacidade:** contas e preferências são dados pessoais; incluir minimização, controles de acesso, exclusão e canal de contato na preparação do lançamento.
- **Tokens para clientes futuros:** não há necessidade de JWT/OIDC no MVP sem consumidores externos. Se esse requisito surgir, avaliar um emissor OAuth/OIDC maduro; os tokens Bearer da Identity API não são JWT padrão e não se deve criar token artesanal.
- **Autorização entre contas:** endpoints autenticados podem ainda sofrer IDOR se não verificarem dono do recurso. A checagem deve ocorrer no backend/caso de uso, com identidade do usuário derivada do principal autenticado.
- **Sessões e implantação:** configuração inadequada de cookie, CSRF ou chaves Data Protection pode invalidar sessões ou expor operações; definir e revisar as opções antes de publicar.

## Decisões já tomadas

- Estratégia de descoberta: crawler e índice próprios, aplicados a múltiplos domínios configuráveis.
- Método de coleta: somente páginas HTML públicas cuja coleta automatizada seja permitida; APIs, feeds e integrações de terceiros ficam fora do projeto.
- Cobertura: incremental; domínios piloto são definidos na Fase 0 e não ficam codificados como única fonte.
- Validação na Fase 0: pesquisa documental e análise manual de páginas de exemplo, sem protótipo ou código de crawler/parser.
- Entrada: texto + EAN/GTIN; URL fica para uma fase futura.
- Comparação: preço do item, sem frete.
- Coleta: intervalo-alvo de seis horas, a confirmar com as cotas reais.
- Privacidade e retenção: vários usuários na instalação local; acompanhamentos/históricos privados por conta; apagar observações e dados do acompanhamento junto com sua remoção, respeitando condições mais restritivas da fonte.
- Fontes: nenhum domínio está aprovado ainda; a revisão de oito candidatos e a decisão de não contatar lojas estão em `docs/research/validacao-fontes-fase-0.md`. Exigir permissão expressa nos termos públicos aplicáveis antes de habilitar cada domínio; termos silenciosos ou ambíguos mantêm a fonte desabilitada.
- Interface: Blazor Web App Interactive Server com MudBlazor; tema e padrões de UI centralizados, providers configurados no modo interativo e componentes sem lógica de negócio.
- UI e Web API ficam no mesmo host ASP.NET Core e na mesma origem no MVP; o Worker é um processo separado.
- Estrutura: monólito modular organizado por módulo de negócio e fatias verticais; Clean Architecture/DDD/SOLID guiam dependências, sem lógica de negócio nos componentes Blazor.
- Dependências: priorizar bibliotecas oficiais/suportadas pela Microsoft; justificar e revisar qualquer dependência externa necessária.
- UI: MudBlazor escolhido explicitamente como biblioteca de componentes; seguir documentação oficial para instalação, providers, formulários e validação.
- Organização das fatias: todos os arquivos específicos de um caso de uso ficam juntos na pasta dele, sem pastas globais separadas por tipo.
- SDD: `docs/domain/context.md` guarda conhecimento compartilhado; cada caso de uso tem especificação própria e pode ter plano/tarefas na mesma pasta; a implementação aguarda aprovação do roadmap e das especificações do MVP.
- Testes: cada critério de aceite da spec e cada regra de domínio aplicável devem ser rastreáveis a testes aprovados; cada caso de uso e cada classe de domínio tem testes associados em projetos de teste organizados por módulo/unidade funcional.
- Autenticação de usuário: ASP.NET Core Identity persistido em PostgreSQL; cookie seguro para o navegador Blazor.
- API: privada e de uso exclusivo pelo próprio sistema no MVP; autenticação interna baseada no contexto Identity/cookie, sem JWT/OIDC ou cliente terceiro.
- Evolução de autenticação: avaliar OAuth/OIDC e JWT se houver aplicativo mobile, frontend separado ou consumidores externos da API.

## Decisões ainda abertas para fechar na Fase 0

1. Quais domínios devem compor a amostra piloto de validação? Escolher vários sites com volume e formatos variados, sem torná-los dependência exclusiva do sistema e com permissão expressa nos termos públicos aplicáveis.

## Próxima etapa recomendada

Definir o critério de autorização, escolher domínios piloto e validar manualmente descoberta, campos, termos e retenção. Consolidar escopo e critérios de aceite da autenticação Identity/cookie antes de iniciar projetos e código; manter OIDC/JWT condicionado a uma futura necessidade de clientes externos e priorizar opções Microsoft nessa avaliação.
