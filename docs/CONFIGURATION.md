# Guia de Configuração — MCP Corel

Este documento detalha todas as variáveis de ambiente, parâmetros de inicialização e diretórios de configuração utilizados pelo **MCP Corel**.

---

## 1. Arquivo de Configuração (.env)

O sistema carrega suas configurações a partir de variáveis de ambiente do sistema operacional ou de um arquivo `.env` localizado na raiz do projeto.

Para iniciar, copie o modelo de exemplo:
```bash
copy .env.example .env
```

---

## 2. Dicionário Completo de Parâmetros

| Variável | Tipo | Valor Padrão | Obrigatório? | Descrição |
| :--- | :--- | :--- | :--- | :--- |
| `MCP_TRANSPORT` | `string` | `stdio` | Não | Modo de transporte do servidor MCP. Opções suportadas: `stdio`. |
| `MCP_LOG_LEVEL` | `string` | `INFO` | Não | Nível de detalhe dos logs de execução (`DEBUG`, `INFO`, `WARNING`, `ERROR`). |
| `COREL_INSTALL_PATH` | `string` | *Caminho padrão x64* | Não | Caminho completo até o executável `CorelDRW.exe` no Windows. |
| `COREL_PROGID` | `string` | `CorelDRAW.Application.24` | Não | ProgID utilizado para conectar via COM ao CorelDRAW 2023. |
| `COREL_COM_TIMEOUT_SECONDS`| `integer` | `60` | Não | Tempo máximo em segundos para esperar o retorno de uma chamada COM antes de abortar. |
| `COREL_VISIBLE` | `boolean` | `true` | Não | Se `true`, a janela do CorelDRAW fica visível na área de trabalho; se `false`, roda minimizado. |
| `WORK_DIR` | `string` | `C:\mcp-corel\data` | **SIM** | Diretório base para persistência e dados de trabalho. |
| `SAFE_STORAGE_ROOT` | `string` | `C:\mcp-corel\data` | **SIM** | Raiz de segurança máxima fora da qual nenhuma operação em disco é permitida. |
| `JOBS_DB_PATH` | `string` | `C:\mcp-corel\data\jobs\jobs.sqlite3` | Não | Caminho absoluto do banco de dados SQLite da fila de tarefas. |
| `TELEGRAM_BOT_ENABLED` | `boolean` | `false` | Não | Habilita ou desabilita o worker de integração com o Telegram. |
| `TELEGRAM_BOT_TOKEN` | `string` | *(Vazio)* | Se telegram ativo | Token de autenticação fornecido pelo `@BotFather`. |
| `TELEGRAM_ALLOWED_USER_IDS`| `string` | *(Vazio)* | Se telegram ativo | Lista de IDs numéricos de contas autorizadas no Telegram (separados por vírgula). |
| `TELEGRAM_POLL_TIMEOUT` | `integer` | `30` | Não | Tempo de espera (em segundos) de cada requisição long polling de saída. |
| `MAX_IMPORT_FILE_SIZE_MB` | `integer` | `50` | Não | Limite máximo em megabytes para importação de imagens em artes. |
| `MAX_EXPORT_FILE_SIZE_MB` | `integer` | `100` | Não | Limite máximo de tamanho para exportações completas. |

---

## 3. Diretórios de Trabalho

Os seguintes diretórios devem possuir permissão de leitura e escrita para o usuário que executa o processo Python no Windows:
- `data/jobs/`: Armazena o banco SQLite de tarefas (`jobs.sqlite3`).
- `data/previews/`: Armazena as imagens temporárias geradas para visualização rápida (`.png`, `.jpg`).
- `data/artifacts/`: Armazena os documentos definitivos (`.cdr`, `.pdf`, `.eps`).
- `logs/`: Armazena os registros de auditoria e depuração da aplicação.
