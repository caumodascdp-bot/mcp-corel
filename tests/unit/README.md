# Testes Unitários: `tests/unit/`

## Escopo
Valida regras de negócio puras, validação de schemas Pydantic, máquina de estados da fila SQLite e lógica de conversão geométrica/cores sem dependência da aplicação CorelDRAW ou do sistema Windows.

## Arquivos de Teste Planejados
- `test_schemas.py`: Validação sintática e de tipos de briefings e manifestos.
- `test_state_machine.py`: Transições válidas e rejeição de transições inválidas na fila de jobs.
- `test_geometry.py`: Cálculos de coordenadas, sangrias e caixas delimitadoras.
