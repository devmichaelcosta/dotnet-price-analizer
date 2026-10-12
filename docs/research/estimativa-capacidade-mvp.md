# Estimativa de capacidade — MVP manual

**Estado:** estimativa preliminar baseada em cenários; números de uso não foram confirmados pelo usuário.<br>
**Escopo:** banco PostgreSQL local para dados informados manualmente. Não inclui crawler, consultas externas, Worker, fila ou alertas.

## Método

O volume anual de observações é estimado por:

`contas × acompanhamentos por conta × ofertas por acompanhamento × observações por oferta por período`.

Para armazenamento, adota-se provisoriamente **0,5–1,5 KiB por observação**, incluindo uma margem para tupla e índices. É uma hipótese de planejamento, não medição do PostgreSQL do projeto. Não inclui WAL, cópias de segurança, índices de texto, arquivos anexos ou espaço temporário. A implementação deve medir o schema real antes de fixar requisitos de disco.

## Cenários

| Cenário | Contas | Acompanhamentos/conta | Ofertas/acompanhamento | Ritmo manual assumido | Observações/dia (média) | Observações/mês (aprox.) | Espaço/mês | Espaço/ano |
|---|---:|---:|---:|---|---:|---:|---:|---:|
| Baixo | 1 | 50 | 2 | 1 por oferta/semana | 14 | 433 | 0,21–0,63 MiB | 2,5–7,6 MiB |
| Esperado | 3 | 100 | 3 | 1 por oferta/semana | 128 | 3.900 | 1,9–5,7 MiB | 22,9–68,6 MiB |
| Estresse | 5 | 200 | 4 | 1 por oferta/dia | 4.000 | 121.667 | 59–178 MiB | 0,70–2,09 GiB |

O espaço é aproximadamente linear: sem exclusões e sem compressão, três anos representam cerca de 7,6–22,9 MiB no cenário baixo, 68,6–205,7 MiB no esperado e 2,1–6,3 GiB no cenário de estresse. Produtos e ofertas são cadastros pontuais; neste modelo, as observações dominam o crescimento.

O ritmo manual é apenas uma hipótese. Se uma nova conferência manual com o mesmo preço não gerar observação, o volume real será menor; essa política está pendente em F0-16. O histórico não tem expurgo por idade: ele permanece enquanto o acompanhamento existir e é removido junto com ele, conforme a decisão existente.

## Implicações para o MVP

- Consultas externas por dia/mês: **zero** no MVP manual.
- As médias de observações são registros locais estimados, não requisições HTTP. Meses foram aproximados como 30,4 dias.
- Atualizações agendadas, filas e custo de coleta não se aplicam; reavaliar quando existir uma fonte HTML aprovada e uma spec incremental.
- Os cenários baixo e esperado cabem confortavelmente em uma instalação PostgreSQL local sob as hipóteses acima. O cenário de estresse exige monitorar o espaço em disco e incluir a retenção de cópias de segurança no cálculo operacional.
- Não definir limite de acompanhamentos a partir desta estimativa. Paginação e desempenho das consultas devem ser medidos contra o schema e volume reais durante a implementação aprovada.

## Incertezas e validação futura

- Quantidade de contas locais e acompanhamentos ativos esperados.
- Ritmo de registros manuais e decisão sobre capturas repetidas sem alteração (F0-16).
- Tamanho real de cada linha/índice e crescimento do PostgreSQL, a medir após definir o schema.
- Espaço e política de retenção para cópias de segurança.
- Cadência e concorrência de qualquer fonte automatizada, sujeitas ao gate por domínio; seis horas não é uma garantia universal.

## Rastreabilidade

- Issue local: `PLAN-008` em [`docs/backlog/fase-0-issues.md`](../backlog/fase-0-issues.md).
- Premissas de produto: [`docs/roadmap.md`](../roadmap.md) e [`docs/domain/context.md`](../domain/context.md).
