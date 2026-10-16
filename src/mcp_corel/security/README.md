# Módulo: `mcp_corel.security`

## Finalidade
Garante a segurança da estação de trabalho através da sanitização estrita de caminhos de arquivos (prevenção de Path Traversal), bloqueio de shells arbitrários e higienização de logs contra vazamento de credenciais.

## Componentes Planejados (Para Fase 2 em diante)
- `path_guard.py`: Validador canônico que garante que todo caminho manipulado resida estritamente dentro de `SAFE_STORAGE_ROOT`.
- `redactor.py`: Filtro de higienização de strings que mascara tokens de API e credenciais em logs e payloads de resposta.
- `input_filter.py`: Sanitizador de caracteres de controle e proteção contra injeções.
