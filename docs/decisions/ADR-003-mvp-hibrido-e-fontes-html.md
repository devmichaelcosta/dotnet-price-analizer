# ADR-003 — Núcleo manual e automação HTML condicionada

**Estado:** direção escolhida pelo usuário; detalhes do MVP pendentes de aprovação<br>
**Data:** 2026-10-12<br>
**Decisor:** Usuário do projeto

## Contexto

A ideia do produto é comparar preços entre lojas e observar sua evolução no tempo. A pesquisa documental não aprovou fonte automatizada para o uso local e retenção pretendidos. O usuário escolheu a opção C: iniciar pelo núcleo manual e deixar conectores automáticos desabilitados até cada fonte passar por seu gate.

A diretriz de projeto mais recente restringe a coleta futura a páginas HTML públicas cuja coleta automatizada seja expressamente permitida pelos termos aplicáveis. APIs externas, feeds, agregadores e integrações de terceiros não serão usados como fontes de dados. O aplicativo não contata lojas.

## Decisão

1. O primeiro MVP concentra-se em cadastro local de produto e ofertas, entrada manual de preço/disponibilidade, comparação de ofertas compatíveis entre lojas e análise da variação no tempo.
2. Registros manuais devem ser identificáveis como informados por um usuário. O sistema não os apresenta como dados verificados pela loja.
3. No fluxo manual, uma URL informada pelo usuário é apenas referência; não dispara consulta de rede.
4. Busca, extração e atualização automáticas não fazem parte do MVP manual e começam desabilitadas.
5. Um conector HTML só pode ser desenvolvido/habilitado em incremento futuro, após análise documental por domínio que comprove permissão expressa para coleta, extração dos campos, exibição e retenção pretendidos. Termos silenciosos ou ambíguos mantêm o domínio desabilitado.
6. Nenhuma API, feed, agregador ou integração de terceiros é fonte aprovada. Alterar essa restrição exige decisão explícita do usuário e atualização deste ADR.
7. A aplicação permanece local, com contas provisionadas pelo administrador e dados privados por conta. O gate de aprovação do roadmap e das specs continua obrigatório antes da implementação.

## Alternativas consideradas

- **Automação como requisito do primeiro corte:** não escolhida porque nenhuma fonte foi aprovada na pesquisa atual.
- **Somente análise manual sem evolução de automação prevista:** não escolhida; a opção C deixa espaço para conectores HTML aprovados individualmente.
- **APIs oficiais de lojas como fonte:** descartadas pela diretriz de projeto vigente, independentemente de disponibilidade técnica.
- **APIs de plataforma, feeds e agregadores:** fora do escopo; não são fontes próprias da loja sob a diretriz vigente.

## Consequências

- Roadmap e inventário devem separar o MVP manual dos conectores condicionais.
- O MVP manual não precisa de fila de coleta nem de processo Worker. A necessidade de Worker será reavaliada quando um conector HTML elegível for aprovado.
- As specs de captura manual precisam definir autoria, proveniência, horário, campos da oferta, comparabilidade, validação e falhas antes de serem aprovadas.
- A cobertura manual depende dos dados informados pelo usuário; a interface deve distinguir observações manuais de observações que, em uma fase futura, tenham sido coletadas de uma fonte aprovada.
- As specs de descoberta/extração e operações de conectores continuam em rascunho condicional e não autorizam implementação.

## Revisão

Revisar apenas se a pesquisa documental encontrar uma fonte HTML elegível, se os termos mudarem, ou se o usuário alterar explicitamente o escopo de fontes. Toda fonte exige aprovação individual, sem contato com a loja.
