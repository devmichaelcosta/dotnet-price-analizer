# Backlog de planejamento

**Estado:** rascunho para revisão; cobre planejamento e Fase 0, sem tarefas de implementação.  
**Fonte de verdade:** roadmap, contexto do domínio e specs aprovadas.  
**Destino planejado:** GitHub Issues e GitHub Projects, após conectar este workspace ao repositório correto.

## Organização no GitHub

- Um issue `type:epic` representa cada fase/resultado maior e contém as tarefas filhas.
- Um issue `type:task`, `type:research`, `type:decision` ou `type:spec` deve ser pequeno o bastante para produzir um artefato revisável e fechar com critérios verificáveis.
- Use os IDs estáveis `PLAN-000`, `PLAN-001` etc. no título e mantenha o número/URL real do GitHub no índice depois da publicação.
- Relacione as tarefas ao épico com subtarefas do GitHub quando disponíveis; caso contrário, use checklist com links `#<issue>` no corpo do épico.
- Não crie tarefa técnica de implementação antes de sua spec estar aprovada. Para increments futuros, crie tarefas dentro da fatia/use case correspondente e vincule aos IDs `AC-...` e `DR-...`.
- Não use estimate em pontos sem conhecer a equipe/capacidade. Depois, se necessário, adotar tamanhos XS/S/M/L com escala acordada.

## Marcos e ordem de trabalho

1. **Fase 0 — Planejamento e validação de fontes:** resolver elegibilidade de fontes, regras de domínio, segurança, arquitetura e teste; escrever e aprovar as specs do MVP.
2. **MVP — Fundação e identidade:** milestone criado quando as specs forem aprovadas e a implementação autorizada.
3. **MVP — Descoberta e acompanhamento:** dependente da validação das fontes e da fundação.
4. **MVP — Histórico e análise:** dependente de acompanhamento e observações.
5. **Pós-MVP — Monitoramento e alertas:** só detalhar quando o escopo dessas capacidades for aprovado.

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

O diretório de trabalho não contém `.git` nem remote configurado. A integração GitHub conectada também não retornou repositórios acessíveis. Portanto os issues abaixo ainda são rascunhos locais; não foram publicados em nenhum repositório. Para publicar, é necessário conectar/indicar o repositório do projeto.
