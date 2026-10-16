# Registro de Mudanças (Changelog)

Todas as alterações relevantes no projeto **MCP Corel** serão documentadas neste arquivo.

O formato baseia-se no [Keep a Changelog](https://keepachangelog.com/pt-BR/1.0.0/) e este projeto segue o [Versionamento Semântico](https://semver.org/lang/pt-BR/).

---

## [0.1.0-alpha] - 2026-10-16

### Adicionado (Fase 0 — Arquitetura, Estrutura e Governança)
- Estrutura completa de diretórios do repositório (`docs/`, `agents/`, `skills/`, `spaces/`, `contracts/`, `templates/`, `src/`, `tests/`, `data/`, `logs/`).
- Documentação inicial com `README.md`, `AGENTS.md`, `CONTEXT.md`, `CONTRIBUTING.md`, `SECURITY.md`, `LICENSE` e `pyproject.toml`.
- Especificações completas do produto:
  - `docs/SPEC.md`: Especificação funcional e não-funcional detalhada e numerada.
  - `docs/ARCHITECTURE.md` e `docs/DATA_FLOWS.md`: Diagramas de blocos, fluxos de dados e tratamento de falhas em Mermaid.
  - `docs/CORELDRAW_COMPATIBILITY.md`: Matriz de compatibilidade do CorelDRAW 2023 com estados e procedimentos de verificação.
  - `docs/MCP_COMPATIBILITY.md`: Matriz de compatibilidade para clientes MCP (AntiGravity, Codex, Claude Code).
  - `docs/TELEGRAM.md`: Arquitetura de operação remota segura via long polling e allowlist.
  - `docs/THREAT_MODEL.md` e `docs/PRIVACY.md`: Análise de ameaças, controles de segurança e privacidade de dados.
  - `docs/ROADMAP.md` e `docs/ACCEPTANCE_CRITERIA.md`: Planejamento por fases e critérios verificáveis de conclusão.
  - `docs/decisions/`: ADR-001 até ADR-005 documentando decisões técnicas propostas.
- Agentes especializados (11 agentes definidos em `agents/*/AGENT.md`).
- Habilidades operacionais (10 skills definidas em `skills/*/SKILL.md`).
- Contratos de ferramentas e máquina de estados em `contracts/`.
- Schemas JSON validados para briefs de arte e manifesto de artefatos.
- Dicionário operacional de logs em português (`docs/dicionario-logs.md`).
- Relatório de conclusão da Fase 0 em `docs/PHASE-0-REPORT.md`.

### Estado Atual
- Nenhum código executável de automação foi criado nesta versão, respeitando o princípio de arquitetura prévia obrigatória.
