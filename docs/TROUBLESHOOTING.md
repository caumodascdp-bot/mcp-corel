# Guia de Resolução de Problemas (Troubleshooting) — MCP Corel

Este documento orienta o diagnóstico e a solução rápida das falhas mais frequentes encontradas durante a operação do **MCP Corel**.

---

## 1. Problemas de Comunicação COM com o CorelDRAW

### 1.1 Erro `0x80040154: Classe não registrada` (Class not registered)
- **Sintoma:** O comando de diagnóstico falha ao tentar criar o objeto `CorelDRAW.Application`.
- **Causa:** O CorelDRAW não está instalado, foi instalado em modo restrito que não registra o servidor COM, ou há incompatibilidade de arquitetura (ex: Python 32-bit tentando instanciar CorelDRAW 64-bit).
- **Como Resolver:**
  1. Verifique se o Python instalado é 64 bits executando `python -c "import platform; print(platform.architecture())"`.
  2. Execute o CorelDRAW uma vez manualmente com privilégios de Administrador para forçar o auto-registro das chaves COM no Windows.
  3. Verifique se o ProgID correto é `CorelDRAW.Application.24` e atualize a variável `COREL_PROGID` no `.env`.

### 1.2 Erro `0x8001010A: O servidor de chamada não está disponível` (RPC Server Busy / Call Rejected)
- **Sintoma:** Chamadas de automação congelam ou retornam erro de servidor ocupado.
- **Causa:** O CorelDRAW está ocupado com uma operação pesada ou abriu uma janela de diálogo modal invisível (ex: alerta de substituição de fontes ausentes ou alerta de ativação de produto).
- **Como Resolver:**
  1. Traga a janela do CorelDRAW para o primeiro plano na área de trabalho e veja se há algum diálogo de confirmação aberto na tela.
  2. Feche a caixa de diálogo manualmente e marque a opção "Não mostrar novamente".
  3. No código de inicialização do adaptador, assegure que a propriedade de supressão de mensagens (`Optimization = True`) esteja ativada.

---

## 2. Problemas com o Servidor MCP

### 2.1 O Cliente de IA (AntiGravity / Claude) não reconhece as ferramentas
- **Sintoma:** O chat informa que o servidor MCP não está respondendo ou não possui ferramentas cadastradas.
- **Causa:** Erro de sintaxe na inicialização do Python ou saída de texto acidental no `stdout` que corrompeu o protocolo JSON-RPC.
- **Como Resolver:**
  1. Certifique-se de que nenhum comando `print()` sem formatação JSON seja emitido pelo código do servidor. Toda saída de depuração deve ser enviada para `stderr` ou para arquivos de log.
  2. Teste o servidor manualmente no PowerShell executando `python -m mcp_corel.mcp.server` e envie um JSON de inicialização via teclado.

---

## 3. Problemas com a Operação via Telegram

### 3.1 O Bot não responde a comandos no chat
- **Sintoma:** Você envia `/start` ou `/status` no Telegram e nada acontece.
- **Causa 1:** O seu ID do Telegram não está incluído em `TELEGRAM_ALLOWED_USER_IDS`. O bot descarta silenciosamente mensagens não autorizadas por segurança.
  - *Solução:* Descubra seu ID numérico (usando bots como `@userinfobot` no Telegram) e adicione o número no `.env`.
- **Causa 2:** O processo do worker não está em execução.
  - *Solução:* Inicie o processo `python -m mcp_corel.telegram.worker` e confira se há erros de conexão no console.

### 3.2 Erro `401 Unauthorized` da API do Telegram
- **Sintoma:** O log exibe erro de autenticação ao tentar conectar com a API do Telegram.
- **Causa:** O token `TELEGRAM_BOT_TOKEN` no arquivo `.env` está incorreto, expirado ou contém espaços extras.
- **Como Resolver:** Gere um novo token no `@BotFather` e atualize o arquivo `.env`.

---

## 4. Violações de Diretório de Segurança

### 4.1 Erro `ERR_SECURITY_PATH_OUT_OF_BOUNDS`
- **Sintoma:** A ferramenta de exportação ou importação falha com código de erro de segurança.
- **Causa:** O caminho especificado pelo usuário ou agente tenta acessar pastas fora de `SAFE_STORAGE_ROOT` (ex: `C:\Users\` ou `..\..\`).
- **Como Resolver:** Mova as imagens que deseja importar para dentro da pasta `C:\mcp-corel\data\artifacts` ou configure o `SAFE_STORAGE_ROOT` no `.env` para abranger a pasta desejada.
