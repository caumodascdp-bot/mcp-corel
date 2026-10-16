# Tabela Canônica de Códigos de Erro — MCP Corel

Este documento padroniza todos os códigos de erro emitidos pelas ferramentas do servidor MCP, pelos módulos internos e pelo worker do Telegram.

---

## 1. Classificação por Domínio

Os códigos de erro são prefixados pelo módulo de origem:
- `ERR_COREL_*`: Erros de automação, COM e controle da aplicação CorelDRAW.
- `ERR_SECURITY_*`: Violações de sandbox, permissões e tentativas de injeção.
- `ERR_VALIDATION_*`: Parâmetros inválidos em chamadas de ferramentas ou schemas JSON.
- `ERR_JOB_*`: Falhas na máquina de estados e enfileiramento de tarefas.
- `ERR_PREPRESS_*`: Inconformidades técnicas de impressão gráfica.
- `ERR_TELEGRAM_*`: Falhas de rede, limites de anexos ou autenticação no Telegram.

---

## 2. Dicionário de Códigos de Erro

| Código de Erro | Gravidade | Descrição Técnica | Ação Corretiva Recomendada |
| :--- | :--- | :--- | :--- |
| `ERR_COREL_NOT_INSTALLED` | Crítica | Executável `CorelDRW.exe` não encontrado no caminho do Windows. | Conferir instalação física do CorelDRAW 2023 ou ajustar `COREL_INSTALL_PATH`. |
| `ERR_COREL_COM_INIT_FAILED` | Crítica | Falha ao instanciar o ProgID COM (`0x80040154`). | Rodar diagnóstico `mcp-corel-diag` e verificar se a versão do Python é 64-bit. |
| `ERR_COREL_COM_TIMEOUT` | Alta | A chamada COM ultrapassou o limite de 60 segundos do watchdog. | Verificar se há diálogos modais bloqueando a janela do CorelDRAW na estação. |
| `ERR_COREL_DOCUMENT_NOT_FOUND` | Média | O documento especificado não está aberto ou não existe. | Listar documentos abertos via `corel.list_documents` ou verificar nome. |
| `ERR_SECURITY_PATH_OUT_OF_BOUNDS` | Crítica | Tentativa de ler ou gravar arquivos fora da raiz segura (`SAFE_STORAGE_ROOT`). | Manter todos os caminhos dentro da pasta autorizada `data/`. |
| `ERR_SECURITY_UNAUTHORIZED_USER` | Alta | Mensagem no Telegram recebida de ID não listado em `TELEGRAM_ALLOWED_USER_IDS`. | Descarte silencioso; cadastrar ID no `.env` se for operador autorizado. |
| `ERR_SECURITY_SHELL_INJECTION` | Crítica | Tentativa de injetar comandos de terminal em parâmetros de texto. | Bloqueio imediato da requisição e registro no log de segurança. |
| `ERR_VALIDATION_SCHEMA_FAILED` | Média | O payload fornecido não atende às regras do schema JSON da ferramenta. | Corrigir os campos de entrada de acordo com a documentação em `contracts/mcp-tools.md`. |
| `ERR_VALIDATION_INVALID_DIMENSIONS` | Média | Largura ou altura solicitada menor ou igual a zero. | Informar valores numéricos positivos em milímetros. |
| `ERR_JOB_NOT_FOUND` | Baixa | O `job_id` consultado não existe na base de dados SQLite. | Consultar tarefas existentes antes de tentar cancelar ou aprovar. |
| `ERR_JOB_INVALID_STATE_TRANSITION` | Média | Transição de estado não permitida (ex: tentar aprovar tarefa já concluída). | Respeitar o fluxo formal documentado em `contracts/job-state-machine.md`. |
| `ERR_PREPRESS_RESOLUTION_LOW` | Aviso | Imagem importada possui resolução inferior a 300 DPI recomendada para impressão. | Fornecer imagem com resolução gráfica profissional. |
| `ERR_PREPRESS_RGB_IN_CMYK_DOC` | Aviso | Elementos em RGB detectados em documento destinado à pré-impressão CMYK. | Converter cores para CMYK antes de gerar o PDF final. |
| `ERR_FILE_ALREADY_EXISTS` | Média | Tentativa de salvar arquivo com nome já existente sem flag `overwrite=true`. | Confirmar se deseja sobrescrever ou fornecer novo nome de arquivo. |
| `ERR_CRASH_ORPHAN_TASK` | Alta | Tarefa interrompida no estado `running` por queda repentina de energia ou reinício. | Tarefa marcada como falha na reinicialização para liberar a fila. |
