# Módulo: `mcp_corel.telegram`

## Finalidade
Worker autônomo que executa comunicação bidirecional remota segura com a API do Telegram utilizando exclusivamente *HTTPS Long Polling* de saída.

## Componentes Planejados (Para Fase 4 em diante)
- `worker.py`: Loop de execução contínua com captura de `getUpdates`.
- `auth.py`: Verificador de lista de permissões (`TELEGRAM_ALLOWED_USER_IDS`).
- `commands.py`: Roteamento de comandos de texto (`/status`, `/jobs`, `/aprovar`, `/rejeitar`).
- `notifier.py`: Envio de mensagens com fotos de prévia e teclados inline de decisão.
