# Dicionário de Logs Operacionais — MCP Corel

Este dicionário mapeia os logs, mensagens de erro e alertas emitidos pelo sistema **MCP Corel**, explicando em português simples o que cada um significa, sua gravidade e a ação recomendada.

---

| Log / Mensagem de Erro | O que Significa | O que Fazer | Gravidade |
| :--- | :--- | :--- | :--- |
| `ERR_COREL_NOT_INSTALLED` | O executável do CorelDRAW não foi encontrado no caminho especificado. | Instale o CorelDRAW 2023 ou ajuste a variável `COREL_INSTALL_PATH` no arquivo `.env`. | **Grave** |
| `ERR_COREL_COM_INIT_FAILED` | Falha ao inicializar o servidor COM no Windows (ProgID não registrado ou incompatibilidade de arquitetura 32/64 bits). | Execute `python -m mcp_corel.corel.diagnostics` para testar os ProgIDs e verifique se o Python é 64-bit. | **Grave** |
| `ERR_COREL_COM_TIMEOUT` | Uma operação no CorelDRAW demorou mais de 60 segundos e foi abortada pelo watchdog. | Verifique se o CorelDRAW abriu uma janela de confirmação de fonte ou diálogo modal na tela. | **Média** |
| `ERR_COREL_DOCUMENT_NOT_FOUND` | O documento solicitado não está aberto ou o arquivo especificado não existe em disco. | Verifique o nome do documento aberto ou o caminho em `data/artifacts/`. | **Leve** |
| `ERR_SECURITY_PATH_OUT_OF_BOUNDS` | Tentativa de ler, salvar ou excluir arquivo fora da pasta autorizada (`SAFE_STORAGE_ROOT`). | Ajuste o caminho do arquivo para dentro da pasta `data/artifacts` ou configure o `.env`. | **Grave (Segurança)** |
| `ERR_SECURITY_UNAUTHORIZED_USER` | Mensagem recebida no Telegram vinda de uma conta não autorizada. | Adicione o ID numérico do usuário à variável `TELEGRAM_ALLOWED_USER_IDS` no `.env` se for um operador legítimo. | **Alerta (Segurança)** |
| `ERR_JOB_NOT_FOUND` | O identificador da tarefa (`job_id`) pesquisado não existe no banco de dados SQLite. | Verifique o número da tarefa enviado e consulte a lista com `/jobs`. | **Leve** |
| `ERR_JOB_INVALID_STATE_TRANSITION` | Tentativa de mudar o estado da tarefa para um estado não permitido pela máquina de estados. | Siga o fluxo natural: a tarefa só pode ser aprovada se estiver em `needs_approval`. | **Média** |
| `ERR_PREPRESS_RESOLUTION_LOW` | Uma imagem inserida na arte possui resolução inferior a 300 DPI recomendada para gráfica. | Substitua a imagem por uma versão de alta resolução antes do fechamento final. | **Aviso Gráfico** |
| `ERR_PREPRESS_RGB_IN_CMYK_DOC` | Foram encontrados textos ou cores RGB em um documento configurado para impressão CMYK. | Converta as cores dos objetos para a paleta CMYK. | **Aviso Gráfico** |
| `INFO_JOB_QUEUED` | A tarefa foi recebida com sucesso e está aguardando o processador de fila. | Nenhuma ação necessária; aguarde a execução. | **Informativo** |
| `INFO_JOB_RUNNING` | A tarefa está sendo executada no CorelDRAW pelo adaptador COM. | Aguarde a geração da prévia ou o término do desenho. | **Informativo** |
| `INFO_PREVIEW_GENERATED` | A prévia visual (PNG) foi exportada com sucesso e está pronta para inspeção. | Visualize a imagem na pasta de prévias ou no chat do Telegram para aprovação. | **Informativo** |
| `INFO_JOB_SUCCEEDED` | A tarefa foi finalizada e os arquivos finais (.CDR e .PDF) foram gravados com sucesso. | Arte pronta para entrega ou envio para gráfica. | **Sucesso** |
