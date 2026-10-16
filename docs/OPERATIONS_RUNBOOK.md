# Manual de Operações (Runbook) — MCP Corel

Este manual fornece instruções práticas para o operador humano ou administrador do sistema manter o **MCP Corel** operando com estabilidade e segurança.

---

## 1. Inicialização dos Serviços

### 1.1 Como Iniciar o Servidor MCP (Modo stdio)
Geralmente, o servidor MCP é iniciado automaticamente pelo cliente de IA (como o AntiGravity ou Claude Code). Para testar manualmente via terminal Windows:
```powershell
.\.venv\Scripts\Activate.ps1
python -m mcp_corel.mcp.server
```

### 1.2 Como Iniciar o Worker do Telegram
Em um terminal separado na máquina Windows:
```powershell
.\.venv\Scripts\Activate.ps1
python -m mcp_corel.telegram.worker
```

---

## 2. Monitoramento de Rotina

### 2.1 Verificando a Saúde da Aplicação
- **Verificar se o CorelDRAW está em execução:**
  Abra o Gerenciador de Tarefas do Windows (`Ctrl + Shift + Esc`) e confira se o processo `CorelDRW.exe` está ativo e sem alto consumo estagnado de CPU.
- **Consultar a Fila de Tarefas:**
  Abra o Telegram e envie `/status` ou consulte diretamente o banco de dados:
  ```powershell
  sqlite3 C:\mcp-corel\data\jobs\jobs.sqlite3 "SELECT id, status, created_at FROM jobs ORDER BY id DESC LIMIT 5;"
  ```

### 2.2 Inspeção de Logs
Os logs operacionais são gravados na pasta `logs/`:
```powershell
Get-Content -Tail 50 -Wait logs\mcp_corel.log
```

---

## 3. Procedimentos de Emergência e Manutenção

### 3.1 O CorelDRAW Congelou ou Não Responde
Se uma tarefa estiver presa há mais de 2 minutos e o watchdog não tiver liberado:
1. Encerre o processo travado pelo PowerShell:
   ```powershell
   Stop-Process -Name "CorelDRW" -Force
   ```
2. Marque a tarefa travada como falha no SQLite:
   ```powershell
   sqlite3 C:\mcp-corel\data\jobs\jobs.sqlite3 "UPDATE jobs SET status='failed', error_message='Processo CorelDRAW finalizado manualmente' WHERE status='running';"
   ```
3. Abra novamente o CorelDRAW ou aguarde a próxima chamada instanciá-lo limpo.

### 3.2 Limpeza Periódica de Disco (Expurgo de Prévias Antigas)
Para liberar espaço em disco removendo prévias geradas há mais de 7 dias:
```powershell
# Remove prévias temporárias com mais de 7 dias sem tocar nos arquivos .CDR ou PDFs
Get-ChildItem -Path "C:\mcp-corel\data\previews\" -Recurse | Where-Object { $_.LastWriteTime -lt (Get-Date).AddDays(-7) } | Remove-Item -Force
```
*(Nota: Nunca execute comandos de limpeza dentro de `data/artifacts/`, pois nela residem os arquivos mestres dos clientes).*
