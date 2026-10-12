# Especificação — ExtractAndNormalizeOffer

**Estado:** Rascunho condicional — não compõe o MVP manual; nenhum domínio HTML elegível em 2026-10-12<br>
**Módulo:** WebDiscovery<br>
**Responsável pelo domínio:** Produto

> Esta fatia só poderá ser implementada depois que os termos permitirem expressamente coleta HTML automatizada no domínio e o gate individual for aprovado. A triagem atual não aprovou nenhum domínio. Não há caminho de API como fonte de dados.

## Problema e objetivo

Converter uma página pública elegível em dados normalizados de produto, oferta, vendedor, preço e disponibilidade, preservando incerteza e origem.

## Atores e autorização

- Ator: Worker local processando uma referência de página aprovada.
- Só processa página pública obtida por fonte habilitada.
- O parser não realiza compras nem ações autenticadas.

## Contexto e regras de domínio

- `DR-001`: produto, página, oferta e observação não se confundem.
- `DR-003`/`DR-004`/`DR-005`: decimal/moeda, origem/UTC e indisponibilidade separada do preço.
- `DR-006`: variantes, condições, kits e vendedores incompatíveis não se fundem.
- `DR-007`: comparação sem frete no MVP.

## Escopo

### Incluído

- Extrair campos visíveis ou estruturados e normalizar unidades/campos conservando a referência original.
- Marcar campo ausente/ambíguo como desconhecido, sem inventar valor.

### Fora de escopo

- Inferência por IA que não possa ser explicada ou validada.
- Combinar preço de frete, CEP, parcelamento ou moedas por conversão no primeiro corte.

## Comportamento esperado

O parser processa HTML permitido e retorna candidatos normalizados, confiança/ambiguidades e evidência de origem. Se houver múltiplas ofertas ou vendedores, cada uma fica separada. O preço extraído corresponde à regra de preço a ser fechada na Fase 0; sem preço confiável, o candidato pode ser exibido sem observação de preço.

## Cenários de aceite

### AC-ENO-001 — Extrair dados com origem

**Dado** que a página é pública e pertence a um domínio habilitado<br>
**Quando** o conteúdo contém dados de produto/oferta válidos<br>
**Então** o resultado inclui os campos observados e a referência da página/fonte

### AC-ENO-002 — Separar ofertas de vendedores distintos

**Dado** que a página expõe anúncios de vendedores diferentes<br>
**Quando** os dados são normalizados<br>
**Então** cada anúncio permanece como oferta independente

### AC-ENO-003 — Preservar variante e condição

**Dado** que uma página mostra variante, kit ou condição distinta<br>
**Quando** a oferta é extraída<br>
**Então** esses atributos são preservados e não são fundidos com produto incompatível

### AC-ENO-004 — Não inventar preço ou disponibilidade

**Dado** que o preço ou estoque não pode ser lido com confiança<br>
**Quando** a página é processada<br>
**Então** o campo fica desconhecido/indisponível e nenhum valor artificial é gravado

### AC-ENO-005 — Excluir frete da comparação

**Dado** que a página mostra preço do item e frete<br>
**Quando** o preço inicial é normalizado<br>
**Então** o preço do item fica separado e frete não compõe a comparação

## Regras de domínio aplicáveis

- `DR-001`, `DR-003`, `DR-004`, `DR-005`, `DR-006`, `DR-007`.

## Classes de domínio afetadas

| Classe/conceito | Responsabilidade/invariante | Critérios/regras |
|---|---|---|
| `Product` (proposta) | Identidade canônica, distinta da página | `AC-ENO-001`, `AC-ENO-003`, `DR-001`, `DR-006` |
| `Offer` (proposta) | Vendedor, variante, preço e disponibilidade do anúncio | `AC-ENO-001` a `AC-ENO-005`, `DR-001`/`DR-006` |
| `SourcePage` (proposta) | Proveniência do conteúdo | `AC-ENO-001`, `DR-004` |

## Rastreabilidade dos testes

| Critério/regra | Caso de uso relacionado | Nome(s) planejado(s) do teste/display name | Caminho no projeto de testes | Estado |
|---|---|---|---|---|
| `AC-ENO-001` a `AC-ENO-005` | Extração/normalização | `AC-ENO-001 — Extrair dados com origem`<br>`AC-ENO-002 — Separar ofertas de vendedores distintos`<br>`AC-ENO-003 — Preservar variante e condição`<br>`AC-ENO-004 — Não inventar preço ou disponibilidade`<br>`AC-ENO-005 — Excluir frete da comparação` | `tests/Modules/WebDiscovery.Tests/Features/ExtractAndNormalizeOffer/` | Pendente da implementação |
| `AC-ENO-001`/`AC-ENO-003`, `DR-001`/`DR-006` | `Product` (propriedade de ProductCatalog) | `AC-ENO-001 — Product — Extrair dados com origem`<br>`AC-ENO-003 — Product — Preservar variante e condição` | `tests/Modules/ProductCatalog.Tests/Domain/Product/` | Pendente da implementação |
| `AC-ENO-001` a `AC-ENO-005`, `DR-001`/`DR-006` | `Offer` (propriedade de PriceTracking) | `AC-ENO-001 — Offer — Extrair dados com origem`<br>`AC-ENO-002 — Offer — Separar ofertas de vendedores distintos`<br>`AC-ENO-003 — Offer — Preservar variante e condição`<br>`AC-ENO-004 — Offer — Não inventar preço ou disponibilidade`<br>`AC-ENO-005 — Offer — Excluir frete da comparação` | `tests/Modules/PriceTracking.Tests/Domain/Offer/` | Pendente da implementação |
| `AC-ENO-001`, `DR-004` | `SourcePage` | `AC-ENO-001 — SourcePage — Extrair dados com origem` | `tests/Modules/WebDiscovery.Tests/Domain/SourcePage/` | Pendente da implementação |

## Dados e integrações

- Entrada: HTML permitido e metadados de fonte/data.
- Saída: candidatos normalizados com evidência e campos desconhecidos explícitos.
- Efeitos: persistir oferta/candidato, não preço histórico até validação de correspondência e preço.
- Idempotência: mesma fonte e versão/identificador de página não deve duplicar resultados.

## Requisitos não funcionais

- Parser não executa scripts, não acessa APIs ocultas e não contorna controles.
- Campo original/fonte deve ser rastreável para corrigir parsing.
- Política de guardar HTML bruto depende dos termos da fonte.

## Questões em aberto

- Definição de preço oficial quando existem PIX, à vista, parcelado e promoções.
- Esquema de confiança e confirmação por campo.
- Normalização de marca/modelo/variante e atributos específicos de categoria.

## Decisões e referências

- [`docs/domain/context.md`](../../../domain/context.md)
