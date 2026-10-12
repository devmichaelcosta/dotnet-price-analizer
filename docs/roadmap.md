# Roadmap — Analisador de preços

**Estado:** rascunho para aprovação; nenhuma aplicação foi implementada.
**Direção escolhida:** MVP híbrido, com núcleo manual primeiro e automação condicionada. A escolha da direção não aprova as specs nem autoriza implementação.
**Referências:** [`docs/domain/context.md`](domain/context.md), [`docs/specs/README.md`](specs/README.md), [`docs/decisions/registro-fase-0.md`](decisions/registro-fase-0.md) e [`ADR-003`](decisions/ADR-003-mvp-hibrido-e-fontes-html.md).

## Objetivo do produto

Uma aplicação local para múltiplas contas provisionadas pelo administrador. Cada pessoa cadastra um produto, registra ofertas de lojas e informa os preços observados. O sistema compara ofertas compatíveis e mantém histórico para mostrar se o preço mudou.

O MVP não pesquisa a Internet. Uma etapa futura poderá automatizar a leitura de páginas HTML públicas de lojas somente quando os termos do domínio permitirem expressamente coleta, apresentação dos campos e retenção. A aplicação não contata lojas.

## Premissas e decisões vigentes

- **Uso:** local, restrito à máquina hospedeira; não aceitar conexões pela rede local ou Internet. Sem cadastro público ou hospedagem pública.
- **Contas:** ASP.NET Core Identity no PostgreSQL, contas provisionadas pelo administrador local e sessão de navegador por cookie protegido. MFA obrigatória para administradores e opcional para os demais, por TOTP e códigos de recuperação de uso único. Recuperações de MFA e credencial administrativa seguem comandos locais conforme as specs de Identity.
- **Stack:** .NET / ASP.NET Core Web API, Blazor Web App Interactive Server, MudBlazor, PostgreSQL, EF Core e Npgsql. A API é privada e usada pelo próprio sistema; JWT/OIDC/OpenIddict não são requisito do MVP.
- **Arquitetura:** monólito modular com slices verticais; lógica de negócio, autorização, cálculos e persistência no backend. Cada use case mantém seus arquivos, docs e testes juntos na estrutura acordada. Não criar microserviços.
- **Dependências:** priorizar bibliotecas oficiais/suportadas pela Microsoft; MudBlazor é a exceção explícita da interface. Qualquer outra dependência externa precisa de justificativa e revisão de suporte, manutenção, segurança e licença.
- **MVP manual:** entrada de produto por texto e/ou EAN/GTIN, cadastro manual de ofertas e preços, comparação sem frete, análise temporal e dados privados por conta. Detalhes dos campos e do horário da observação ainda estão em decisão F0-16.
- **Proveniência:** observações manuais precisam ficar marcadas como informadas por usuário e não podem ser apresentadas como verificadas pela loja. URL informada é referência e não dispara requisição de rede.
- **Histórico:** manter observações enquanto o acompanhamento existir e apagá-las junto com ele. Para leituras automáticas futuras, `DR-016` suprime observações quando preço e disponibilidade não mudam; essa regra não define o que fazer quando o usuário salva manualmente uma nova conferência com o mesmo valor. A política manual permanece em F0-16.
- **Fontes automáticas futuras:** somente páginas HTML públicas do próprio domínio, com permissão expressa nos termos aplicáveis. Não usar APIs, feeds, agregadores ou integrações de terceiros como fonte. Não contatar lojas; `robots.txt` sozinho não autoriza coleta.
- **Automação:** atualização desejada a cada seis horas, condicionada aos termos e limites do domínio; não faz parte do MVP manual. Nenhum domínio foi aprovado.
- **Notificações:** fora do escopo local atual.

## Fase 0 — Fechar planejamento e aprovar o MVP

**Objetivo:** revisar contexto e arquitetura e concluir specs que descrevem o primeiro incremento manual.

**Entregas:**

- Contexto do domínio alinhado à escolha C e à diretriz vigente de fontes HTML somente em etapa futura.
- Decisões F0 restantes resolvidas ou explicitamente adiadas, em especial campos de produto/oferta, proveniência, horário da observação, comparabilidade e períodos de análise.
- Arquitetura inicial, diagramas de módulos/chamadas, modelo conceitual e fluxos atualizados para o núcleo manual; a rota Blazor/API aguarda F0-18. Worker e fila ficam condicionais à automação futura.
- Inventário separado entre specs do MVP manual e specs condicionais de descoberta/coleta.
- Specs do MVP revisadas, com critérios `AC-...`, regras `DR-...`, classes de domínio e matriz de testes rastreáveis.
- Roadmap e conjunto de specs aprovados explicitamente pelo usuário antes de criar aplicação, scaffolding ou código de produção.
- Backlog profissional da Fase 0 mantido localmente e sincronizado com GitHub quando a integração permitir escrita.

**Critério de saída:** regras abertas foram decididas ou isoladas como fora do MVP; diagrama, contratos e modelos são revisáveis; todas as specs do MVP manual têm critérios verificáveis e testes planejados; usuário aprova explicitamente o pacote. A ausência de fontes automatizadas não bloqueia o núcleo manual. Nenhuma fonte é habilitada sem seu gate. Nenhum código de aplicação é criado antes da aprovação.

## Fase 1 — Fundação local, identidade e autorização

**Objetivo:** estabelecer a base segura do monólito para uso exclusivamente local.

**Escopo:** solução .NET modular; host ASP.NET Core para Blazor Interactive Server e API privada; PostgreSQL/EF Core; MudBlazor; ASP.NET Core Identity; bootstrap local de uso único para primeiro administrador; comandos locais de recuperação; provisionamento administrativo; senha temporária com troca obrigatória no próximo login; TOTP/recovery codes segundo a política aprovada; lockout, cookies protegidos, antifalsificação para mutações por cookie, propriedade por conta e chaves Data Protection persistidas/protegidas. Escutar somente em loopback. Não incluir Worker ou conectores enquanto não houver job automatizado aprovado.

**Critério de saída:** administrador é criado sem senha padrão e sem segredo exposto; usuários são provisionados sem cadastro público; MFA e recuperação atendem suas specs; cada operação protegida valida identidade e propriedade no backend; aplicação não aceita acesso remoto; cada caso de uso e classe de domínio da fase tem testes rastreáveis aprovados; UI não contém lógica de negócio nem acesso direto ao banco.

## Fase 2 — Catálogo, acompanhamento e ofertas manuais

**Objetivo:** permitir cadastrar o item e organizar referências de preços de diferentes lojas.

**Escopo:** cadastrar/acompanhar produto por texto e/ou EAN/GTIN; associar o acompanhamento à conta proprietária; cadastrar oferta manual para uma loja; armazenar campos acordados para identificar a oferta, sua variante/condição, moeda, disponibilidade e preço. Qualquer URL é apenas referência informada e não é acessada pelo app. Listar e remover acompanhamentos com todos os dados privados associados.

**Critério de saída:** usuário autenticado consegue criar, listar e remover seus próprios acompanhamentos e ofertas; dados de outra conta permanecem inacessíveis; o sistema distingue ofertas e variantes incompatíveis; remoção apaga ofertas, observações e demais dados privados associados; todas as classes de domínio/use cases têm testes rastreáveis aprovados.

## Fase 3 — Observações, comparação e histórico

**Objetivo:** entregar a análise manual de variação e comparação entre lojas.

**Escopo:** registrar observação manual inicial e atualizações; registrar autoria/proveniência e instante conforme decisão aprovada; preservar observações imutáveis; aplicar a política ainda pendente para uma conferência manual explícita que repita preço e disponibilidade; consultar série por período; calcular diferença absoluta e percentual entre datas válidas; comparar os últimos preços informados para ofertas do mesmo produto quando moeda, variante, condição e demais critérios aprovados forem compatíveis. Exibir a data de cada valor e não afirmar que uma observação manual ainda está vigente na loja. Comparação considera apenas preço do item, sem frete.

**Critério de saída:** UI diferencia registro manual de qualquer origem futura; mostra preço, moeda, disponibilidade, origem e data; apresenta ofertas comparáveis sem misturar variantes/condições/moedas; indica aumentos, reduções e estabilidade sem inventar pontos; todo critério tem testes aprovados.

## Fase 4 — Descoberta HTML condicionada por domínio

**Condição para iniciar:** usuário aprova uma spec incremental para um domínio específico e o gate documental comprova expressamente que coleta automatizada, campos, apresentação e retenção do MVP são permitidos.

**Escopo condicionado:** decidir busca e extração para páginas públicas do domínio; manter por padrão somente campos normalizados autorizados, sem armazenar HTML bruto; respeitar intervalo, concorrência, timeouts e bloqueios; isolar falhas por domínio; identificar claramente observações de origem automatizada. Nenhuma API, feed, agregador ou serviço de terceiros pode ser usado como fallback. Não contornar autenticação, CAPTCHA ou controles de acesso.

**Infraestrutura:** somente quando a primeira spec de automação aprovada exigir processamento assíncrono, incluir Worker .NET separado e fila persistida/idempotente. A meta de seis horas será validada contra as regras daquele domínio; não é uma cadência universal.

**Critério de saída:** coleta só ocorre no domínio aprovado; consulta e armazenamento seguem o gate; falhas não afetam a entrada manual; reinício não perde jobs persistidos, se houver Worker; cada comportamento incremental tem spec e testes aprovados antes da implementação.

## Fase 5 — Novos domínios e operação

**Objetivo:** ampliar cobertura automática sem ampliar implicitamente o tipo de fonte permitido.

**Escopo:** avaliar novos domínios HTML individualmente; revisar mudanças de termos, limites, campos e retenção; observabilidade, cópias de segurança e processo de exclusão conforme capacidade real. Um domínio sem permissão expressa permanece desabilitado. Alertas/notificações continuam fora até decisão separada.

## Backlog macro

| Ordem | Resultado | Dependência |
|---|---|---|
| P0 | Fechar decisões restantes e aprovar roadmap/specs | Direção C escolhida |
| P1 | Fundação local, Identity, autorização e MudBlazor | Gate da Fase 0 |
| P1 | Cadastro de produto, acompanhamento e ofertas manuais | Fundação |
| P1 | Observações, comparação entre lojas e histórico | Ofertas manuais |
| Condicional | Conector HTML por domínio e Worker/fila quando necessários | Evidência expressa + spec incremental aprovada |
| Futuro | Novos domínios, operação e alertas mediante decisão | Conector elegível e requisitos aprovados |

## Riscos e controles

- **Coleta sem permissão:** não há automação no MVP. Para cada domínio futuro, exigir evidência expressa nos termos e avaliar `robots.txt`, limites, campos e retenção; termos silenciosos ou ambíguos mantêm o domínio desabilitado.
- **Dados manuais imprecisos:** identificar autoria e proveniência e não rotular como confirmação da loja; fechar os campos e horário de observação em spec.
- **Exclusão e restauração local:** ao remover um acompanhamento, apagar os dados privados vinculados. Definir antes da implementação se backups/restauração compõem o MVP e evitar que uma restauração reintroduza acompanhamentos já removidos.
- **Comparação incorreta:** separar produto, oferta e observação; não misturar condição, variante ou moeda incompatível.
- **Privacidade entre contas:** identidade deriva do principal autenticado; filtros e autorização no backend em toda operação.
- **Exposição local:** host e API restritos a loopback; sem cadastro público, sem cliente externo e sem implantação de rede.
- **Manutenção excessiva:** introduzir Worker e infraestrutura de scraping só quando uma fonte aprovada e uma spec concreta justificarem esse custo.

## Aberturas para concluir a Fase 0

As propostas e seus impactos estão em [`docs/decisions/registro-fase-0.md`](decisions/registro-fase-0.md). A direção C já foi escolhida; permanecem pendentes os detalhes do fluxo manual, políticas de segurança ainda não aprovadas, decisão F0-18 sobre chamada da UI/API, estratégia final de testes e revisão integral das specs. Até a aprovação explícita do roadmap e do conjunto MVP, o trabalho continua em planejamento.
