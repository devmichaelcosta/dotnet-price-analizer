# Planejamento e arquitetura inicial — Analisador de preços

**Estado:** planejamento; nenhuma aplicação foi criada.  
**Nota de escopo:** o usuário definiu o produto como uma aplicação local para uso próprio, sem hospedagem pública, com vários usuários autenticados e contas provisionadas pelo administrador local. O acesso é restrito à máquina hospedeira; host e API devem escutar somente em loopback/localhost, sem acesso pela rede local ou Internet. O primeiro administrador será criado por comando local de uso único, com senha informada sem valor padrão; a recuperação administrativa será por comando local no host, sem depender de e-mail. O sistema não contata lojas: as únicas chamadas externas previstas são requisições HTTP GET de leitura a páginas públicas elegíveis.
**Data da pesquisa:** 11/10/2026.  
**Região inicial considerada:** Brasil; idioma pt-BR; moeda BRL.
**Direção do MVP:** buscar por texto ou EAN/GTIN em páginas públicas de vários sites de compras. O sistema manterá seu próprio índice de páginas e ofertas normalizadas, usando crawler e parsers; nenhuma API comercial de busca nem marketplace específico será requisito. Comparar apenas o preço do item; atualização desejada a cada seis horas, sujeita às regras de cada site.
**Limite do primeiro corte:** cobertura inicial de vários domínios de compras configuráveis e elegíveis para coleta. Não prometer busca exaustiva em toda a Internet: ampliar a cobertura depende de descobrir e indexar mais domínios, além de respeitar bloqueios, termos, limites e diferenças técnicas de cada site.

## 1. Objetivo do produto

O administrador local provisiona contas. Cada usuário entra no app local, informa um produto por nome ou EAN/GTIN, escolhe os anúncios correspondentes e acompanha preços em diferentes lojas. O sistema preserva leituras datadas para mostrar comparação atual e variação de preço. URL não faz parte da entrada inicial.

A unidade acompanhada é o **produto**; a unidade que tem preço e vendedor é a **oferta/anúncio**. Um produto pode ter várias ofertas, em vários vendedores e sites. Cada oferta tem seu próprio histórico.

## 2. Stack e forma de implantação recomendadas

- **Frontend:** Blazor com MudBlazor, responsável pelas telas de conta, busca, seleção, acompanhamento, comparação e histórico.
- **Backend:** ASP.NET Core Web API em .NET 10 LTS.
- **Persistência:** PostgreSQL via EF Core 10 e provider Npgsql compatível.
- **Coleta e busca:** processo Worker .NET separado, que descobre páginas por sitemaps/catálogos públicos, coleta páginas elegíveis e alimenta um índice próprio; executa jobs persistidos sem deixar uma requisição HTTP aberta.
- **Organização:** solução modular, inicialmente um monólito modular com um host Server (Blazor + API) e um processo Worker. Não iniciar com microserviços.
- **Interface Blazor:** Blazor Web App com renderização interativa no servidor (Interactive Server), conforme escolha do usuário.
- **Hospedagem inicial:** um host ASP.NET Core e PostgreSQL são executados localmente; o Worker também roda localmente. O host ASP.NET Core escuta somente em loopback/localhost; PostgreSQL não é exposto à rede. A aplicação não aceita conexões de outros dispositivos da rede local.
- **Seleção de bibliotecas:** priorizar bibliotecas e componentes oficiais/suportados pela Microsoft quando atenderem aos requisitos. MudBlazor é uma escolha explícita do usuário para a camada de UI e uma exceção aprovada a essa preferência geral; acompanhar sua compatibilidade, manutenção e licença. Para outras dependências de terceiros, exigir uma lacuna concreta, justificativa e avaliação de suporte, manutenção, segurança e licença.

Com Interactive Server, eventos e componentes interativos são executados no servidor através de um circuito em tempo real. No MVP, os componentes Blazor chamam os casos de uso no servidor; não fazem uma chamada HTTP de volta ao próprio host. A Web API REST é parte privada do sistema e não será oferecida a clientes terceiros neste momento. Essa organização mantém uma única origem e uma sessão ASP.NET Core Identity baseada em cookie. Todos os endpoints REST ainda validam autenticação, autorização e propriedade dos dados. Se futuramente houver clientes externos ou a UI for separada do host, reavaliar OIDC/JWT e proteção CSRF/CORS antes de mudar a implantação.

### 2.1. Base de autenticação e autorização

- **Identidade:** ASP.NET Core Identity é o sistema de usuários e contas locais, persistido em PostgreSQL. O administrador local provisiona contas e pode iniciar pelo app a redefinição da senha de outro usuário; esse usuário deve escolher uma nova senha no próximo login. O mecanismo seguro de ativação da redefinição ainda precisa ser definido; use os mecanismos suportados pelo Identity e não exponha credenciais temporárias em argumentos, logs ou arquivos versionados. Não há cadastro público. O primeiro administrador é criado por comando local de uso único, executado no host, com senha informada sem valor padrão. O comando só pode inicializar essa conta uma vez; seu fluxo deve proteger a senha e não gravá-la em argumentos, logs ou arquivos versionados. A recuperação administrativa usa comando local no host, sem depender de e-mail, e deve usar os mecanismos suportados pelo Identity para redefinir a credencial; não gravar a senha em argumentos, logs ou arquivos versionados. Usar os mecanismos suportados do framework para senhas, bloqueio após tentativas inválidas, MFA, gestão de sessões e redefinição administrativa; não reimplementar hash/sal de senha nem o ciclo de login.
- **Blazor no navegador:** autenticação por cookie Identity, com HTTPS e atributos `Secure`, `HttpOnly` e `SameSite` apropriados. A Microsoft recomenda cookies para apps de navegador; evitar bearer tokens no `localStorage`. Páginas/fluxos de conta devem seguir a integração oficial Identity do Blazor e não executar operações de Identity diretamente em componentes interativos.
- **Web API:** a API é de uso interno pelo próprio sistema e não terá consumidores terceiros no MVP. Exigir autenticação por padrão e liberar endpoints públicos explicitamente. Se uma requisição HTTP autenticada por cookie alterar dados, aplicar proteção antifalsificação (CSRF). Os componentes Interactive Server chamam os casos de uso no servidor, sem fazer uma chamada HTTP para a própria API.
- **Tokens:** não emitir JWT nem hospedar servidor OAuth/OIDC no MVP. ASP.NET Core Identity com cookie protegido cobre a autenticação do navegador e os fluxos internos planejados. OpenIddict não é necessário para esse escopo. Se forem adicionados clientes externos, mobile ou uma UI separada que precise de Bearer JWT, avaliar primeiro as opções oficiais/suportadas pela Microsoft. Se elas não atenderem ao requisito, selecionar um emissor OIDC confiável de terceiros com justificativa técnica e revisão de suporte, manutenção, segurança e licença. A API deve validar assinatura, emissor (`iss`), audiência (`aud`), expiração e claims; não criar endpoint artesanal para emitir JWT. Os tokens Bearer das Identity API são proprietários, não JWT padrão.
- **Autorização:** combinar políticas/claims para capacidades com autorização baseada no recurso. Toda consulta de dados privados filtra pelo usuário autenticado no servidor; não aceitar `UserId` enviado pelo navegador como prova de propriedade. Roles ficam para funções amplas (por exemplo, administração); acesso a produtos/acompanhamentos exige verificar o dono do recurso no caso de uso.
- **Identificador do usuário:** associar os dados de negócio a um identificador interno estável; se houver emissor externo, mapear o par emissor/sujeito (`iss`/`sub`) para essa conta local. Não usar e-mail como chave de propriedade nem confiar em claims de identidade que o cliente possa editar.
- **Revalidação e implantação:** manter revalidação da sessão Identity no circuito Interactive Server; persistir e proteger as chaves ASP.NET Core Data Protection entre reinícios/instâncias. O pipeline de autorização API não depende de botões ou componentes ocultos na UI.

No MVP, ASP.NET Core Identity local e cookie protegido atendem a autenticação do sistema. OIDC/JWT fica como evolução condicionada à entrada de clientes externos ou a uma mudança na topologia da aplicação; não é um requisito para o login do Blazor atual.

### 2.2. Diretrizes de interface com MudBlazor

- Integrar a versão estável compatível com a versão do .NET selecionada, acompanhar atualizações e seguir a instalação oficial: registrar `AddMudServices`, importar o namespace e referenciar os assets CSS/JavaScript necessários.
- Configurar `MudThemeProvider` e `MudPopoverProvider` no layout interativo global; registrar `MudDialogProvider` e `MudSnackbarProvider` quando os respectivos serviços forem usados. Providers e componentes consumidores devem estar no mesmo modo interativo; providers renderizados estaticamente podem impedir interações.
- Centralizar tema, cores, tipografia, espaçamentos e padrões reutilizáveis para manter consistência e evitar estilos duplicados por página. Evitar misturar frameworks CSS sem necessidade.
- Em formulários, escolher deliberadamente entre `EditForm` e `MudForm`, sem combinar os dois padrões de submissão. Campos MudBlazor podem integrar a validação Blazor; a validação visual melhora a experiência, mas os casos de uso e validadores do backend continuam sendo a autoridade para regras e segurança.
- Componentes Razor apresentam estado, capturam entradas e encaminham ações aos casos de uso. Não guardar regras de domínio, autorização, cálculos de negócio ou persistência em code-behind/componentes.
- Oferecer rótulos explícitos, mensagens de erro úteis, uso por teclado, foco previsível, contraste adequado e comportamento responsivo. Listas longas de ofertas e observações devem usar paginação/carregamento adequado, sem renderizar volumes ilimitados de uma vez.
- Usar diálogos para decisões que precisem de confirmação e notificações breves para feedback transitório; erros que bloqueiem uma operação devem permanecer visíveis junto ao contexto afetado.

## 3. Módulos de negócio e plataforma

1. **Identidade e acesso** — login, provisionamento administrativo de usuários, recuperação de acesso e autorização. Todo produto acompanhado pertence ao usuário que o criou.
2. **Catálogo canônico** — identidade do produto: EAN/GTIN quando disponível, marca, modelo, categoria e características que distinguem versões.
3. **Acompanhamento** — vínculo entre usuário e produto, consulta de origem, frequência, status ativo/pausado e preferências básicas. O intervalo desejado no MVP é de seis horas, sujeito às regras e limites do domínio.
4. **Descoberta e índice web próprio** — aceita texto ou EAN/GTIN e pesquisa o índice local alimentado pelo crawler; não depende de provedores externos de busca.
5. **Cadastro de domínios e conectores** — configura vários sites de compras, políticas de coleta, sitemaps, limites, capacidades e exceções específicas de cada site. A arquitetura não presume um marketplace único.
6. **Crawler e extração** — descobre URLs por sitemaps, catálogos, páginas de busca públicas dos próprios sites e links públicos; lê páginas elegíveis; extrai dados estruturados (`Product`/`Offer` em JSON-LD, Microdata ou RDFa) e usa parsers específicos por domínio como fallback.
7. **Correspondência e confirmação** — associa páginas e ofertas ao produto canônico. Prioriza GTIN/EAN e identificadores estáveis; correspondência apenas por título é sugestão e deve ser confirmada quando houver dúvida.
8. **Jobs e agendamento** — persiste buscas, varreduras e atualizações; controla estado, tentativas, limites por domínio e próximos horários. A frequência deve respeitar as políticas de cada site.
9. **Normalização** — converte os dados extraídos para um formato comum, mantendo moeda, preço do item, condição, variante, disponibilidade, URL e origem. Frete não participa das comparações do MVP.
10. **Ofertas e histórico** — mantém o anúncio/página de origem e grava cada coleta como observação histórica. Uma nova leitura cria um registro novo; não apaga a anterior.
11. **Comparação e análise** — preço atual por oferta, menor preço válido, diferença em R$ e percentual, mínimo/máximo do período e série temporal.
12. **Alertas** — fora do escopo local atual; qualquer alerta externo ou canal de notificação exige nova decisão do usuário.
13. **Operação e administração** — saúde do crawler e parsers, erros e latência por domínio, jobs atrasados, métricas, configuração segura e política de retenção.

## 4. Monólito modular, Clean Architecture e vertical slices

Organizar primeiro por capacidade de negócio, não por um conjunto global de pastas `Controllers/Services/Repositories`. Os módulos previstos são:

- **Accounts/Identity:** usuários, sessão, autenticação e políticas de acesso.
- **ProductCatalog:** produto canônico, EAN/GTIN, marca, modelo e variantes.
- **WebDiscovery:** domínios, crawler, índice próprio, extração e adaptadores por domínio.
- **PriceTracking:** acompanhamentos, ofertas confirmadas e observações de preço.
- **PriceAnalysis:** comparação, métricas e séries históricas.
- **Notifications:** não incluído no MVP local; só adicionar mediante decisão futura do usuário.
- **Operations:** estado do crawler, jobs, saúde e configuração operacional.

Dentro de cada módulo, organizar a aplicação por fatias verticais (casos de uso), mantendo o domínio e a infraestrutura sob a fronteira do módulo. Exemplo ilustrativo:

```text
Modules/PriceTracking/
  Domain/                   # entidades, value objects e invariantes
  Features/
    StartTracking/          # endpoint/entrada, validação, handler, resultado
      StartTracking.razor    # componente da tela deste caso de uso, se houver
      StartTrackingEndpoint.cs
      StartTrackingRequest.cs
      StartTrackingValidator.cs
      StartTrackingHandler.cs
      StartTrackingResult.cs
    ConfirmOffer/
    GetPriceHistory/
  Infrastructure/           # mapeamento EF Core e persistência do módulo
  Contracts/                 # contratos públicos necessários a outros módulos
```

**Regra de co-localização:** todos os arquivos específicos de um caso de uso ficam dentro da pasta desse caso de uso. Não espalhar endpoints, requests, validators, handlers, resultados, mapeamentos ou componentes correspondentes em pastas globais organizadas apenas pelo tipo do arquivo. Se a fatia crescer, podem existir subpastas técnicas dentro da própria pasta do caso de uso, sem mover parte dela para fora. Só extrair um arquivo para uma área compartilhada quando ele for realmente usado por mais de uma fatia e tiver um dono claro no módulo.

### Regras de dependência e responsabilidade

- **Domain:** regras invariantes e linguagem do negócio; não referencia EF Core, ASP.NET Core, Blazor, PostgreSQL nem HTTP.
- **Features/Application:** cada slice orquestra um caso de uso, valida entrada, aplica políticas de autorização pertinentes e coordena transação/saída. Interfaces ficam no módulo que precisa delas.
- **Infrastructure:** implementa persistência, crawler, parsers e clientes externos por meio das abstrações do módulo; detalhes técnicos não vazam para UI ou domínio.
- **Server/API:** endpoints REST finos traduzem HTTP para um slice; não contêm regras de negócio. O host compõe módulos, autenticação e autorização.
- **Blazor:** componentes cuidam de apresentação, estado de tela e validação de experiência. Chamam casos de uso Application no servidor; não contêm regras do produto, cálculos de preço, acesso a `DbContext` ou coleta HTTP.
- **Coesão da fatia:** mudanças em um caso de uso devem ser localizáveis e revisáveis na pasta dele; código compartilhado permanece fora apenas quando houver reutilização real, nunca para criar uma pasta genérica por tipo.
- **Worker:** recebe jobs duráveis e invoca os slices/capacidades de aplicação apropriados; não duplica regras de negócio.
- **Comunicação entre módulos:** usar contratos explícitos ou eventos de integração/outbox. Um módulo não consulta diretamente as tabelas, entidades internas ou `DbContext` de outro. Um único PostgreSQL pode hospedar os dados, com propriedade das tabelas definida por módulo.
- **DDD/SOLID:** definir agregados e value objects onde protegem invariantes reais; manter o shared kernel pequeno; usar abstrações nas fronteiras necessárias, sem interface/classe genérica para cada tipo por padrão.

### Componentes de implantação previstos

- `PriceAnalyzer.Server` — host ASP.NET Core que serve Blazor Interactive Server e endpoints REST versionados; composition root dos módulos, Identity e políticas.
- `PriceAnalyzer.Worker` — descoberta/crawl, atualização de páginas/ofertas, retentativas e processamento de jobs.
- `Modules/*` — módulos co-localizados na solução, cada um com Domain, Features, Infrastructure e contratos próprios. Podem ser assemblies separados para reforçar fronteiras, mantendo a implantação como monólito modular.

No MVP, `Server` é um host de UI e API e `Worker` é outro processo. Ambos podem operar sobre uma base PostgreSQL compartilhada; isso não transforma os módulos em microserviços nem permite acesso cruzado às tabelas internas.

### 4.1. Spec-Driven Development e artefatos de domínio

O projeto seguirá Spec-Driven Development (SDD): especificações aprovadas, contexto do domínio e decisões registradas guiam o desenho e a implementação. O objetivo é manter linguagem, regras e critérios de aceite rastreáveis fora do código e evitar que decisões de negócio fiquem implícitas em componentes ou handlers.

- `docs/domain/context.md` é a referência contínua para a linguagem ubíqua, conceitos, invariantes compartilhadas, limites e perguntas ainda abertas do domínio.
- Cada capacidade/caso de uso terá sua própria pasta em `docs/specs/<Modulo>/<CasoDeUso>/`, contendo `spec.md` e, quando necessário, `plan.md` e `tasks.md`. Artefatos da fatia ficam juntos, como os arquivos específicos do caso de uso no código.
- Decisões transversais de arquitetura ou produto serão registradas como ADRs em `docs/decisions/`, com contexto, alternativas, decisão, estado e consequências.
- O ciclo é: entender e atualizar o contexto -> especificar comportamento e cenários de aceite -> revisar segurança, domínio e fronteiras dos módulos -> planejar e decompor tarefas -> implementar após aprovação -> verificar contra a especificação e atualizar o conhecimento quando necessário.
- Especificações distinguem decisão aprovada, hipótese e questão aberta. Regras compartilhadas pertencem ao contexto; comportamento específico pertence à spec da fatia.
- A implementação do MVP não começa até o roadmap e o conjunto de especificações necessário para o primeiro lançamento estarem documentados e aprovados pelo usuário.

### 4.2. Estratégia de testes rastreável às specs

- Critérios de aceite nas specs recebem IDs estáveis `AC-...`; regras/invariantes compartilhadas recebem IDs `DR-...` no contexto do domínio. Os testes referenciam esses IDs para deixar visível qual comportamento aprovado protegem.
- Cada caso de uso tem testes automatizados que cobrem seus critérios de aceite, caminhos alternativos e falhas relevantes. A spec lista as classes de domínio afetadas e os critérios/regras que cada uma realiza; cada classe concreta do domínio tem testes associados a esses critérios, invariantes e comportamentos.
- Manter assemblies/projetos de teste separados do código de produção, espelhando o módulo e o caso de uso. Exemplo: `tests/Modules/PriceTracking.Tests/Features/StartTracking/`; todos os testes daquele caso de uso ficam juntos. Testes de classes de domínio ficam sob `tests/Modules/<Modulo>.Tests/Domain/` e são organizados pela classe testada.
- Testes de domínio verificam invariantes e transições; testes de caso de uso verificam resultados e efeitos observáveis; testes de integração cobrem fronteiras reais de persistência, fila, HTTP e autorização quando aplicável. A estratégia exata fica no plano da fatia.
- A matriz de rastreabilidade da spec deve apontar cada `AC-...` e `DR-...` aplicável para um ou mais testes automatizados aprovados. Evitar testes tautológicos de detalhes internos; testar o comportamento e a regra que justificam a classe.
- A implementação de uma fatia só está concluída quando seus testes relacionados passam e os critérios de aceite estão cobertos. Não definir um percentual global arbitrário de cobertura como substituto para essa rastreabilidade.

## 5. Modelo de dados conceitual

| Entidade | Finalidade | Campos relevantes |
|---|---|---|
| `User` | Conta local provisionada pelo administrador e gerenciada pelo Identity | ID, identificador de login e estado |
| `Product` | Produto canônico | GTIN/EAN, marca, modelo, atributos/variante |
| `TrackedProduct` | Produto seguido por uma conta | UserId, ProductId, texto ou EAN/GTIN da busca, frequência, ativo |
| `WebSource` | Site/domínio de compras e política de coleta | domínio, nome, sitemap, estado, frequência/limites, última coleta |
| `IndexedProductPage` | Página de produto localizada e indexada | WebSourceId, URL canônica, GTIN/EAN, título normalizado, hash, descoberta/coleta |
| `Offer` | Oferta extraída de uma página | WebSourceId, ID externo se existir, URL, vendedor se disponível, ProductId, condição/variante |
| `PriceObservation` | Uma leitura histórica da oferta | OfferId, coletadoEmUtc, preço do item, moeda, disponibilidade |
| `CollectionJob` | Descoberta, crawl ou atualização a processar | tipo, domínio/URL/produto, estado, tentativas, agendadoEmUtc, erro resumido |
| `PriceAlert` / `Notification` | Conceitos futuros, fora do escopo atual | Só serão detalhados após nova decisão do usuário |

### Regras importantes dos dados

- Produto e anúncio são registros diferentes; anúncios de vendedores diferentes não devem ser fundidos.
- Preço é um valor decimal com moeda; não usar ponto flutuante binário.
- Guardar horário de observação em UTC e exibir no fuso do usuário.
- O MVP compara apenas o preço anunciado do item. Não solicitar CEP nem somar frete; frete pode ser reavaliado numa fase futura.
- Não misturar variantes, tamanhos, capacidade, cor, novo/usado ou kits incompatíveis na mesma comparação.
- Anúncio fora de estoque é um estado, nunca um preço zero.
- Armazenar no MVP os campos normalizados e uma referência à fonte; só guardar payload bruto de forma temporária se for necessário para depuração e permitido pela fonte.
- Manter no índice próprio apenas campos necessários para localizar e comparar produtos; a retenção de texto, imagens ou HTML integral deve ser avaliada por domínio e não é requisito do MVP.

## 6. Contratos da Web API previstos

Rotas preliminares, não finais:

- `POST /api/v1/tracked-products` — cadastrar uma consulta/produto para acompanhar.
- `GET /api/v1/tracked-products` e `GET /api/v1/tracked-products/{id}` — listar e consultar itens do usuário.
- `POST /api/v1/tracked-products/{id}/search` — solicitar busca inicial; retorna identificador/estado do job.
- `GET /api/v1/tracked-products/{id}/offers` — listar anúncios encontrados e confirmados.
- `POST /api/v1/tracked-products/{id}/offers/{offerId}/confirm` — confirmar correspondência se necessário.
- `POST /api/v1/tracked-products/{id}/refresh` — solicitar atualização manual.
- `GET /api/v1/tracked-products/{id}/history?from=...&to=...` — série histórica.
- `GET /api/v1/jobs/{id}` — acompanhar uma operação assíncrona.
- Rotas de alerta e preferências entram depois do MVP básico.

A API deve conferir propriedade do recurso pelo usuário autenticado em cada leitura e alteração; ocultar botão na interface não é controle de acesso.

## 7. Fluxos principais

### Adicionar produto

1. Usuário informa texto ou GTIN/EAN.
2. API valida e cria o acompanhamento e jobs de busca/crawl.
3. O índice local encontra páginas já conhecidas; o Worker descobre novas páginas em vários domínios configurados por sitemaps, catálogos ou páginas de busca pública dos próprios sites, respeitando a política de cada domínio.
4. Parsers extraem nome, identificadores, preço, moeda, disponibilidade e URL. O sistema deduplica candidatos e apresenta a origem e a hora da coleta.
5. Usuário confirma ofertas correspondentes; o sistema guarda a relação entre produto canônico e páginas/anúncios.
6. Primeira observação de preço fica visível com fonte e horário.

### Acompanhar preço

1. O agendador cria jobs por página/oferta acompanhada. A busca ampla no índice ocorre ao adicionar/atualizar a consulta; atualização de preço usa URLs já confirmadas, sem recrawlear toda a web a cada ciclo.
2. Worker atualiza páginas conforme a política configurada para cada domínio e grava uma observação ou registra indisponibilidade/falha. O intervalo-alvo de seis horas é configurável e pode variar por domínio.
3. A tela mostra preço atual do item, data/hora da coleta, moeda e disponibilidade.
4. A página do produto plota o histórico da oferta e compara somente ofertas compatíveis.

## 8. Estratégia de descoberta e limites

| Mecanismo | O que permite | Plano |
|---|---|---|
| Crawler e índice próprios | Descobrir páginas públicas de vários domínios e pesquisar localmente por texto/EAN/GTIN. Sitemaps, páginas de busca dos próprios sites, catálogos, links e dados estruturados ajudam a localizar e interpretar páginas. A cobertura depende dos domínios conhecidos, das regras de acesso e da capacidade de atualização. | Caminho principal do MVP; meta de piloto em pelo menos três domínios, expansível por configuração e adaptadores. Nenhum serviço pago de busca é requisito. |
| Dados estruturados `schema.org` | `Product` e `Offer` podem expor identificadores, preço, moeda e disponibilidade em JSON-LD/Microdata/RDFa. Nem todo site publica esses dados e a presença deles não garante que sejam completos ou atuais. | Usar parser genérico primeiro; adaptar por domínio quando necessário e validar os valores extraídos contra a página visível. |
| APIs de busca web | Podem ajudar a descobrir URLs fora dos domínios já indexados, mas criam custo, dependência e condições de armazenamento. | Fora do escopo atual; a cobertura usa o índice próprio e não depende de provedores externos. |
| APIs de marketplace e feeds de lojistas | Podem oferecer dados estruturados, mas têm condições, cotas e dependências próprias. | Fora do escopo atual; não usar como fonte de dados sem nova decisão aprovada do usuário. |
| Google Merchant API | Gerencia produtos da própria conta Merchant Center; não é uma busca geral de preços entre lojas. | Não usar como índice da web. |

APIs públicas de busca mudam e não são uma base segura para o produto: a Custom Search JSON API do Google está fechada para novos clientes e a Microsoft encerrou as Bing Search APIs em 11/08/2025. A arquitetura deve operar sem essas APIs. Um índice próprio também não cobre automaticamente a Internet inteira: o produto deve comunicar quais domínios foram pesquisados e permitir expandir a cobertura.

### Gate obrigatório por domínio

Antes de habilitar crawling em um domínio, registrar: forma de descoberta (sitemap, catálogo ou busca pública); estrutura e campos; frequência/limites; termos de uso e política de coleta; `robots.txt`; dados que podem ser armazenados; sinais de bloqueio; comportamento em erro; e forma de desabilitar/reprocessar. Só habilitar se os termos públicos aplicáveis permitirem expressamente a coleta automatizada; se forem silenciosos ou ambíguos, manter desabilitado. Não contatar lojas por mensagens, formulários, chamados ou pedidos. As únicas chamadas externas da aplicação serão requisições HTTP GET de leitura a páginas HTML públicas elegíveis. Identificar o crawler com User-Agent informativo, limitar concorrência, respeitar recusas e não contornar login, CAPTCHA ou controles de acesso. O Robots Exclusion Protocol orienta crawlers, mas não substitui autorização ou controles de acesso.

## 9. Requisitos não funcionais

- **Segurança:** senha e sessão tratadas por Identity; acesso por usuário conferido na API; segredos fora do código e protegidos; HTTPS; limites de requisição; logs sem tokens.
- **Privacidade:** coletar apenas dados pessoais necessários, proteger o isolamento entre contas locais, permitir exclusão de dados e revisar requisitos aplicáveis antes da distribuição.
- **Resiliência:** chamadas HTTP com timeout, limite de concorrência por domínio, retentativas com espera crescente e circuit breaker quando apropriado; erro ou bloqueio de um domínio não derruba a busca inteira.
- **Consistência:** jobs duráveis e idempotentes; não perder coletas se a API ou Worker reiniciar.
- **Rastreabilidade:** cada preço apresentado inclui fonte e instante da última leitura.
- **Manutenibilidade:** contrato comum para descoberta, crawling e parsing, com configuração/adaptador próprio por domínio.

## 10. Referências oficiais consultadas

- [.NET: política oficial de suporte](https://dotnet.microsoft.com/en-us/platform/support/policy/dotnet-core)
- [Microsoft Learn: ASP.NET Core Identity](https://learn.microsoft.com/en-us/aspnet/core/security/authentication/identity)
- [Microsoft Learn: modos de renderização do Blazor no .NET 10](https://learn.microsoft.com/en-us/aspnet/core/blazor/components/render-modes?view=aspnetcore-10.0)
- [Microsoft Learn: segurança e Identity no Blazor no .NET 10](https://learn.microsoft.com/en-us/aspnet/core/blazor/security/?view=aspnetcore-10.0)
- [Microsoft Learn: autorização por política](https://learn.microsoft.com/en-us/aspnet/core/security/authorization/policies?view=aspnetcore-10.0)
- [Microsoft Learn: autorização baseada em recurso](https://learn.microsoft.com/en-us/aspnet/core/security/authorization/resourcebased?view=aspnetcore-10.0)
- [Microsoft Learn: proteção antifalsificação (CSRF)](https://learn.microsoft.com/en-us/aspnet/core/security/anti-request-forgery?view=aspnetcore-10.0)
- [MudBlazor: instalação em uma aplicação Blazor](https://mudblazor.com/getting-started/installation)
- [MudBlazor: formulários e validação](https://mudblazor.com/components/form)
- [Microsoft Learn: arquiteturas web comuns e Clean Architecture para .NET](https://learn.microsoft.com/en-us/dotnet/standard/modern-web-apps-azure-architecture/common-web-application-architectures)
- [Microsoft Learn: Worker Services e BackgroundService](https://learn.microsoft.com/en-us/dotnet/core/extensions/workers)
- [Microsoft Learn: providers do EF Core](https://learn.microsoft.com/en-us/ef/core/providers/)
- [Npgsql: notas do provider EF Core 10](https://www.npgsql.org/efcore/release-notes/10.0.html)
- [Brave Search API: busca web, preços e direitos de armazenamento](https://brave.com/search/api/)
- [Google: estado da Custom Search JSON API](https://developers.google.com/custom-search/v1/overview)
- [Microsoft: encerramento das Bing Search APIs](https://learn.microsoft.com/en-us/lifecycle/announcements/bing-search-api-retirement)
- [Schema.org: Product](https://schema.org/Product)
- [Schema.org: Offer](https://schema.org/Offer)
- [Schema.org: price](https://schema.org/price)
- [IETF RFC 9309: Robots Exclusion Protocol](https://www.rfc-editor.org/rfc/rfc9309.html)
- [Google: Merchant API — gerir produtos próprios](https://developers.google.com/merchant/api/guides/products/add-manage)
- [Planalto: Lei Geral de Proteção de Dados](https://www.planalto.gov.br/ccivil_03/_ato2015-2018/2018/lei/l13709.htm)
