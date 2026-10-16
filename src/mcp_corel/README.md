# Pacote Python: `mcp_corel`

> [!IMPORTANT]
> **AVISO DE FASE 0:**  
> Este diretório contém a estrutura de módulos planejada para a implementação do projeto. **Nenhum código funcional executável foi adicionado nesta fase.** Todos os submódulos contêm apenas documentação arquitetural de seu propósito e escopo.

---

## 1. Arquitetura de Módulos Internos

| Módulo | Caminho | Finalidade Planejada |
| :--- | :--- | :--- |
| **corel** | `src/mcp_corel/corel/` | Adaptador COM (`win32com`), watchdog e utilitário de diagnóstico. |
| **mcp** | `src/mcp_corel/mcp/` | Servidor MCP baseado na SDK oficial, transporte stdio e handlers. |
| **telegram** | `src/mcp_corel/telegram/` | Worker de long polling, filtros de ID e mensageria remota. |
| **orchestration**| `src/mcp_corel/orchestration/` | Despacho de tarefas, resolução de dependências e máquina de estados. |
| **jobs** | `src/mcp_corel/jobs/` | Gerenciador de fila e persistência SQLite (`aiosqlite`). |
| **artwork** | `src/mcp_corel/artwork/` | Modelos de dados de objetos vetoriais, grids e estilos. |
| **validation** | `src/mcp_corel/validation/` | Validadores de briefings, cores e regras de pré-impressão. |
| **storage** | `src/mcp_corel/storage/` | Gerenciamento de arquivos e manifesto de artefatos. |
| **security** | `src/mcp_corel/security/` | Sanitizador de caminhos, sandbox de diretório e mascaramento de segredos. |
| **observability**| `src/mcp_corel/observability/`| Estruturação de logs, métricas de tempo e monitoramento de saúde. |
