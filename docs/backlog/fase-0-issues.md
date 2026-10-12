# GitHub Issues — Fase 0

**Estado:** os rascunhos revisados para C estão na branch publicada `docs/phase-0-hybrid`. A leitura do repositório funciona, mas a tentativa de `update_issue` retornou HTTP 403. A sessão do navegador disponível está desconectada do GitHub; nenhuma issue foi alterada por ela. `gh auth status` confirma token local inválido; reautenticar com `gh auth refresh -h github.com` antes de publicar. PLAN-000 a PLAN-009 continuam com os corpos remotos antigos; PLAN-010 a PLAN-012 não foram criados. Não há PR aberto; a página para criá-lo é [esta](https://github.com/devmichaelcosta/dotnet-price-analizer/pull/new/docs/phase-0-hybrid).
**Épico:** [`PLAN-000` — Concluir planejamento e aprovar as specs do MVP](https://github.com/devmichaelcosta/dotnet-price-analizer/issues/1).

---

## PLAN-000 — [type:epic] Concluir planejamento e aprovar as specs do MVP ([#1](https://github.com/devmichaelcosta/dotnet-price-analizer/issues/1))

**Milestone:** Fase 0 — Planejamento e validação de fontes
**Prioridade:** P0
**Labels:** `type:epic`, `priority:P0`, `area:product`

### Objetivo

Concluir a definição do MVP antes de criar aplicação ou código de produção.

### Contexto

O roadmap exige fechar as decisões do MVP manual, arquitetura e segurança, estratégia de testes e specs aprovadas. O usuário escolheu o núcleo manual primeiro e automação HTML futura condicionada a gate por domínio. APIs, feeds e integrações de terceiros não são fontes de dados. O projeto não contatará lojas por mensagens, formulários, chamados ou pedidos.

### Critérios de conclusão

- [x] A falta de fontes elegíveis foi registrada e o usuário escolheu o escopo C; fontes automáticas não bloqueiam o MVP manual.
- [ ] As regras abertas do domínio, segurança, arquitetura, privacidade e escala foram resolvidas ou explicitamente mantidas fora do MVP.
- [ ] Todas as specs do MVP manual estão revisadas e aprovadas, com IDs `AC-...`, regras `DR-...` aplicáveis e matriz de rastreabilidade; automação HTML está classificada como futura/condicional.
- [ ] Roadmap, contexto do domínio e estratégia SDD/testes foram atualizados.
- [ ] O usuário aprovou o gate de planejamento antes de qualquer implementação.

### Tarefas filhas

- [x] PLAN-001 — Concluir triagem HTML e registrar o gate documental futuro; nenhum domínio elegível foi aprovado.
- [x] PLAN-002 — Encerrar a análise de páginas HTML de fontes inelegíveis; não há amostra de produto aprovada para inspecionar.
- [ ] PLAN-003 — Fechar regras de produto, correspondência e observações.
- [ ] PLAN-004 — Definir política de autenticação, conta e autorização.
- [ ] PLAN-005 — Fechar arquitetura modular, contratos, dados e fluxos.
- [ ] PLAN-006 — Definir estratégia de specs, testes e rastreabilidade.
- [ ] PLAN-007 — Escrever e revisar as specs do MVP.
- [x] PLAN-008 — Estimar volume e armazenamento do fluxo manual; adiar carga de automação até haver fonte elegível.
- [ ] PLAN-009 — Revisar e aprovar o gate Fase 0.
- [x] PLAN-010 — Estratégia API-first substituída por núcleo manual e futura automação HTML condicionada.
- [ ] PLAN-011 — Registrar escolha C e alinhar roadmap/specs/backlog; decisão de direção tomada, alinhamento ainda em curso.
- [ ] PLAN-012 — Fechar campos manuais, proveniência, atualidade e comparabilidade de ofertas.

### Referências

- `docs/roadmap.md`
- `docs/domain/context.md`
- `docs/research/validacao-fontes-fase-0.md`
- `docs/specs/README.md`

---

## PLAN-001 — [type:research] Concluir triagem HTML e registrar o gate futuro ([#2](https://github.com/devmichaelcosta/dotnet-price-analizer/issues/2))

**Milestone:** Fase 0
**Prioridade:** P0
**Labels:** `type:research`, `priority:P0`, `area:discovery`

### Objetivo

Concluir a avaliação inicial de páginas HTML públicas e registrar o gate para um eventual conector futuro, sem bloquear o MVP manual. Nenhum domínio HTML foi aprovado.

### Escopo

- A pesquisa manual já consultou os candidatos registrados no relatório; ela não executou crawler, parser, navegador automatizado nem coleta de produto.
- Manter a classificação por domínio baseada nos termos aplicáveis, nos campos e na retenção pretendida. `robots.txt` isoladamente não comprova permissão.
- Não usar APIs, feeds, agregadores ou provedores de terceiros como fontes; não autenticar, contatar lojas, criar contas, obter credenciais ou fazer crawler.

### Situação atual

O usuário escolheu o MVP híbrido: entrada e análise manuais primeiro; automação somente em páginas HTML que passem gate individual. A pesquisa registrada não aprovou domínio. A pesquisa exploratória de APIs foi preservada no relatório de fontes, mas APIs não são fontes no escopo vigente. Nenhuma loja foi contatada.

### Critérios de conclusão

- [x] A triagem documental registra os candidatos e distingue proibição explícita, evidência inconclusiva e falta de permissão expressa; nenhum domínio foi aprovado.
- [x] Ambiguidade, falta de permissão expressa ou incompatibilidade mantém o domínio desabilitado; `robots.txt` não é tratado como autorização.
- [x] O relatório de fontes, roadmap, contexto e ADR distinguem o MVP manual dos conectores HTML condicionais futuros.

### Dependências

- Part of: PLAN-000

### Referências

- `docs/research/validacao-fontes-fase-0.md`
- `AGENTS.md` — regras de coleta

---

## PLAN-002 — [type:research] Encerrar análise de páginas sem fontes elegíveis ([#3](https://github.com/devmichaelcosta/dotnet-price-analizer/issues/3))

**Milestone:** Fase 0
**Prioridade:** P0
**Labels:** `type:research`, `priority:P0`, `area:discovery`

### Objetivo

Avaliar manualmente páginas de produto e de busca apenas em domínios HTML elegíveis. Como a triagem não aprovou nenhum domínio, não há páginas de produto que possam ser analisadas para o MVP atual; encerrar a atividade sem automação.

### Critérios de conclusão

- [x] A triagem documental não aprovou nenhum domínio HTML para coleta automatizada no escopo pretendido.
- [x] A ausência de fonte elegível foi registrada e a opção C foi escolhida; amostras de páginas de produto ficam fora do MVP manual.
- [x] Nenhum crawler, parser, navegador automatizado, acesso autenticado ou contato com loja foi usado; a análise manual já registrada permanece documental.

### Dependências

- Depends on: PLAN-001
- Part of: PLAN-000

### Referências

- `docs/research/validacao-fontes-fase-0.md`
- `docs/research/revisao-escopo-mvp.md`

---

## PLAN-003 — [type:decision] Fechar regras de produto, correspondência e observações ([#4](https://github.com/devmichaelcosta/dotnet-price-analizer/issues/4))

**Milestone:** Fase 0
**Prioridade:** P0
**Labels:** `type:decision`, `priority:P0`, `area:domain`

### Objetivo

Definir a identidade de produto e oferta manual, proveniência de observações e regras para comparar preços entre lojas e ao longo do tempo.

### Decisões a documentar

- Regras para EAN/GTIN inválido, ausente, duplicado ou conflitante.
- Campos mínimos de produto/oferta manual, loja/URL, variante, condição e tratamento de duplicatas.
- Como identificar a autoria e o instante de uma observação informada pelo usuário; mostrar claramente que não foi verificada pela loja.
- Regras para comparar ofertas de lojas distintas e determinar a atualidade do último preço.
- **Decisão aprovada para coleta automatizada futura:** na mesma oferta, registrar observação somente quando preço ou disponibilidade mudarem; leituras automáticas idênticas não criam duplicatas (`DR-016`). Isso não define se uma ação manual explícita com o mesmo valor cria nova observação; fechar em F0-16.
- Como tratar preço PIX, parcelamento, preço promocional, faixas de preço, moeda e indisponibilidade na entrada manual.
- [x] Ao remover um acompanhamento, apagar imediatamente seus dados privados vinculados, sem lixeira/desfazer (`F0-10`, `DR-013`); retenção de backups locais continua pendente em F0-12.

### Critérios de conclusão

- [ ] Cada decisão tem regra, justificativa e exemplos observáveis.
- [ ] Regras compartilhadas recebem IDs `DR-...` em `docs/domain/context.md`.
- [ ] Hipóteses e casos fora do MVP permanecem identificados como tais.
- [ ] Specs impactadas referenciam as regras adotadas.

### Dependências

- Depends on: PLAN-002, quando a decisão depender dos campos observáveis nas fontes.
- Part of: PLAN-000

### Referências

- `docs/domain/context.md`
- `docs/roadmap.md`

---

## PLAN-004 — [type:decision] Definir política de autenticação, conta e autorização ([#5](https://github.com/devmichaelcosta/dotnet-price-analizer/issues/5))

**Milestone:** Fase 0
**Prioridade:** P0
**Labels:** `type:decision`, `priority:P0`, `area:security`

### Objetivo

Definir os fluxos do ASP.NET Core Identity para uma aplicação local com vários usuários provisionados por um administrador, sem cadastro público, e os controles de autorização dos recursos privados.

### Decisões a documentar

- O primeiro administrador será criado por comando local de uso único no host, com senha informada sem valor padrão. Detalhar a guarda que impede reuso do bootstrap e como proteger a senha para que não apareça em argumentos, logs ou arquivos versionados.
- Decisão aprovada: recuperação administrativa por comando local no host, sem depender de e-mail. Detalhar a precondição de uso, os mecanismos do Identity e a proteção da senha contra exposição em argumentos, logs ou arquivos versionados.
- Decisão aprovada: administrador define pelo app uma senha temporária e a entrega diretamente ao usuário. No próximo login, o usuário precisa definir uma nova senha antes de continuar. Não expor nem manter a senha temporária em texto puro; usar os mecanismos suportados pelo Identity.
- Provisionamento/desativação de usuários sem cadastro público, identificador de login, política de senha e resposta a perda de acesso dessas contas.
- Decisão aprovada: MFA TOTP obrigatória para administradores e opcional para os demais usuários, com códigos de recuperação de uso único e sem dependência de e-mail. Se alguém perder o autenticador e todos os códigos, a recuperação de MFA de qualquer conta será exclusivamente por comando local no host, disponível a qualquer pessoa com acesso local ao terminal e sem redefinição pelo app ou por e-mail. O comando solicita o login, exibe a conta correspondente e exige confirmação explícita antes do reset. Após o reset, exigir novo cadastro de TOTP antes do acesso normal quando MFA for obrigatória ou estiver habilitada. A spec deve detalhar ativação, exibição segura e consumo dos códigos. A auditoria do comando registra instante UTC, conta alvo, usuário do sistema operacional que o executou e resultado, sem segredos.
- Política de senha compatível com Identity, lockout, sessão/cookie e encerramento de sessão.
- Exclusão/desativação de conta e dos acompanhamentos/históricos privados.
- Política padrão dos endpoints locais, proteção antifalsificação e autorização por proprietário; acesso limitado à máquina hospedeira por loopback/localhost.

### Critérios de conclusão

- [ ] O administrador provisiona contas; não há cadastro público, e os fluxos de login, gestão, redefinição e desativação estão descritos.
- [ ] O comando local cria o primeiro administrador apenas quando o bootstrap ainda não foi concluído, solicita senha sem valor padrão e não expõe a senha em argumentos, logs ou arquivos versionados.
- [ ] O comando local de recuperação permite redefinir a credencial administrativa no host, sem depender de e-mail, e protege a senha contra exposição em argumentos, logs ou arquivos versionados.
- [ ] Pelo app, o administrador define uma senha temporária e a entrega diretamente ao usuário; no próximo login, esse usuário deve definir uma nova senha antes de continuar, e a senha temporária não é exposta nem mantida em texto puro.
- [ ] MFA TOTP é exigida em todas as contas administrativas e pode ser habilitada por usuários comuns; códigos de recuperação são de uso único, exibidos com segurança e sem dependência de e-mail.
- [ ] A perda do autenticador e de todos os códigos só pode ser recuperada por comando local no host, disponível a qualquer pessoa com acesso local ao terminal, sem fluxo pelo app ou por e-mail; a conta precisa cadastrar novamente o TOTP antes do acesso normal quando MFA for obrigatória ou estiver habilitada.
- [ ] O comando solicita o login, exibe a conta correspondente e exige confirmação explícita antes do reset; a auditoria registra instante UTC, conta alvo, usuário do sistema operacional que executou o comando e resultado, sem segredos.
- [ ] Defaults de segurança têm justificativa e usam os mecanismos suportados do ASP.NET Core Identity.
- [ ] Host e API aceitam conexões somente por loopback/localhost e não ficam acessíveis pela rede local.
- [ ] Decisões estão registradas no contexto/ADR adequado e são referenciadas pelas specs de conta.
- [ ] Nenhum emissor JWT/OIDC foi incluído no escopo sem cliente externo aprovado.

### Dependências

- Part of: PLAN-000

### Referências

- `docs/arquitetura-inicial.md`
- `docs/roadmap.md`
- `AGENTS.md` — segurança e privacidade

---

## PLAN-005 — [type:task] Fechar arquitetura modular, contratos, dados e fluxos ([#6](https://github.com/devmichaelcosta/dotnet-price-analizer/issues/6))

**Milestone:** Fase 0
**Prioridade:** P0
**Labels:** `type:task`, `priority:P0`, `area:architecture`

### Objetivo

Produzir um desenho revisável do monólito modular, módulos, dependências, persistência e comunicação entre Blazor, Web API interna e PostgreSQL. Worker/fila ficam condicionais à automação HTML futura.

### Critérios de conclusão

- [x] Fronteiras e responsabilidades dos módulos foram definidas.
- [x] O diagrama mostra entradas Blazor/API e os mesmos casos de uso; a escolha de caminho da UI aguarda F0-18.
- [x] O modelo conceitual preserva Produto, Página, Oferta, Acompanhamento e Observação como conceitos distintos.
- [x] Ownership/isolamento por usuário local está representado; jobs, retentativas e idempotência são condicionais à automação futura.
- [x] A topologia manual executa Server e PostgreSQL localmente; host/API ficam restritos a loopback/localhost, sem acesso remoto. Worker é condicional.
- [x] A regra de fatias verticais mantém os arquivos específicos de cada use case juntos; dependências seguem os limites Clean Architecture/DDD.
- [x] Contratos e modelo foram atualizados após as decisões de domínio/fontes disponíveis.
- [x] Fontes e Worker condicionais estão separados do MVP manual; API/feed/agregador não aparecem como origem de dados.
- [ ] Resolver F0-18: componentes Blazor chamam casos de uso em processo ou consomem a Web API REST privada.

### Dependências

- Depends on: PLAN-001 e PLAN-003
- Part of: PLAN-000

### Referências

- `docs/arquitetura-inicial.md`
- `docs/roadmap.md`
- `docs/domain/context.md`

---

## PLAN-006 — [type:decision] Definir estratégia SDD, testes e rastreabilidade ([#7](https://github.com/devmichaelcosta/dotnet-price-analizer/issues/7))

**Milestone:** Fase 0
**Prioridade:** P0
**Labels:** `type:decision`, `priority:P0`, `area:testing`

### Objetivo

Definir como critérios de aceite das specs e regras de domínio serão cobertos e rastreados até os testes de cada use case e classe de domínio.

### Critérios de conclusão

- [ ] Framework e tooling de testes foram escolhidos priorizando suporte oficial/Microsoft, com justificativa para eventuais dependências externas.
- [ ] A proposta MSTest + `MSTest.Sdk`/`Microsoft.Testing.Platform` em `docs/decisions/ADR-001-framework-de-testes.md` foi revisada no gate da Fase 0.
- [x] A convenção está definida para testes unitários de domínio, testes de use case e testes de integração.
- [x] A matriz das specs manuais liga `AC-...`/`DR-...` a classes de domínio, use cases e testes esperados.
- [x] A localização planejada dos testes espelha módulo/use case/classe e permanece em projetos separados.
- [ ] Gates de revisão/CI estão definidos sem exigir testes para critérios fora do escopo do incremento.
- [x] `docs/specs/README.md` e `docs/specs/_template.md` contêm o ciclo SDD e as regras de rastreabilidade.

### Dependências

- Part of: PLAN-000

### Referências

- `docs/specs/README.md`
- `docs/specs/_template.md`
- `AGENTS.md` — Testes e rastreabilidade

---

## PLAN-007 — [type:spec] Escrever e revisar as specs do MVP ([#8](https://github.com/devmichaelcosta/dotnet-price-analizer/issues/8))

**Milestone:** Fase 0
**Prioridade:** P0
**Labels:** `type:spec`, `priority:P0`, `area:product`

### Objetivo

Criar uma especificação aprovada para cada use case incluído no MVP, co-localizando spec, plano e tarefas na pasta do use case.

### Critérios de conclusão

- [x] O inventário separa specs do MVP manual de specs condicionais de automação HTML.
- [ ] Specs do MVP cobrem cadastro de produto/oferta, autoria/proveniência, atualidade e comparação de preços entre ofertas.
- [ ] Cada spec define objetivo, atores, autorização, regras, validações, cenários de sucesso/alternativos/falha e dados observáveis.
- [ ] Cada critério tem ID estável `AC-...`, critérios usam Given/When/Then quando adequado, e apontam para `DR-...` relevantes.
- [x] A matriz de rastreabilidade das specs manuais identifica classes de domínio, use case e testes derivados.
- [x] Cada spec tem status explícito; nenhuma spec em rascunho autoriza implementação.
- [ ] As specs não deixam decisão crítica implícita; itens adiados estão explicitamente fora do MVP.

### Dependências

- Depends on: PLAN-003, PLAN-004, PLAN-005 e PLAN-006
- Part of: PLAN-000

### Referências

- `docs/specs/README.md`
- `docs/specs/_template.md`
- `docs/domain/context.md`

---

## PLAN-008 — [type:task] Estimar carga manual e armazenamento; automação condicional ([#9](https://github.com/devmichaelcosta/dotnet-price-analizer/issues/9))

**Milestone:** Fase 0
**Prioridade:** P1
**Labels:** `type:task`, `priority:P1`, `area:architecture`

### Objetivo

Estimar o volume local de acompanhamentos/ofertas/observações manuais para o primeiro MVP. Não estimar coleta externa até um domínio HTML passar pelo gate.

### Entradas necessárias

- Número de usuários locais previstos.
- Acompanhamentos médios e máximos por usuário local.
- Número de ofertas manuais e observações registradas por conta.
- Para futura automação: limites/frequência do domínio documentados em spec individual aprovada.

### Critérios de conclusão

- [x] Consultas externas no MVP manual são zero; observações e armazenamento por dia/mês estão estimados em cenários baixo, esperado e de estresse.
- [x] A estimativa informa volume e retenção no banco local, com premissas claramente identificadas como hipóteses.
- [x] Frequência, concorrência e custo de crawler são adiados até existir fonte aprovada; o alvo de seis horas não é requisito do MVP manual.
- [x] Premissas e fórmulas estão registradas em `docs/research/estimativa-capacidade-mvp.md`.

### Dependências

- Depends on: PLAN-001 e PLAN-002
- Part of: PLAN-000

### Referências

- `docs/roadmap.md`
- `docs/research/validacao-fontes-fase-0.md`
- `docs/research/estimativa-capacidade-mvp.md`

---

## PLAN-009 — [type:decision] Revisar e aprovar o gate da Fase 0 ([#10](https://github.com/devmichaelcosta/dotnet-price-analizer/issues/10))

**Milestone:** Fase 0
**Prioridade:** P0
**Labels:** `type:decision`, `priority:P0`, `area:product`

### Objetivo

Verificar se planejamento, fontes, regras, arquitetura, estratégia de testes e specs estão completos para iniciar a implementação do MVP.

### Critérios de conclusão

- [ ] Critérios de saída da Fase 0 foram conferidos contra as evidências e documentos.
- [ ] Dependências sem solução e riscos residuais foram mostrados ao usuário.
- [ ] Roadmap e todas as specs do MVP estão aprovados e com status/data registrados.
- [ ] O usuário aprovou explicitamente o início da implementação.
- [ ] Se qualquer gate falhar, issues de planejamento continuam abertas; não criar issues de implementação como Ready.

### Dependências

- Depends on: PLAN-001 a PLAN-008
- Part of: PLAN-000

### Referências

- `docs/roadmap.md`
- `docs/specs/README.md`

---

## PLAN-010 — [type:decision] Estratégia API-first substituída por HTML condicionado (publicação pendente)

**Milestone:** Fase 0
**Prioridade:** P0
**Labels:** `type:decision`, `priority:P0`, `area:discovery`

### Objetivo

Manter o registro histórico da decisão API-first e documentar sua substituição: núcleo manual no MVP; automação futura somente por HTML público com permissão expressa por domínio.

### Contexto

O ADR-002 registra a decisão API-first anterior e está substituído pelo ADR-003. A pesquisa de APIs é histórica e nenhuma API ficou autorizada. O usuário escolheu C; o núcleo manual vem primeiro e automação HTML fica condicionada ao gate. Não foram criadas contas, aceitos termos, obtidas credenciais ou contatadas lojas.

### Escopo

- Manter a pesquisa API apenas como histórico; não usar API, feed, agregador ou integração de terceiros como fonte sem nova decisão explícita do usuário.
- Garantir que o roadmap e as specs indiquem HTML público expressamente permitido como única forma de automação considerada, somente em etapa futura.

### Critérios de conclusão

- [x] A estratégia API-first foi substituída por ADR-003; APIs, feeds e agregadores não são fontes vigentes.
- [x] Núcleo manual primeiro e coleta HTML futura condicionada foram registrados.
- [x] Contexto, `AGENTS.md`, roadmap, specs e backlog local estão consistentes; sincronização GitHub ainda depende de escrita da integração.

### Dependências

- Depends on: PLAN-001 e PLAN-005
- Part of: PLAN-000

### Referências

- `docs/research/validacao-fontes-fase-0.md`
- `docs/domain/context.md`
- `docs/decisions/ADR-002-fontes-oficiais-api-first.md` (histórico)
- `docs/decisions/ADR-003-mvp-hibrido-e-fontes-html.md`

---

## PLAN-011 — [type:decision] Registrar opção C e alinhar os artefatos do MVP (publicação pendente)

**Milestone:** Fase 0
**Prioridade:** P0
**Labels:** `type:decision`, `priority:P0`, `area:product`

### Objetivo

Registrar a escolha explícita do MVP híbrido e alinhar roadmap, contexto, arquitetura, specs e backlog com a entrada manual inicial e automação HTML condicionada.

### Contexto

A triagem de HTML não aprovou domínio; a pesquisa de APIs é histórica e APIs não são fontes permitidas no escopo vigente. O usuário escolheu a opção C. A decisão está em `docs/research/revisao-escopo-mvp.md` e ADR-003; detalhes do fluxo manual e outras propostas continuam pendentes.

### Escopo

- Manter registrada a escolha C sem inferir aprovação das specs detalhadas.
- Definir campos manuais, proveniência, horário de observação, atualidade e comparabilidade.
- Atualizar roadmap, contexto, specs e issues afetados, preservando como pendentes as escolhas ainda não confirmadas.
- Manter coleta automatizada desabilitada até cada fonte passar pelo gate individual de termos, uso, acesso e retenção.

### Critérios de conclusão

- [x] O usuário escolheu a opção C e a direção está registrada.
- [ ] Detalhes de produto/oferta/observação manual e comparação dos últimos preços informados estão resolvidos ou explicitamente adiados.
- [x] Roadmap, contexto, arquitetura, inventário de specs e backlog descrevem o mesmo escopo; detalhes manuais e aprovações permanecem em aberto nos critérios separados.
- [x] Nenhum domínio HTML ou permissão de coleta foi presumido; nenhum domínio está habilitado.
- [ ] Roadmap e specs revisados recebem aprovação explícita antes da implementação.

### Dependências

- Depends on: PLAN-001 e PLAN-010
- Part of: PLAN-000

### Referências

- `docs/research/revisao-escopo-mvp.md`
- `docs/research/validacao-fontes-fase-0.md`
- `docs/decisions/registro-fase-0.md`
- `docs/roadmap.md`

---

## PLAN-012 — [type:decision] Fechar regras de oferta e observação manual (publicação pendente)

**Milestone:** Fase 0
**Prioridade:** P0
**Labels:** `type:decision`, `priority:P0`, `area:domain`

### Objetivo

Definir os dados e as regras observáveis para cadastrar uma oferta manual, registrar preços e compará-los entre lojas e ao longo do tempo.

### Contexto

O usuário escolheu o núcleo manual primeiro. A spec `AddManualOffer` e a comparação `CompareCurrentOffers` estão em rascunho; campos, proveniência temporal, preço comparável e atualidade ainda aparecem como questões abertas. Uma URL é somente referência no MVP e não será acessada.

### Decisões a documentar

- Campos obrigatórios/opcionais para produto e oferta (loja, título, URL, vendedor, variante e condição).
- Horário que representa observação manual e autoria/proveniência exibida.
- Regras para preço, moeda, disponibilidade, promoções e deduplicação.
- Atualidade do preço e agrupamento de ofertas comparáveis por moeda, variante e condição.
- Tratamento de observação sem preço, indisponível e sem dados suficientes.

### Critérios de conclusão

- [ ] Propostas são aceitas, ajustadas ou adiadas com clareza, sem hipóteses descritas como decisão.
- [ ] `docs/domain/context.md` recebe apenas regras compartilhadas confirmadas e IDs `DR-...` estáveis.
- [ ] Specs `StartTracking`, `ResolveCanonicalProduct`, `AddManualOffer`, `RecordPriceObservation`, `GetPriceHistory`, `CompareCurrentOffers` e `ComparePriceVariation` refletem as decisões.
- [ ] ACs e matrizes de rastreabilidade cobrem sucessos, autorização, validação, duplicação e dados incompatíveis.

### Dependências

- Depends on: PLAN-003, PLAN-005 e PLAN-007
- Part of: PLAN-000

### Referências

- `docs/decisions/registro-fase-0.md` — F0-05 a F0-08 e F0-16/F0-17
- `docs/specs/PriceTracking/AddManualOffer/spec.md`
- `docs/specs/PriceAnalysis/CompareCurrentOffers/spec.md`
