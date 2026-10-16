# Espaço de Trabalho: Produto e Arquitetura

Este documento consolida o contexto, as diretrizes e as referências essenciais para os agentes e desenvolvedores que atuam na definição do produto, governança técnica e decisões arquiteturais.

---

## 1. Missão da Área
Garantir que o sistema atenda aos objetivos dos usuários, mantenha estabilidade técnica a longo prazo, preserve a integridade do modelo de dados e tome decisões arquiteturais fundamentadas em evidências.

---

## 2. Agentes Vinculados
- **Product Architect** (Líder técnico)
- **Art Director** (Design e briefing)
- **Orchestrator** (Fluxo de processos)

---

## 3. Arquivos e Recursos Centrais
- [Visão do Produto](file:///home/kali/mcp-corel/docs/PRODUCT_BRIEF.md)
- [Especificação Técnica](file:///home/kali/mcp-corel/docs/SPEC.md)
- [Arquitetura Geral](file:///home/kali/mcp-corel/docs/ARCHITECTURE.md)
- [Fluxos de Dados e Diagramas](file:///home/kali/mcp-corel/docs/DATA_FLOWS.md)
- [Cronograma de Fases](file:///home/kali/mcp-corel/docs/ROADMAP.md)
- [Decisões de Arquitetura (ADRs)](file:///home/kali/mcp-corel/docs/decisions/)

---

## 4. Regras e Restrições Vigentes
1. Nenhuma nova tecnologia ou biblioteca pode ser introduzida sem um ADR correspondente proposto e justificado.
2. Nenhuma capacidade técnica pode ser declarada funcional sem comprovação por logs de teste físico.
3. A comunicação com clientes de IA deve priorizar o padrão aberto Model Context Protocol (MCP) sobre `stdio`.
