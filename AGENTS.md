# AGENTS.md — Analisador de preços

## Objetivo e estado do repositório

Este repositório está na fase de planejamento. Não crie a aplicação, scaffolding ou código de produção até que o usuário aprove o roadmap e as especificações do MVP. Artefatos de planejamento e especificações podem ser criados/atualizados nessa fase.

## Antes de trabalhar

1. Leia `docs/domain/context.md` para linguagem ubíqua, regras `DR-...`, decisões e questões abertas do domínio.
2. Leia `docs/arquitetura-inicial.md` e `docs/roadmap.md` para stack, arquitetura e limites aprovados.
3. Para uma funcionalidade, leia a spec em `docs/specs/<Modulo>/<CasoDeUso>/spec.md` e seus `plan.md`/`tasks.md`, se existirem. Não invente regra de negócio que não esteja na spec ou no contexto; registre lacunas como perguntas/hipóteses.
4. Respeite o status do artefato. Spec em rascunho ou revisão não autoriza implementação.

## Spec-Driven Development

- Trate as especificações aprovadas como contrato de comportamento e fonte dos critérios de aceite. O contexto de domínio preserva conhecimento compartilhado; specs preservam comportamento de cada caso de uso.
- Mudanças de requisito começam pela atualização/revisão da spec, não por uma alteração isolada no código. Separe claramente decisões, hipóteses e questões abertas.
- Use IDs estáveis `AC-...` para critérios de aceite e `DR-...` para regras/invariantes compartilhadas. Mantenha na spec a matriz que liga critérios e regras às classes de domínio e testes.
- Antes da primeira implementação, exija roadmap e conjunto de specs do MVP aprovados pelo usuário. Antes de cada incremento posterior, exija sua spec aprovada.
- Ao descobrir uma regra compartilhada nova, atualize `docs/domain/context.md` e as specs impactadas.
- Siga o ciclo e o template em `docs/specs/README.md` e `docs/specs/_template.md`.

## Stack e decisões do produto

- Backend: .NET / ASP.NET Core Web API; Blazor Web App Interactive Server; PostgreSQL com EF Core e provider Npgsql. Worker .NET separado é condicional e só entra quando uma spec de descoberta/atualização automática for aprovada; o MVP manual não exige Worker.
- Frontend: usar MudBlazor como biblioteca de componentes. Seguir as diretrizes da seção 2.2 de `docs/arquitetura-inicial.md` e a documentação oficial da versão escolhida.
- Autenticação: ASP.NET Core Identity com várias contas locais no PostgreSQL e cookie protegido. MFA é obrigatória para administradores e opcional para os demais usuários, usando TOTP em app autenticador e códigos de recuperação de uso único, sem depender de e-mail. Se alguém perder o autenticador e todos os códigos, a recuperação de MFA de qualquer conta é feita exclusivamente por comando local no host, disponível a qualquer pessoa com acesso local ao terminal; não há redefinição de MFA pelo app nem por e-mail. O comando solicita o login, exibe a conta correspondente e exige confirmação explícita antes do reset. Após o reset, a conta precisa cadastrar novamente o TOTP antes do acesso normal quando MFA for obrigatória ou estiver habilitada. Auditar o instante UTC, a conta alvo, o usuário do sistema operacional que executou o comando e o resultado, sem registrar segredos. O administrador local provisiona as contas e redefine credenciais pelo app, definindo uma senha temporária e entregando-a diretamente ao usuário; no próximo login, ele deve escolher uma nova senha antes de continuar. Não expor nem manter senhas temporárias ou códigos de recuperação em logs ou texto puro. Não há cadastro público nem hospedagem pública. O primeiro administrador é criado por comando local de uso único, com senha informada sem valor padrão; a recuperação da credencial administrativa usa comando local no host e não depende de e-mail. A API é privada e usada pelo próprio sistema no MVP; não adicionar emissão JWT, servidor OIDC ou OpenIddict sem uma mudança aprovada no requisito.
- Uso e implantação: aplicação local para uso próprio, com vários usuários autenticados. O acesso ocorre somente na máquina hospedeira; limitar o host e a API a loopback/localhost, sem exposição à rede local ou à Internet. Não projetar hospedagem pública ou cadastro aberto.
- Preferência de dependências: priorize bibliotecas oficiais/suportadas pela Microsoft. MudBlazor é exceção explícita para UI; qualquer outra dependência de terceiros precisa de justificativa e avaliação de compatibilidade, suporte/manutenção, segurança e licença.
- Descoberta de produtos: o MVP é manual e não consulta a Internet. Em etapa futura, usar somente páginas HTML públicas do próprio domínio da loja, e apenas quando os termos permitirem expressamente coleta automatizada. Não usar APIs, feeds, agregadores ou integrações de terceiros como fonte de dados. Cada domínio exige evidência pública para o uso, campos, apresentação e retenção pretendidos; termos silenciosos ou ambíguos mantêm-no desabilitado. `robots.txt` sozinho não é autorização. Não contatar lojas nem alterar anúncios, contas ou transações; não contornar autenticação, CAPTCHA ou controles de acesso. A cobertura futura é incremental, sem alegar busca exaustiva na Internet.
- Escopo inicial: cadastro manual por texto e/ou EAN/GTIN, entrada manual de ofertas e preços, comparação do preço do item sem frete e análise histórica. URL informada é referência e não causa requisição de rede. Observações manuais precisam ser identificadas como tal. A frequência de seis horas é meta futura condicionada às regras e limites de cada domínio.
- Privacidade/retenção decididas para o MVP: acompanhamentos e históricos são privados por conta local; observações e dados do acompanhamento são apagados quando o acompanhamento é removido, respeitando limites ou requisitos aplicáveis à fonte. Validar as condições de cada domínio antes de habilitar coleta.

## Arquitetura e organização do código

- Use monólito modular, organizado por capacidades de negócio e slices verticais. Não introduza microserviços sem decisão aprovada.
- Módulos de referência: `Accounts/Identity`, `ProductCatalog`, `WebDiscovery`, `PriceTracking`, `PriceAnalysis` e `Operations`. No MVP manual, funcionalidades de descoberta e gestão de conectores/jobs em `WebDiscovery`/`Operations` ficam condicionais a uma spec HTML futura aprovada. `Notifications` fica fora do escopo local atual e só volta ao plano mediante decisão explícita.
- Estrutura de referência: `Modules/<Module>/{Domain,Features,Infrastructure,Contracts}`.
- Cada caso de uso tem sua própria pasta sob `Features/<UseCase>/`. Mantenha juntos todos os arquivos específicos daquela fatia: componente Razor, endpoint, request/command, validator, handler, result, mapping e outros arquivos auxiliares. Não crie pastas globais organizadas apenas por tipo de arquivo. Subpastas técnicas são permitidas dentro da pasta do caso de uso.
- Co-localize também os artefatos próprios da fatia em `docs/specs/<Module>/<UseCase>/` e agrupe seus testes na pasta espelhada do projeto de testes.
- Blazor é apresentação: componentes cuidam de renderização, estado de tela e encaminhamento de ações. Regras de domínio, cálculos, autorização de recursos, orquestração e persistência ficam no backend/caso de uso. Não acesse `DbContext` nem faça chamadas externas às fontes em componentes.
- Endpoints REST traduzem HTTP para casos de uso e permanecem finos. Se for aprovado, o Worker processa jobs e chama as mesmas capacidades/casos de uso; não duplique regras. Não criar fila ou Worker para simular automação no MVP manual.
- `Domain` não referencia EF Core, ASP.NET Core, Blazor, PostgreSQL nem HTTP. `Infrastructure` implementa portas do módulo. Módulos não acessam diretamente entidades, tabelas ou `DbContext` de outros módulos; use contratos ou eventos explícitos.
- Aplique DDD, Clean Architecture, OOP e SOLID de forma pragmática: proteja invariantes reais, mantenha shared kernel pequeno e não crie abstrações genéricas sem necessidade demonstrada.

## Testes e rastreabilidade

- Toda classe concreta de domínio deve ter testes associados para seus comportamentos, invariantes e critérios `AC-...`/regras `DR-...` aplicáveis.
- Todo caso de uso deve ter testes automatizados para seus critérios de aceite, fluxos alternativos e falhas relevantes. A spec identifica as classes de domínio afetadas e aponta os testes na matriz de rastreabilidade.
- Mantenha projetos de teste separados dos assemblies de produção, espelhando módulo, caso de uso e classe. Exemplo: `tests/Modules/PriceTracking.Tests/Features/StartTracking/`; testes do mesmo caso de uso ficam juntos nessa pasta. Testes de domínio ficam sob `tests/Modules/<Module>.Tests/Domain/`, organizados pela classe testada.
- Testes de domínio exercitam invariantes/transições; testes de caso de uso exercitam resultados e efeitos observáveis; testes de integração cobrem persistência, jobs, HTTP e autorização quando aplicável.
- Ao implementar uma spec, crie/atualize os testes necessários e mantenha IDs de aceite e regras no nome/descrição ou na matriz de rastreabilidade. Uma fatia não está concluída se seus critérios aplicáveis não estiverem cobertos por testes aprovados.
- O framework de testes ainda será selecionado; priorize tooling oficial/suportado pela Microsoft conforme a regra de dependências.

## Regras do domínio e da coleta

- Produto, página de produto, oferta e observação de preço são conceitos separados. Não fundir anúncios de vendedores distintos nem variantes, condições ou kits incompatíveis.
- Representar dinheiro com decimal e moeda; persistir instantes em UTC; observações válidas preservam o histórico. Indisponibilidade nunca significa preço zero.
- Registrar proveniência, autoria e instante UTC para cada preço apresentado; observações manuais devem ser rotuladas como informadas pelo usuário. Frete não participa da comparação inicial.
- Antes de implementar/habilitar um conector HTML futuro, guardar evidência pública de que os termos permitem expressamente coleta automatizada pelo app local, os campos apresentados e a retenção histórica; avaliar também `robots.txt`, limites, frequência e falhas. `robots.txt` não prova autorização. APIs, feeds, agregadores e integrações de terceiros não são fontes. Termos silenciosos, ambíguos ou incompatíveis mantêm o domínio desabilitado. Não contatar lojas nem enviar mensagens, formulários, chamados ou pedidos de autorização. Não contornar autenticação, CAPTCHA, limites ou controles de acesso.
- Quando uma coleta HTML futura for aprovada, isolar erros por domínio, limitar concorrência, usar timeouts/backoff e manter jobs persistidos/idempotentes se a spec exigir processamento assíncrono.

## Segurança e privacidade

- Exija autenticação por padrão e libere explicitamente apenas endpoints realmente públicos.
- Derive a identidade do usuário do principal autenticado. Nunca confie em `UserId` enviado pelo cliente como prova de propriedade; verifique acesso ao recurso no backend em cada operação.
- Para requisições HTTP autenticadas por cookie que alterem dados, aplique antifalsificação. Mantenha cookie HTTPS com `Secure`, `HttpOnly` e `SameSite` apropriados.
- Use os fluxos suportados pelo Identity para senha, redefinição administrativa, lockout e MFA conforme aplicável; nunca implemente hash/sal de senha manualmente.
- Proteja segredos fora do controle de versão e persista/proteja chaves ASP.NET Core Data Protection entre reinícios/instâncias.
- O MVP manual não usa credenciais de fontes. Se uma spec futura de HTML demonstrar uma necessidade legítima, definir antes o armazenamento protegido fora do repositório e impedir que qualquer segredo apareça em commits, issues, pull requests, logs, telemetria, UI após salvamento ou argumentos de processo. `.gitignore` não substitui armazenamento seguro nem revisão de segredos.
- Minimize dados pessoais, mantenha histórico e acompanhamentos no escopo de propriedade aprovado, e considere exclusão de conta/dados no desenho.

## Idioma e documentação

- Converse com o usuário e escreva documentação de produto em português. Mantenha nomes de APIs, tipos, classes, namespaces e termos de código em inglês conforme as convenções .NET.
- Ao alterar comportamento, atualize a spec, contexto de domínio, roadmap ou ADR pertinente na mesma mudança.
- Não marque hipótese como decisão aprovada nem prometa suporte de um domínio sem validação documentada.

## Operações Git

- O usuário autorizou gravar alterações e executar comandos Git neste repositório, incluindo `status`, `diff`, `log`, `add`, `commit`, `fetch`, `pull` e `push` para o remote configurado, quando necessários à tarefa solicitada. Não peça autorização a cada operação rotineira.
- Antes de preparar ou registrar alterações, confira o estado e o diff; selecione apenas arquivos pertencentes à tarefa atual e preserve alterações não relacionadas.
- Para apagar alterações locais, reescrever histórico publicado, usar `push --force` ou remover branches/tags, solicite autorização específica para a ação.
