# Matriz de Compatibilidade MCP por Cliente

Este documento especifica como o servidor **MCP Corel** deve ser configurado e integrado em diferentes clientes compatíveis com o protocolo **Model Context Protocol (MCP)**.

---

## 1. Requisitos Gerais do Protocolo MCP

O **MCP Corel** adere às seguintes especificações do padrão MCP:
- **Versão da Especificação:** Model Context Protocol v1.0+.
- **Padrão de Transporte Padrão:** `stdio` (Processo filho controlado pelo cliente via stdin/stdout com mensagens JSON-RPC 2.0 delimitadas por quebra de linha).
- **Semântica de Ferramentas (Tools):** Todas as ferramentas expõem schemas estritos compatíveis com JSON Schema Draft-07.
- **Tratamento de Saída:** Erros de execução retornam `isError: true` no protocolo MCP sem fechar a conexão do processo.

---

## 2. Matriz de Clientes MCP

| Cliente | Transporte Suportado | Configuração Documentada | Estado do Teste | Limitações Conhecidas | Evidência |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Google AntiGravity** | `stdio` | Configuração JSON em `.gemini/` ou `.antigravity/` | `not_verified` | Exige caminho absoluto do executável Python no Windows. | Nenhuma evidência de teste físico coletada até o momento. |
| **Claude Code** | `stdio` | Configuração via `claude mcp add` ou arquivo `.mcp.json` | `not_verified` | Executa no terminal; prévias em imagem devem ser salvas em disco para visualização. | Nenhuma evidência de teste físico coletada até o momento. |
| **Codex (CLI / Extension)**| `stdio` | Configuração de subprocesso em settings JSON | `not_verified` | Depende do suporte a ferramentas assíncronas do cliente. | Nenhuma evidência de teste físico coletada até o momento. |
| **Clientes Genéricos MCP** | `stdio` | Execução via `python -m mcp_corel.mcp.server` | `not_verified` | Clientes que não suportam ferramentas de retorno longo precisarão consultar a fila via pooling. | Nenhuma evidência coletada. |

---

## 3. Configurações por Cliente (Exemplos Propostos)

### 3.1 Configuração para o Google AntiGravity
No arquivo de configuração de extensões/servidores MCP do AntiGravity no Windows:

```json
{
  "mcpServers": {
    "coreldraw": {
      "command": "C:\\Python311\\python.exe",
      "args": ["-m", "mcp_corel.mcp.server"],
      "env": {
        "WORK_DIR": "C:\\mcp-corel\\data",
        "SAFE_STORAGE_ROOT": "C:\\mcp-corel\\data",
        "MCP_LOG_LEVEL": "INFO"
      }
    }
  }
}
```

### 3.2 Configuração para o Claude Code
No arquivo `.mcp.json` na raiz do projeto ou no diretório de configuração do usuário:

```json
{
  "mcpServers": {
    "corel": {
      "command": "python",
      "args": ["-m", "mcp_corel.mcp.server"],
      "env": {
        "WORK_DIR": "C:\\mcp-corel\\data"
      }
    }
  }
}
```
Ou adicionando via terminal:
```bash
claude mcp add corel python -m mcp_corel.mcp.server
```

---

## 4. Procedimento de Validação de Cliente

Para validar um cliente na Fase 2:
1. Iniciar o cliente apontando para o servidor `mcp-corel`.
2. Executar a chamada de ferramenta `corel.get_status`.
3. Validar se o retorno com a versão e o status do sistema foi recebido em menos de 2 segundos.
4. Registrar o log da sessão JSON-RPC em `logs/mcp_handshake_<cliente>.log`.
