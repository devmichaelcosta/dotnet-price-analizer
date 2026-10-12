# GitHub Issues — Fase 0

**Estado:** os issues foram publicados no GitHub e estão indexados em [`README.md`](README.md).
**Épico:** [`PLAN-000` — Concluir planejamento e aprovar as specs do MVP](https://github.com/devmichaelcosta/dotnet-price-analizer/issues/1).

---

## PLAN-000 — [type:epic] Concluir planejamento e aprovar as specs do MVP ([#1](https://github.com/devmichaelcosta/dotnet-price-analizer/issues/1))

**Milestone:** Fase 0 — Planejamento e validação de fontes  
**Prioridade:** P0  
**Labels:** `type:epic`, `priority:P0`, `area:product`

### Objetivo

Concluir a definição do MVP antes de criar aplicação ou código de produção.

### Contexto

O roadmap exige análise manual de páginas, validação de fontes, decisões de domínio, arquitetura e segurança, estratégia de testes e especificações aprovadas. A coleta usará somente HTML público cuja automação seja permitida expressamente nos termos públicos aplicáveis. O projeto não contatará lojas; APIs, feeds e integrações de terceiros não serão fontes de dados.

### Critérios de conclusão

- [ ] Pelo menos três fontes elegíveis foram comprovadas, ou a inviabilidade dessa meta foi registrada e o escopo foi revisto com o usuário.
- [ ] As regras abertas do domínio, segurança, arquitetura, privacidade e escala foram resolvidas ou explicitamente mantidas fora do MVP.
- [ ] Todas as specs de casos de uso do MVP estão revisadas e aprovadas, com IDs `AC-...`, regras `DR-...` aplicáveis e matriz de rastreabilidade.
- [ ] Roadmap, contexto do domínio e estratégia SDD/testes foram atualizados.
- [ ] O usuário aprovou o gate de planejamento antes de qualquer implementação.

### Tarefas filhas

- [ ] PLAN-001 — Comprovar fontes HTML elegíveis e selecionar o piloto.
- [ ] PLAN-002 — Analisar manualmente páginas das fontes elegíveis.
- [ ] PLAN-003 — Fechar regras de produto, correspondência e observações.
- [ ] PLAN-004 — Definir política de autenticação, conta e autorização.
- [ ] PLAN-005 — Fechar arquitetura modular, contratos, dados e fluxos.
- [ ] PLAN-006 — Definir estratégia de specs, testes e rastreabilidade.
- [ ] PLAN-007 — Escrever e revisar as specs do MVP.
- [ ] PLAN-008 — Estimar carga, frequência, armazenamento e custo.
- [ ] PLAN-009 — Revisar e aprovar o gate Fase 0.

### Referências

- `docs/roadmap.md`
- `docs/domain/context.md`
- `docs/research/validacao-fontes-fase-0.md`
- `docs/specs/README.md`

---

## PLAN-001 — [type:research] Comprovar fontes HTML elegíveis e selecionar o piloto ([#2](https://github.com/devmichaelcosta/dotnet-price-analizer/issues/2))

**Milestone:** Fase 0  
**Prioridade:** P0  
**Labels:** `type:research`, `priority:P0`, `area:discovery`

### Objetivo

Identificar no mínimo três lojas cujos termos públicos aplicáveis permitam expressamente o acesso automatizado a páginas HTML públicas para comparação de preços.

### Escopo

- Revisar candidatos com formatos e categorias variados, incluindo os já registrados no relatório de pesquisa.
- Guardar URL e data dos termos, política de coleta e `robots.txt`; não contatar lojas nem solicitar autorização ou esclarecimento.
- Não usar páginas que exijam login, não automatizar API/feed, nem contornar CAPTCHA, bloqueio ou limite técnico.

### Situação atual

A revisão manual de oito candidatos não comprovou três fontes elegíveis. Carrefour e Fast Shop têm proibições explícitas nos termos consultados; para os demais, não foi encontrada permissão expressa nos termos. Em 12/10/2026, canais de atendimento chegaram a ser mapeados, mas a decisão atual é não contatar lojas; esses canais e o modelo de solicitação estão arquivados e não devem ser usados. O [relatório de fontes](../research/validacao-fontes-fase-0.md) registra a decisão e as evidências. Este issue permanece aberto até identificar fontes cujos termos públicos autorizem expressamente a coleta ou registrar inviabilidade e revisar o escopo com o usuário.

### Critérios de conclusão

- [ ] Existe uma ficha de evidências para cada candidato, classificando-o como elegível, desabilitado por falta de permissão expressa ou descartado por proibição.
- [ ] Para cada elegível, os termos públicos aplicáveis permitem expressamente a coleta automatizada e a retenção pretendida.
- [ ] A regra `robots.txt`, limites técnicos e retenção da fonte foram registrados separadamente da evidência de autorização.
- [ ] Foram selecionados pelo menos três domínios elegíveis; se isso não for possível, a limitação e opções de revisão do escopo foram documentadas.
- [ ] `docs/research/validacao-fontes-fase-0.md` e `docs/roadmap.md` refletem os resultados.

### Dependências

- Part of: PLAN-000

### Referências

- `docs/research/validacao-fontes-fase-0.md`
- `AGENTS.md` — regras de coleta

---

## PLAN-002 — [type:research] Analisar manualmente páginas das fontes elegíveis ([#3](https://github.com/devmichaelcosta/dotnet-price-analizer/issues/3))

**Milestone:** Fase 0  
**Prioridade:** P0  
**Labels:** `type:research`, `priority:P0`, `area:discovery`

### Objetivo

Avaliar como descobrir produtos e extrair preço, moeda, disponibilidade, identificadores, variante e vendedor nas fontes autorizadas, sem escrever crawler ou parser.

### Critérios de conclusão

- [ ] Para cada uma das fontes piloto, foram registradas páginas de busca/categoria e exemplos de páginas de produto, com URL e data de consulta manual.
- [ ] Campos visíveis e estruturados (`schema.org/Product`/`Offer`, quando presentes) foram inventariados.
- [ ] Foram documentados preço anunciado, preço promocional/PIX/parcelado, variante, vendedor e disponibilidade, incluindo ambiguidades.
- [ ] Foi registrada a estratégia de descoberta por HTML público e quais exceções parecem exigir adaptador de domínio.
- [ ] Nenhuma automação, crawler ou parser foi implementado nesta tarefa.
- [ ] O relatório de fontes foi atualizado.

### Dependências

- Depends on: PLAN-001
- Part of: PLAN-000

### Referências

- `docs/research/validacao-fontes-fase-0.md`
- `docs/domain/context.md`

---

## PLAN-003 — [type:decision] Fechar regras de produto, correspondência e observações ([#4](https://github.com/devmichaelcosta/dotnet-price-analizer/issues/4))

**Milestone:** Fase 0  
**Prioridade:** P0  
**Labels:** `type:decision`, `priority:P0`, `area:domain`

### Objetivo

Definir os comportamentos de domínio que determinam se uma oferta pode entrar no acompanhamento e como o histórico registra leituras.

### Decisões a documentar

- Regras para EAN/GTIN inválido, ausente, duplicado ou conflitante.
- Como identificar variantes, kits, condição do produto e vendedor; quando exigir confirmação da pessoa.
- **Decisão aprovada pelo usuário:** para a mesma oferta, criar observação somente quando preço ou disponibilidade mudarem; leituras idênticas não criam duplicatas. Regra registrada como `DR-016` em `docs/domain/context.md`.
- Como tratar preço PIX, parcelamento, preço promocional, faixas de preço e indisponibilidade.
- O que acontece ao remover um acompanhamento e os dados associados, inclusive falhas de exclusão por limites da fonte.

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
- MFA: obrigatório ou opcional; recuperação de segundo fator.
- Política de senha compatível com Identity, lockout, sessão/cookie e encerramento de sessão.
- Exclusão/desativação de conta e dos acompanhamentos/históricos privados.
- Política padrão dos endpoints locais, proteção antifalsificação e autorização por proprietário; acesso limitado à máquina hospedeira por loopback/localhost.

### Critérios de conclusão

- [ ] O administrador provisiona contas; não há cadastro público, e os fluxos de login, gestão, redefinição e desativação estão descritos.
- [ ] O comando local cria o primeiro administrador apenas quando o bootstrap ainda não foi concluído, solicita senha sem valor padrão e não expõe a senha em argumentos, logs ou arquivos versionados.
- [ ] O comando local de recuperação permite redefinir a credencial administrativa no host, sem depender de e-mail, e protege a senha contra exposição em argumentos, logs ou arquivos versionados.
- [ ] Pelo app, o administrador define uma senha temporária e a entrega diretamente ao usuário; no próximo login, esse usuário deve definir uma nova senha antes de continuar, e a senha temporária não é exposta nem mantida em texto puro.
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

Produzir um desenho revisável do monólito modular, módulos, dependências, persistência e comunicação entre Blazor, Web API interna e Worker.

### Critérios de conclusão

- [ ] Fronteiras e responsabilidades dos módulos foram definidas.
- [ ] Diagramas mostram caminho principal de consulta, descoberta, confirmação, observação, histórico e autorização.
- [ ] Modelo conceitual preserva Produto, Página, Oferta, Acompanhamento e Observação como conceitos distintos.
- [ ] Ownership/isolamento por usuário local, estados de job, retentativas e idempotência estão representados.
- [ ] A topologia executa Server, Worker e PostgreSQL localmente; host/API ficam restritos a loopback/localhost e não aceitam acesso pela rede local.
- [ ] A fatia vertical mantém juntos os arquivos específicos de cada use case; dependências respeitam as decisões Clean Architecture/DDD.
- [ ] Contratos e modelo foram atualizados após as decisões de domínio/fontes.

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
- [ ] A convenção está definida para testes unitários de domínio, testes de use case e testes de integração.
- [ ] A matriz liga `AC-...`/`DR-...` a classes de domínio, use cases e testes esperados.
- [ ] A localização dos testes espelha módulo/use case/classe e permanece em projetos separados.
- [ ] Gates de revisão/CI estão definidos sem exigir testes para critérios fora do escopo do incremento.
- [ ] `docs/specs/README.md` e `docs/specs/_template.md` foram atualizados.

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

- [ ] O inventário de use cases foi reduzido e aprovado após fechar domínio, fontes e arquitetura.
- [ ] Cada spec define objetivo, atores, autorização, regras, validações, cenários de sucesso/alternativos/falha e dados observáveis.
- [ ] Cada critério tem ID estável `AC-...`, critérios usam Given/When/Then quando adequado, e apontam para `DR-...` relevantes.
- [ ] A matriz de rastreabilidade identifica classes de domínio, use case e testes derivados.
- [ ] Cada artefato tem status Rascunho/Em revisão/Aprovado; nenhuma spec em rascunho autoriza implementação.
- [ ] As specs não deixam decisão crítica implícita; itens adiados estão explicitamente fora do MVP.

### Dependências

- Depends on: PLAN-003, PLAN-004, PLAN-005 e PLAN-006
- Part of: PLAN-000

### Referências

- `docs/specs/README.md`
- `docs/specs/_template.md`
- `docs/domain/context.md`

---

## PLAN-008 — [type:task] Estimar carga, frequência, armazenamento e custo ([#9](https://github.com/devmichaelcosta/dotnet-price-analizer/issues/9))

**Milestone:** Fase 0  
**Prioridade:** P1  
**Labels:** `type:task`, `priority:P1`, `area:architecture`

### Objetivo

Estimar se um intervalo-alvo de seis horas é viável para as fontes elegíveis e volume inicial pretendido.

### Entradas necessárias

- Número de usuários locais previstos.
- Acompanhamentos médios e máximos por usuário local.
- Número de ofertas confirmadas por acompanhamento e páginas a verificar.
- Limites por domínio comprovados no PLAN-001.

### Critérios de conclusão

- [ ] Foram calculadas requisições e armazenamento de observações por dia/mês em cenários baixo, esperado e alto.
- [ ] Frequência e concorrência são compatíveis com limites de cada fonte e custo estimado.
- [ ] Se seis horas não couber, nova frequência e impacto de produto são propostos para decisão.
- [ ] Premissas e fórmulas estão registradas e ligadas ao roadmap.

### Dependências

- Depends on: PLAN-001 e PLAN-002
- Part of: PLAN-000

### Referências

- `docs/roadmap.md`
- `docs/research/validacao-fontes-fase-0.md`

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
