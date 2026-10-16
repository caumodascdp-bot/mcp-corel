# Módulo: `mcp_corel.mcp`

## Finalidade
Implementa a interface do servidor Model Context Protocol (MCP), provendo compatibilidade com clientes como AntiGravity, Claude Code e Codex via canal `stdio`.

## Componentes Planejados (Para Fase 2 em diante)
- `server.py`: Ponto de entrada do servidor MCP inicializando a SDK oficial e configurando o transporte `stdio`.
- `tools.py`: Registro das 16 ferramentas propostas mapeadas para validadores Pydantic e despachantes de tarefas.
- `handlers.py`: Tratamento de requisições JSON-RPC e formatação de saídas de erro (`isError: true`).
