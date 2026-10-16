# Cronograma de Execução e Fases (Roadmap) — MCP Corel

Este documento orienta a evolução do projeto em fases lineares e dependentes. **Nenhuma fase pode ser iniciada antes que os critérios de passagem da fase anterior tenham sido validados com evidência real.**

---

## 1. Tabela Resumo das Fases

| Fase | Descrição | Estado Atual | Dependência Prévia | Evidência Exigida para Fechamento |
| :--- | :--- | :--- | :--- | :--- |
| **Fase 0** | **Arquitetura, Contratos e Governança** | `verified` | Nenhuma | Repositório estruturado, schemas validados, sem código executável. |
| **Fase 1** | **Diagnóstico e PoC COM CorelDRAW** | `planned` | Aprovação humana da Fase 0 | Relatório de diagnóstico executado no Windows 10 comprovando controle do Corel. |
| **Fase 2** | **Servidor MCP Local & Fila de Jobs** | `planned` | Fase 1 comprovada | Testes unitários com mocks passando; stdio funcional com AntiGravity e Claude Code. |
| **Fase 3** | **Motor de Criação Gráfica & Exportação** | `planned` | Fase 2 comprovada | Arquivo .CDR criado, formas desenhadas e PDF com marcas exportado com sucesso. |
| **Fase 4** | **Bot Telegram (Long Polling Remoto)** | `planned` | Fase 3 comprovada | Envio de prévia e aprovação remota via chat testada e sem portas abertas. |
| **Fase 5** | **Hardening, Testes End-to-End & MVP** | `planned` | Fase 4 comprovada | Suíte de testes completa passando e checklist de release aprovado. |

---

## 2. Detalhamento por Fase

### Fase 0: Arquitetura, Contratos e Governança (Concluída)
- **Objetivo:** Estabelecer as bases técnicas, regras de segurança, schemas JSON, diretrizes de agentes e skills antes de escrever qualquer código.
- **Entregáveis:** Estrutura completa de pastas, 11 agentes em `agents/`, 10 skills em `skills/`, 5 ADRs, schemas validados em `contracts/` e relatórios.
- **Critério de Passagem:** Todos os arquivos criados, sem código funcional, schemas válidos e aprovação humana explícita.

### Fase 1: Diagnóstico de Ambiente & Prova de Conceito (PoC)
- **Objetivo:** Descobrir as capacidades reais da instalação do CorelDRAW 2023 no Windows 10.
- **Entregáveis:** 
  - Utilitário `mcp_corel.corel.diagnostics` para coletar ProgID, versão de bits e formatos suportados.
  - Script isolado de PoC que abre o CorelDRAW, desenha 1 quadrado, salva 1 arquivo de teste e fecha.
- **Critério de Passagem:** Log real de execução na máquina Windows confirmando comunicação COM bem-sucedida.

### Fase 2: Servidor MCP Local & Fila Serializada (SQLite)
- **Objetivo:** Construir o servidor MCP padrão rodando sobre `stdio` e a fila de processamento serializado.
- **Entregáveis:**
  - Servidor MCP compatível com protocolo v1.0.
  - Implementação de `jobs.sqlite3` com tabela de tarefas, logs e estados.
  - Tratamento de timeout de 60s via watchdog.
- **Critério de Passagem:** AntiGravity ou Claude Code conectando ao servidor via stdio e criando um job na fila.

### Fase 3: Motor de Leiaute Gráfico & Exportação
- **Objetivo:** Implementar as ferramentas de desenho vetorial, formatação de textos e exportação de prévias/fechamentos.
- **Entregáveis:**
  - Ferramentas `corel.create_shape`, `corel.add_text`, `corel.import_asset`.
  - Ferramenta `corel.preview` gerando PNG a 150 DPI.
  - Ferramenta `corel.export_document` gerando PDF para pré-impressão com sangria.
- **Critério de Passagem:** Criação completa de um flyer modelo comprovada por inspeção visual da prévia.

### Fase 4: Operação Remota via Telegram (Long Polling)
- **Objetivo:** Integrar canal remoto seguro para operadores via celular.
- **Entregáveis:**
  - Worker Telegram baseado em `python-telegram-bot` usando estritamente conexão de saída (*long polling*).
  - Filtro rígido de allowlist por ID numérico.
  - Comandos de status, envio de prévia e aprovação interativa com botões.
- **Critério de Passagem:** Aprovação de uma arte pelo celular com entrega do arquivo PDF diretamente na conversa.

### Fase 5: Hardening de Segurança, Testes E2E & Release MVP
- **Objetivo:** Blindagem final e validação abrangente do produto.
- **Entregáveis:**
  - Testes de segurança contra tentativas de path traversal e injeção de parâmetros.
  - Manual de operações (`OPERATIONS_RUNBOOK.md`) e de resolução de problemas (`TROUBLESHOOTING.md`) atualizados com casos reais.
- **Critério de Passagem:** 100% dos testes automatizados passando; 0 vulnerabilidades abertas; liberação da versão v1.0.0.
