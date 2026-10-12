# Backlog de planejamento

**Estado:** PLAN-000 a PLAN-009 existem no GitHub. O backlog local foi revisado após o usuário escolher C; as revisões e PLAN-010 a PLAN-012 ainda não foram sincronizadas. A integração havia retornado `403 Resource not accessible by integration` para escrita. Não há tarefas de implementação.
**Situação atual:** núcleo manual escolhido; nenhum domínio HTML foi aprovado. As decisões manuais restantes estão no [registro da Fase 0](../decisions/registro-fase-0.md), e a direção consta no [documento de revisão do MVP](../research/revisao-escopo-mvp.md). Consulte o [relatório de fontes](../research/validacao-fontes-fase-0.md) como pesquisa histórica.
**Fonte de verdade:** roadmap, contexto do domínio e specs aprovadas.<br>
**Repositório:** [devmichaelcosta/dotnet-price-analizer](https://github.com/devmichaelcosta/dotnet-price-analizer), branch `main`.

## Organização no GitHub

- Um issue `type:epic` representa cada fase/resultado maior e contém as tarefas filhas.
- Um issue `type:task`, `type:research`, `type:decision` ou `type:spec` deve ser pequeno o bastante para produzir um artefato revisável e fechar com critérios verificáveis.
- Use os IDs estáveis `PLAN-000`, `PLAN-001` etc. no título e mantenha o número/URL real do GitHub no índice depois da publicação.
- Relacione as tarefas ao épico com subtarefas do GitHub quando disponíveis; caso contrário, use checklist com links `#<issue>` no corpo do épico.
- Não crie tarefa técnica de implementação antes de sua spec estar aprovada. Para increments futuros, crie tarefas dentro da fatia/use case correspondente e vincule aos IDs `AC-...` e `DR-...`.
- Não use estimate em pontos sem conhecer a equipe/capacidade. Depois, se necessário, adotar tamanhos XS/S/M/L com escala acordada.

## Marcos e ordem de trabalho

1. **Fase 0 — Planejamento do MVP manual:** fechar regras de domínio, proveniência, comparação, segurança, arquitetura e testes; aprovar as specs antes de codificar.
2. **MVP — Fundação e identidade:** milestone criado quando as specs forem aprovadas e a implementação autorizada.
3. **MVP — Cadastro manual e acompanhamento:** dependente da fundação.
4. **MVP — Ofertas, histórico e análise:** depende de cadastro manual e observações.
5. **Futuro condicional — Coleta HTML:** exige gate e spec aprovada por domínio; Worker/fila só quando necessário.
6. **Futuro — Alertas:** só detalhar mediante decisão explícita.

Por enquanto, publicar apenas issues da Fase 0. Os outros marcos são agrupadores do roadmap, não autorização para começar implementação.

## Taxonomia inicial de labels

Labels sugeridas; verificar se já existem antes de criar no repositório:

- Tipo: `type:epic`, `type:task`, `type:research`, `type:decision`, `type:spec`.
- Prioridade: `priority:P0`, `priority:P1`, `priority:P2`, `priority:P3`.
- Área: `area:domain`, `area:discovery`, `area:security`, `area:architecture`, `area:testing`, `area:product`.
- Bloqueio: `status:blocked`.

No GitHub Projects, usar os campos `Status` (Backlog, Ready, In Progress, In Review, Done, Blocked), `Priority` (P0–P3), `Milestone` e `Area`. O status é controlado no Project; não duplicar todos os estados com labels.

## Padrão de issue

Título:

`[PLAN-###] Verbo + resultado verificável`

Corpo:

```markdown
## Objetivo
Resultado que este issue deve produzir.

## Contexto
Por que é necessário e links para roadmap, contexto ou spec.

## Escopo
- Inclui ...
- Não inclui ...

## Critérios de conclusão
- [ ] Artefato/evidência revisável foi produzido.
- [ ] Critérios específicos deste issue foram satisfeitos.
- [ ] Documentos impactados foram atualizados.
- [ ] Dependências e decisões abertas foram registradas.

## Dependências
- Depends on: #...
- Part of: #...

## Referências
- `docs/...`
```

## Pronto para iniciar / concluído

Um item fica **Ready** quando tem objetivo claro, resultado revisável, critérios de conclusão, dependências e fonte de requisito identificados. Um issue de implementação também exige spec aprovada.

Um item fica **Done** quando os critérios foram conferidos, a evidência/artefato está vinculada, docs relacionadas foram atualizadas e o resultado não contradiz decisões ou specs aprovadas. Para implementação, os testes rastreados pela spec devem estar aprovados.

## Estado do GitHub

Os documentos de planejamento iniciais foram publicados na branch `main` do repositório indicado pelo usuário. O espelho local contém atualizações posteriores da pesquisa e da revisão de escopo, ainda não sincronizadas no GitHub.

O marco [Fase 0 — Planejamento e validação de fontes](https://github.com/devmichaelcosta/dotnet-price-analizer/milestone/1) e as labels da taxonomia foram criados. Os dez issues originais foram publicados, organizados pelo marco e vinculados ao épico. Os títulos/corpos listados abaixo refletem a versão remota original; a revisão para C está local até a integração permitir atualização:

| ID | Issue |
|---|---|
| PLAN-000 | [Concluir planejamento e aprovar as specs do MVP](https://github.com/devmichaelcosta/dotnet-price-analizer/issues/1) |
| PLAN-001 | [Comprovar APIs oficiais elegíveis e selecionar o piloto](https://github.com/devmichaelcosta/dotnet-price-analizer/issues/2) — conteúdo remoto desatualizado |
| PLAN-002 | [Analisar documentação e respostas de APIs elegíveis](https://github.com/devmichaelcosta/dotnet-price-analizer/issues/3) — pesquisa histórica, conteúdo remoto desatualizado |
| PLAN-003 | [Fechar regras de produto, correspondência e observações](https://github.com/devmichaelcosta/dotnet-price-analizer/issues/4) |
| PLAN-004 | [Definir política de autenticação, conta e autorização](https://github.com/devmichaelcosta/dotnet-price-analizer/issues/5) |
| PLAN-005 | [Fechar arquitetura modular, contratos, dados e fluxos](https://github.com/devmichaelcosta/dotnet-price-analizer/issues/6) |
| PLAN-006 | [Definir estratégia SDD, testes e rastreabilidade](https://github.com/devmichaelcosta/dotnet-price-analizer/issues/7) |
| PLAN-007 | [Escrever e revisar as specs do MVP](https://github.com/devmichaelcosta/dotnet-price-analizer/issues/8) |
| PLAN-008 | [Estimar carga, frequência, armazenamento e custo](https://github.com/devmichaelcosta/dotnet-price-analizer/issues/9) |
| PLAN-009 | [Revisar e aprovar o gate da Fase 0](https://github.com/devmichaelcosta/dotnet-price-analizer/issues/10) |
| PLAN-010 | Estratégia API-first substituída por HTML condicionado — publicação pendente |
| PLAN-011 | Registrar opção C e alinhar artefatos — publicação pendente |
| PLAN-012 | Fechar regras de oferta e observação manual — publicação pendente |

Cada issue contém critérios de conclusão, labels de tipo/prioridade/área, dependências e links para os artefatos relacionados. O épico mantém o checklist das tarefas filhas. A publicação não aprova o roadmap nem autoriza implementação; esse gate continua dependendo de decisão explícita do usuário.
