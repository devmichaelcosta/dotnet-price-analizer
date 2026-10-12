# ADR-001 — Framework e runner de testes

**Estado:** Proposta — 2026-10-12<br>
**Decisores:** Usuário do projeto<br>
**Contexto relacionado:** `docs/arquitetura-inicial.md`, seção 4.2; `docs/specs/README.md`

## Contexto

O projeto exige testes separados dos assemblies de produção e rastreáveis a cada critério `AC-...`, regra `DR-...`, caso de uso e classe concreta de domínio. A preferência aprovada é priorizar bibliotecas oficiais/suportadas pela Microsoft e evitar dependências sem necessidade concreta.

## Alternativas avaliadas

- **MSTest + `MSTest.Sdk` + `Microsoft.Testing.Platform`:** framework mantido e suportado pela Microsoft, multiplataforma e integrado ao .NET CLI, Visual Studio e CI.
- **xUnit ou NUnit:** frameworks conhecidos e maduros, mas mantidos por terceiros; exigiriam justificativa sob a preferência de dependências do projeto.
- **Framework próprio:** rejeitado por duplicar tooling e manutenção sem benefício.

## Decisão proposta

Adotar MSTest com `MSTest.Sdk`, usando `Microsoft.Testing.Platform` como runner padrão, para testes unitários, de domínio e casos de uso. Testes de integração usarão as extensões Microsoft suportadas quando atenderem às fronteiras necessárias. Manter pacotes de produção livres de dependências de teste. Versões e extensões exatas serão fixadas durante a implementação após validar compatibilidade com a versão LTS do .NET.

## Consequências

- A estrutura de projetos seguirá o template oficial e os caminhos espelhados de módulo/use case já definidos.
- Os testes usarão assertions MSTest inicialmente; qualquer biblioteca externa de assertions, mocking ou fixtures requer justificativa e avaliação de manutenção/licença.
- O runner deve executar no `dotnet test`/pipeline e suportar execução local repetível.
- A escolha permanece proposta até o usuário revisar/aprovar o gate da Fase 0.

## Referências oficiais

- [MSTest — introdução e suporte](https://learn.microsoft.com/en-us/dotnet/core/testing/unit-testing-mstest-intro)
- [Começar com MSTest e `MSTest.Sdk`](https://learn.microsoft.com/en-us/dotnet/core/testing/unit-testing-mstest-getting-started)
- [Testar uma biblioteca .NET 10](https://learn.microsoft.com/en-us/dotnet/core/tutorials/test-class-library)
