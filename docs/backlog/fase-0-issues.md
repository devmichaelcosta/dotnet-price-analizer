# Rascunhos de GitHub Issues — Fase 0

**Estado:** conteúdo pronto para revisão/publicação; ainda não criado no GitHub.  
**Épico:** `PLAN-000` — Concluir planejamento e aprovar as specs do MVP.

---

## PLAN-000 — [type:epic] Concluir planejamento e aprovar as specs do MVP

**Milestone:** Fase 0 — Planejamento e validação de fontes  
**Prioridade:** P0  
**Labels:** `type:epic`, `priority:P0`, `area:product`

### Objetivo

Concluir a definição do MVP antes de criar aplicação ou código de produção.

### Contexto

O roadmap exige análise manual de páginas, validação de fontes, decisões de domínio, arquitetura e segurança, estratégia de testes e especificações aprovadas. A coleta usará somente HTML público cuja automação seja permitida expressamente nos termos aplicáveis ou por autorização escrita. APIs, feeds e integrações de terceiros não serão fontes de dados.

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

## PLAN-001 — [type:research] Comprovar fontes HTML elegíveis e selecionar o piloto

**Milestone:** Fase 0  
**Prioridade:** P0  
**Labels:** `type:research`, `priority:P0`, `area:discovery`

### Objetivo

Identificar no mínimo três lojas que permitam expressamente, nos termos aplicáveis, ou autorizem por escrito o acesso automatizado a páginas HTML públicas para comparação de preços.

### Escopo

- Revisar candidatos com formatos e categorias variados, incluindo os já registrados no relatório de pesquisa.
- Guardar URL e data dos termos, política de coleta, `robots.txt` e resposta da loja, quando necessária.
- Não usar páginas que exijam login, não automatizar API/feed, nem contornar CAPTCHA, bloqueio ou limite técnico.

### Situação atual

A revisão manual de oito candidatos não comprovou três fontes elegíveis. Carrefour e Fast Shop têm proibições explícitas nos termos consultados; para os demais, há falta de autorização comprovada ou incerteza sobre o escopo dos documentos. O relatório registra evidências e três caminhos para revisão de viabilidade. Há também um [modelo de solicitação de autorização escrita](../research/modelo-solicitacao-autorizacao-coleta.md), ainda não enviado. Este issue permanece aberto até obter três autorizações válidas ou revisar com o usuário os critérios de cobertura/fontes.

### Critérios de conclusão

- [ ] Existe uma ficha de evidências para cada candidato, classificando-o como elegível, aguardando autorização ou descartado.
- [ ] Para cada elegível, há permissão expressa nos termos ou autorização escrita arquivada/referenciada.
- [ ] A regra `robots.txt`, limites técnicos e retenção da fonte foram registrados separadamente da evidência de autorização.
- [ ] Foram selecionados pelo menos três domínios elegíveis; se isso não for possível, a limitação e opções de revisão do escopo foram documentadas.
- [ ] `docs/research/validacao-fontes-fase-0.md` e `docs/roadmap.md` refletem os resultados.

### Dependências

- Part of: PLAN-000

### Referências

- `docs/research/validacao-fontes-fase-0.md`
- `AGENTS.md` — regras de coleta

---

## PLAN-002 — [type:research] Analisar manualmente páginas das fontes elegíveis

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

## PLAN-003 — [type:decision] Fechar regras de produto, correspondência e observações

**Milestone:** Fase 0  
**Prioridade:** P0  
**Labels:** `type:decision`, `priority:P0`, `area:domain`

### Objetivo

Definir os comportamentos de domínio que determinam se uma oferta pode entrar no acompanhamento e como o histórico registra leituras.

### Decisões a documentar

- Regras para EAN/GTIN inválido, ausente, duplicado ou conflitante.
- Como identificar variantes, kits, condição do produto e vendedor; quando exigir confirmação da pessoa.
- Se toda leitura válida gera observação, inclusive quando preço e disponibilidade não mudam.
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

## PLAN-004 — [type:decision] Definir política de autenticação, conta e autorização

**Milestone:** Fase 0  
**Prioridade:** P0  
**Labels:** `type:decision`, `priority:P0`, `area:security`

### Objetivo

Definir os fluxos de ASP.NET Core Identity local e controles de autorização para contas e recursos privados.

### Decisões a documentar

- Cadastro por e-mail/senha, confirmação de e-mail e recuperação sem revelar se a conta existe.
- MFA: obrigatório ou opcional; recuperação de segundo fator.
- Política de senha compatível com Identity, lockout, sessão/cookie e encerramento de sessão.
- Exclusão de conta e dos acompanhamentos/históricos privados.
- Política padrão dos endpoints internos, proteção antifalsificação e autorização por proprietário.

### Critérios de conclusão

- [ ] Fluxos e estados de conta estão descritos, incluindo falhas e abuso relevantes.
- [ ] Defaults de segurança têm justificativa e usam os mecanismos suportados do ASP.NET Core Identity.
- [ ] Decisões estão registradas no contexto/ADR adequado e são referenciadas pelas specs de conta.
- [ ] Nenhum emissor JWT/OIDC foi incluído no escopo sem cliente externo aprovado.

### Dependências

- Part of: PLAN-000

### Referências

- `docs/arquitetura-inicial.md`
- `docs/roadmap.md`
- `AGENTS.md` — segurança e privacidade

---

## PLAN-005 — [type:task] Fechar arquitetura modular, contratos, dados e fluxos

**Milestone:** Fase 0  
**Prioridade:** P0  
**Labels:** `type:task`, `priority:P0`, `area:architecture`

### Objetivo

Produzir um desenho revisável do monólito modular, módulos, dependências, persistência e comunicação entre Blazor, Web API interna e Worker.

### Critérios de conclusão

- [ ] Fronteiras e responsabilidades dos módulos foram definidas.
- [ ] Diagramas mostram caminho principal de consulta, descoberta, confirmação, observação, histórico e autorização.
- [ ] Modelo conceitual preserva Produto, Página, Oferta, Acompanhamento e Observação como conceitos distintos.
- [ ] Ownership/isolamento por conta, estados de job, retentativas e idempotência estão representados.
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

## PLAN-006 — [type:decision] Definir estratégia SDD, testes e rastreabilidade

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

## PLAN-007 — [type:spec] Escrever e revisar as specs do MVP

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

## PLAN-008 — [type:task] Estimar carga, frequência, armazenamento e custo

**Milestone:** Fase 0  
**Prioridade:** P1  
**Labels:** `type:task`, `priority:P1`, `area:architecture`

### Objetivo

Estimar se um intervalo-alvo de seis horas é viável para as fontes elegíveis e volume inicial pretendido.

### Entradas necessárias

- Usuários previstos no início e horizonte de crescimento.
- Acompanhamentos médios e máximos por conta.
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

## PLAN-009 — [type:decision] Revisar e aprovar o gate da Fase 0

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
