# Arquitetura do Sistema — MCP Corel

Este documento descreve os componentes, camadas de software, padrões de comunicação e modelos de dados do ecossistema **MCP Corel**.

---

## 1. Visão Arquitetural em Camadas

O sistema é dividido em quatro camadas bem delimitadas:

```mermaid
flowchart TD
    subgraph CamadaCliente["1. Camada de Clientes e Interfaces"]
        MCPClient["Clientes de IA via MCP (AntiGravity / Claude Code / Codex)<br/>Transporte: stdio"]
        TGClient["Operador Humano via Telegram<br/>Transporte: HTTPS Long Polling de Saída"]
    end

    subgraph CamadaEntrada["2. Camada de Entrada e Segurança"]
        MCPHandler["Servidor MCP (Protocolo JSON-RPC 2.0)"]
        TGHandler["Telegram Poller & Router"]
        AuthGuard["Validador de Permissões & Allowlist"]
        PathSanitizer["Sanitizador de Caminhos (Safe Storage Guard)"]
    end

    subgraph CamadaOrquestracao["3. Camada de Orquestração e Fila"]
        JobManager["Gerenciador de Jobs & Fila Serializada"]
        JobDB[("Banco de Dados SQLite<br/>(jobs.sqlite3 em modo WAL)")]
        ApprovalGate["Portão de Aprovação Humana (Needs Approval)"]
    end

    subgraph CamadaExecucao["4. Camada de Automação Gráfica"]
        CorelAdapter["Adaptador CorelDRAW (Python / pywin32)"]
        COMWatchdog["COM Watchdog (Monitor de Timeout)"]
        CorelApp["Aplicação CorelDRAW 2023 (Windows Nativo)"]
        DiskStorage["Armazenamento Local (/data/artifacts e /data/previews)"]
    end

    MCPClient -->|stdio| MCPHandler
    TGClient <-->|HTTPS Long Polling| TGHandler

    MCPHandler --> AuthGuard
    TGHandler --> AuthGuard

    AuthGuard --> PathSanitizer
    PathSanitizer --> JobManager

    JobManager <--> JobDB
    JobManager --> ApprovalGate
    ApprovalGate --> CorelAdapter

    CorelAdapter <--> COMWatchdog
    CorelAdapter --> CorelApp
    CorelAdapter --> DiskStorage
```

---

## 2. Componentes Principais

### 2.1 Servidor MCP (`src/mcp_corel/mcp/`)
- Implementa as especificações oficiais do Model Context Protocol (JSON-RPC sobre `stdio`).
- Mapeia chamadas de ferramentas (*tools*) para comandos validados da fila de tarefas.
- Retorna respostas imediatas com o identificador da tarefa (`job_id`) ou executa ações síncronas de leitura sem travar o cliente.

### 2.2 Worker Telegram (`src/mcp_corel/telegram/`)
- Módulo independente que se conecta à API oficial do Telegram através de chamadas periódicas de saída (*getUpdates* via long polling).
- **Sem portas de entrada:** Opera sem webhook aberto na máquina local.
- Garante que apenas mensagens de IDs permitidos em `TELEGRAM_ALLOWED_USER_IDS` sejam processadas.

### 2.3 Gerenciador de Tarefas e Estado (`src/mcp_corel/jobs/`)
- Enfileira todas as operações que modificam o CorelDRAW.
- Assegura a **serialização absoluta** (apenas 1 operação de desenho por vez no CorelDRAW).
- Persiste o histórico em SQLite (`data/jobs/jobs.sqlite3`), permitindo auditar o que foi executado, quem pediu, quando iniciou e quanto tempo demorou.

### 2.4 Adaptador CorelDRAW (`src/mcp_corel/corel/`)
- Encapsula a interface COM do Windows (`pywin32` / `win32com.client`).
- Trata tipos de dados, conversão de unidades (milímetros para pontos do CorelDRAW), gerenciamento de erros COM (HRESULTs) e timeouts com watchdog.
- Protege a aplicação contra janelas modais indesejadas (configurando supressão de diálogos de fontes ou avisos via propriedades de automação da aplicação).

### 2.5 Módulo de Armazenamento e Segurança (`src/mcp_corel/storage/` & `security/`)
- Garante que todos os caminhos gerados ou solicitados estejam contidos na pasta segura autorizada (`SAFE_STORAGE_ROOT`).
- Bloqueia manipulações indevidas em arquivos mestres (.cdr) já existentes.

---

## 3. Diagrama: Fluxo de Comando MCP

Este diagrama ilustra o ciclo de vida de uma requisição originada por um agente de IA via cliente MCP (como o AntiGravity):

```mermaid
sequenceDiagram
    autonumber
    actor Agent as Agente de IA (AntiGravity)
    participant MCP as Servidor MCP (stdio)
    participant Sec as Guarda de Segurança
    participant Queue as Fila de Jobs (SQLite)
    participant Adapter as Adaptador CorelDRAW
    participant Corel as CorelDRAW 2023
    participant Disk as Armazenamento (data/)

    Agent->>MCP: call_tool("corel.create_shape", {tipo, x, y, largura, altura})
    MCP->>Sec: Validar parâmetros e permissões
    Sec-->>MCP: Parâmetros válidos
    MCP->>Queue: Criar tarefa (status: queued)
    Queue-->>MCP: Retorna job_id
    MCP-->>Agent: Retorna {job_id, status: "queued"}

    Note over Queue,Adapter: Fila processa de forma serial
    Queue->>Adapter: Executar tarefa (status: running)
    Adapter->>Corel: COM: Criar retângulo na coordenada (x, y)
    Corel-->>Adapter: Objeto criado com sucesso (ObjectID: 104)
    Adapter->>Disk: Exportar prévia da página atual (preview.png)
    Disk-->>Adapter: Prévia salva no disco
    Adapter->>Queue: Finalizar tarefa (status: succeeded, preview_url)
```

---

## 4. Diagrama: Fluxo de Comando Remoto Telegram

Este diagrama ilustra como um operador humano remoto interage de forma segura pelo Telegram:

```mermaid
sequenceDiagram
    autonumber
    actor User as Operador (Telegram Celular)
    participant TGServer as Servidores do Telegram (Nuvem)
    participant Poller as Telegram Worker Local (Windows)
    participant Sec as Verificador de ID (Allowlist)
    participant Queue as Fila de Tarefas
    participant Disk as Disco Local (data/)

    User->>TGServer: Envia comando "/status" ou "/aprovar job_123"
    Poller->>TGServer: Requisição de Saída getUpdates (Long Polling)
    TGServer-->>Poller: Entrega mensagem do usuário
    Poller->>Sec: O ID numérico está na allowlist?
    alt ID Não Autorizado
        Sec-->>Poller: Não autorizado
        Note over Poller: Mensagem descartada em silêncio (sem resposta)
    else ID Autorizado
        Sec-->>Poller: Autorizado
        Poller->>Queue: Atualiza estado da tarefa para "running" / "succeeded"
        Queue-->>Poller: Confirmação e dados da prévia
        Poller->>Disk: Lê arquivo de prévia (preview.png)
        Disk-->>Poller: Bytes da imagem
        Poller->>TGServer: Chamada de saída: sendPhoto(preview.png, botoes_aprovacao)
        TGServer-->>User: Exibe foto e botões no celular
    end
```

---

## 5. Diagrama: Recuperação de Falhas e Timeout COM

Como o sistema reage quando o CorelDRAW trava ou uma chamada COM não responde:

```mermaid
sequenceDiagram
    autonumber
    participant Queue as Fila de Tarefas
    participant Watchdog as Monitor COM (Watchdog)
    participant Adapter as Adaptador CorelDRAW
    participant Corel as CorelDRAW 2023

    Queue->>Adapter: Executar tarefa complexa
    Adapter->>Watchdog: Iniciar cronômetro (Timeout: 60s)
    Adapter->>Corel: COM: Inserir e calcular curvas
    Note over Corel: Aplicação congelada ou diálogo modal aberto bloqueando a chamada
    Watchdog-->>Adapter: ALERTA: Timeout de 60 segundos expirado!
    Adapter->>Corel: Tentar cancelar ou fechar diálogo modal
    Adapter->>Queue: Falha na tarefa (status: failed, erro: "TIMEOUT_COREL_COM")
    Queue-->>Queue: Registra erro no SQLite e libera a fila para o próximo job
```
