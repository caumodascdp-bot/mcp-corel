# Módulo: `mcp_corel.orchestration`

## Finalidade
Coordena o pipeline de criação artística, gerenciando a passagem de bastão entre os agentes (Art Director -> Layout Engineer -> Corel Automation -> Visual QA / Prepress -> Aprovação).

## Componentes Planejados (Para Fase 2 em diante)
- `pipeline.py`: Definição dos passos ordenados de construção de um documento gráfico a partir de um briefing.
- `gates.py`: Portões de verificação obrigatórios (portão de pré-impressão e portão de aprovação humana).
