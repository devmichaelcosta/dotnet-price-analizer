# Especificação — RecordPriceObservation

**Estado:** Rascunho — 2026-10-12<br>
**Escopo:** MVP manual para atualizações; a observação inicial é criada em `AddManualOffer`. Reuso por automação exige spec futura.<br>
**Módulo:** PriceTracking<br>
**Responsável pelo domínio:** Produto

## Problema e objetivo

Registrar uma nova observação manual de preço/disponibilidade para uma oferta, preservando a linha do tempo e sua proveniência.

## Atores e autorização

- Ator: usuário local autenticado e proprietário da oferta/acompanhamento.
- O backend deriva a identidade do principal e valida propriedade em cada chamada.
- No MVP, o Worker não chama este caso de uso.

## Contexto e regras de domínio

- `DR-002`/`DR-004`: observações preservam histórico, proveniência e instante UTC.
- `DR-003`: dinheiro usa decimal e moeda.
- `DR-005`: indisponibilidade não é preço zero.
- `DR-011`/`DR-012`: propriedade e privacidade por conta.
- `DR-016`: aplica-se somente a polling automatizado futuro; não decide se uma nova captura manual explícita com valor idêntico cria observação.
- `DR-017`/`DR-018`: sem consulta de rede; origem manual e autoria identificadas.

## Escopo

### Incluído

- Validar e registrar uma leitura informada pelo proprietário da oferta.
- Registrar observações manuais preservando autoria e data; o comportamento de uma nova ação explícita com valor idêntico permanece pendente em F0-16.
- Preservar observações anteriores imutáveis.

### Fora de escopo

- Inferir um preço que o usuário não informou.
- Consultar URL, loja, API, feed ou serviço de terceiros.
- Registrar dados como verificados pela loja.
- Entrada inicial da oferta, coberta por `AddManualOffer`.

## Comportamento esperado

O usuário informa preço/estado conforme as regras aprovadas. O sistema associa autoria manual e horário segundo F0-16 e valida moeda/estado. Uma alteração válida de preço ou disponibilidade gera registro imutável. Se a pessoa salva novamente o mesmo valor em uma ação manual distinta, a spec ainda não define se isso cria nova observação; a regra de polling automático `DR-016` não deve ser aplicada implicitamente. Falha de validação não altera o histórico anterior.

## Cenários de aceite

### AC-RPO-001 — Registrar primeira atualização válida

**Dado** que uma oferta própria já possui uma observação inicial<br>
**Quando** o usuário informa dados válidos com preço ou disponibilidade diferente da observação atual<br>
**Então** uma observação manual é persistida com autoria e instante UTC

### AC-RPO-002 — Registrar mudança de preço

**Dado** que existe observação válida anterior<br>
**Quando** o usuário informa um preço diferente, mantendo os demais dados comparáveis<br>
**Então** uma nova observação é criada e a anterior permanece imutável

### AC-RPO-003 — Registrar mudança de disponibilidade

**Dado** que o preço não mudou, mas a disponibilidade informada é diferente<br>
**Quando** a entrada é validada<br>
**Então** uma nova observação registra a mudança de estado

### AC-RPO-004 — Critério substituído: não duplicar polling automático idêntico

Este critério aplicava à captura manual a regra aprovada para consultas automáticas. Está substituído porque não decide o comportamento de uma nova ação manual explícita. A política dessa ação permanece pendente em F0-16. A supressão de leituras automáticas sem mudança pertence a uma spec futura de conector, não a este caso de uso manual.

### AC-RPO-005 — Não representar indisponibilidade como zero

**Dado** que a oferta está indisponível ou não tem preço legível/informado<br>
**Quando** a observação é registrada<br>
**Então** o estado fica separado do preço e nenhum zero fictício é criado

### AC-RPO-006 — Rejeitar dado inválido

**Dado** que falta moeda, instante/proveniência exigidos ou que o valor viola a política aprovada<br>
**Quando** o usuário tenta registrar a observação<br>
**Então** a entrada é rejeitada e o histórico anterior permanece intacto

### AC-RPO-007 — Impedir atualização de oferta de outra conta

**Dado** que a oferta pertence a outro usuário<br>
**Quando** o usuário tenta registrar observação<br>
**Então** a operação é recusada sem revelar ou alterar a oferta

## Regras de domínio aplicáveis

- `DR-002`, `DR-003`, `DR-004`, `DR-005`, `DR-011`, `DR-012`, `DR-017`, `DR-018`.

## Classes de domínio afetadas

| Classe/conceito | Responsabilidade/invariante | Critérios/regras |
|---|---|---|
| `PriceObservation` (proposta) | Imutabilidade, preço decimal/moeda, instante e origem manual | `AC-RPO-001`/`AC-RPO-002`/`AC-RPO-003`/`AC-RPO-005`/`AC-RPO-006`, `DR-002` a `DR-005`, `DR-018` |
| `Offer` (proposta) | Último estado observado e identidade da oferta | `AC-RPO-002`, `AC-RPO-003`, `AC-RPO-005` |

## Rastreabilidade dos testes

| Critério/regra | Caso de uso relacionado | Nome(s) planejado(s) do teste/display name | Caminho no projeto de testes | Estado |
|---|---|---|---|---|
| `AC-RPO-001`/`AC-RPO-002`/`AC-RPO-003`/`AC-RPO-005`/`AC-RPO-006`/`AC-RPO-007` | Registrar atualização manual | `AC-RPO-001 — Registrar primeira atualização válida`<br>`AC-RPO-002 — Registrar mudança de preço`<br>`AC-RPO-003 — Registrar mudança de disponibilidade`<br>`AC-RPO-005 — Não representar indisponibilidade como zero`<br>`AC-RPO-006 — Rejeitar dado inválido`<br>`AC-RPO-007 — Impedir atualização de oferta de outra conta` | `tests/Modules/PriceTracking.Tests/Features/RecordPriceObservation/` | Pendente da implementação |
| `AC-RPO-004` | Critério histórico substituído; polling não pertence a esta fatia manual | — (sem teste; ver F0-16) | Não aplicável | Substituído; sem teste |
| `AC-RPO-001`/`AC-RPO-002`/`AC-RPO-003`/`AC-RPO-005`/`AC-RPO-006`, `DR-002`/`DR-003`/`DR-004`/`DR-005`/`DR-018` | `PriceObservation` | `AC-RPO-001 — PriceObservation — Registrar primeira atualização válida`<br>`AC-RPO-002 — PriceObservation — Registrar mudança de preço`<br>`AC-RPO-003 — PriceObservation — Registrar mudança de disponibilidade`<br>`AC-RPO-005 — PriceObservation — Não representar indisponibilidade como zero`<br>`AC-RPO-006 — PriceObservation — Rejeitar dado inválido` | `tests/Modules/PriceTracking.Tests/Domain/PriceObservation/` | Pendente da implementação |
| `AC-RPO-002`/`AC-RPO-003`/`AC-RPO-005` | `Offer` | `AC-RPO-002 — Offer — Registrar mudança de preço`<br>`AC-RPO-003 — Offer — Registrar mudança de disponibilidade`<br>`AC-RPO-005 — Offer — Não representar indisponibilidade como zero` | `tests/Modules/PriceTracking.Tests/Domain/Offer/` | Pendente da implementação |

## Dados e integrações

- Entrada: oferta, preço decimal opcional conforme disponibilidade, moeda, disponibilidade e instante/proveniência definidos por F0-16.
- Persistência: observação imutável; comparação com a última observação válida da oferta.
- Efeitos externos: nenhum no MVP.
- Concorrência/repetição técnica da mesma submissão: regra idempotente a definir no plano técnico; não confundir uma repetição técnica com uma nova conferência manual.

## Requisitos não funcionais

- Persistir em transação; definir no plano técnico como tornar a mesma submissão segura a repetição sem suprimir capturas manuais intencionais.
- Normalizar instante para UTC e manter moeda explícita.
- A observação deve carregar marcador de origem manual e autoria conforme a política de privacidade aprovada.

## Questões em aberto

- Horário é de entrada ou o usuário informa quando leu o preço na loja?
- Uma nova captura manual em outro momento, com o mesmo preço e disponibilidade, cria uma observação datada ou é suprimida? Como distingui-la do retry da mesma submissão?
- Qual conjunto de estados de disponibilidade será aceito?
- Preço zero pode ser válido ou deve ser recusado?
- Como representar preço promocional, PIX, preço condicional e campos não informados?

## Decisões e referências

- [`docs/domain/context.md`](../../../domain/context.md)
- [`AddManualOffer`](../AddManualOffer/spec.md)
- [`docs/decisions/registro-fase-0.md`](../../../decisions/registro-fase-0.md) — F0-07 e F0-16.
