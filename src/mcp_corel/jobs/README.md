# Módulo: `mcp_corel.jobs`

## Finalidade
Gerencia a fila persistente de tarefas no banco de dados local SQLite (`jobs.sqlite3`), assegurando execução sequencial (serializada), controle transacional de estados e recuperação pós-falha.

## Componentes Planejados (Para Fase 2 em diante)
- `database.py`: Inicialização do SQLite em modo WAL e execução de migrations de tabela.
- `queue.py`: Despachante da fila de tarefas que consome itens em estado `queued`.
- `state_machine.py`: Validador de transições de estado (`contracts/job-state-machine.md`).
- `recovery.py`: Rotina de inicialização que detecta e recupera tarefas órfãs (`running`).
