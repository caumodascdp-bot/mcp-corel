# Módulo: `mcp_corel.observability`

## Finalidade
Fornece infraestrutura de observabilidade estruturada, incluindo logs auditáveis em JSON/texto formatado, medição de latência de chamadas COM e registro de incidentes operacionais.

## Componentes Planejados (Para Fase 2 em diante)
- `logger.py`: Configuração de logging estruturado (via `structlog`) emitindo logs para `logs/mcp_corel.log` e para `stderr`.
- `metrics.py`: Contadores de tempo de execução por ferramenta e taxas de sucesso/falha da automação gráfica.
