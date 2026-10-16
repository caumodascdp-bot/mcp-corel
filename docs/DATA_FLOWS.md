# Fluxos de Dados — MCP Corel

Este documento detalha o tráfego de dados, a máquina de estados de tarefas e o ciclo completo de criação artística no sistema **MCP Corel**.

---

## 1. Fluxo Completo de Criação de Arte (Briefing à Entrega)

O ciclo de vida de uma arte gráfica passa por 6 etapas bem definidas entre os agentes especializados e o CorelDRAW:

```mermaid
flowchart TD
    subgraph 1_Entrada["1. Concepção & Briefing"]
        B1["Briefing do Cliente / Usuário"] --> B2["Art Director:<br/>Estrutura hierarquia visual,<br/>paleta e textos literais"]
        B2 --> B3["Briefing Estruturado<br/>(artwork-brief.schema.json)"]
    end

    subgraph 2_Planejamento["2. Engenharia de Leiaute"]
        B3 --> L1["Layout Engineer:<br/>Calcula coordenadas (x,y),<br/>dimensões, sangria e guias"]
        L1 --> L2["Plano Vetorial Paramétrico"]
    end

    subgraph 3_Execucao["3. Execução no CorelDRAW"]
        L2 --> C1["Fila de Tarefas (Queue)"]
        C1 --> C2["Corel Automation:<br/>Adaptador COM executa comandos"]
        C2 --> C3["Desenho do Documento (.CDR)<br/>em C:\\mcp-corel\\data\\artifacts"]
    end

    subgraph 4_Previa["4. Inspeção Visual"]
        C3 --> P1["Exportação de Prévia (PNG 150 DPI)"]
        P1 --> P2["Visual QA:<br/>Verifica alinhamentos e contraste"]
        P2 --> P3["Prepress:<br/>Valida sangrias, CMYK e resolução"]
    end

    subgraph 5_Aprovacao["5. Portão de Aprovação"]
        P3 --> A1{"Aprovação do<br/>Operador Humano?"}
        A1 -- Não (Requer Ajuste) --> B2
        A1 -- Sim (Aprovado) --> E1["Exportação Final"]
    end

    subgraph 6_Entrega["6. Fechamento & Entrega"]
        E1 --> F1["PDF Pré-Impressão (com marcas de corte)"]
        E1 --> F2["Arquivo Mestre .CDR salvo"]
        E1 --> F3["Imagens Web (PNG/JPG)"]
        F1 --> D1["Entrega via Telegram ou Pasta de Saída"]
        F2 --> D1
        F3 --> D1
    end
```

---

## 2. Máquina de Estados de Tarefas (Job State Machine)

Toda operação assíncrona registrada no sistema transita por estados controlados no banco de dados SQLite:

```mermaid
stateDiagram-v2
    [*] --> queued: Tarefa criada pela IA ou Telegram

    queued --> running: Fila inicia o processamento serial
    queued --> cancelled: Operador solicita cancelamento

    running --> needs_approval: Prévia gerada com sucesso, aguardando validação humana
    running --> succeeded: Operação sem necessidade de aprovação finalizada
    running --> failed: Erro de execução COM, timeout ou validação

    needs_approval --> running: Operador aprovou; iniciando exportação final
    needs_approval --> cancelled: Operador rejeitou a tarefa

    failed --> queued: Tentativa de reprocessamento (se configurada)
    failed --> [*]
    succeeded --> [*]
    cancelled --> [*]
```

### Tabela de Transições de Estado

| Estado Atual | Próximo Estado Válido | Disparador / Gatilho | Condição de Validação |
| :--- | :--- | :--- | :--- |
| `queued` | `running` | Fila liberada para novo item | Nenhuma tarefa em execução no CorelDRAW. |
| `queued` | `cancelled` | Chamada `corel.cancel_job` | Tarefa ainda não havia começado. |
| `running` | `needs_approval` | Prévia PNG gerada com sucesso | Configuração exige revisão humana antes de fechar. |
| `running` | `succeeded` | Operação concluída sem pendências | Artefato final gravado e validado. |
| `running` | `failed` | Exceção COM, timeout ou erro de disco | Código de erro registrado em `error_code`. |
| `needs_approval` | `running` | Comando `/aprovar` ou aprovação MCP | Confirmação explícita do operador. |
| `needs_approval` | `cancelled` | Rejeição do operador no Telegram/MCP | Motivo da rejeição registrado no job. |

---

## 3. Recuperação de Tarefas Órfãs após Falha do Sistema

Se a máquina Windows ou o processo do servidor for interrompido abruptamente enquanto uma tarefa estava no estado `running`:

```mermaid
flowchart TD
    Boot["Inicialização do Servidor MCP / Worker"] --> Scan["Varrer SQLite buscando jobs em estado 'running'"]
    Scan --> Found{"Encontrou jobs em 'running'?"}
    Found -- Não --> Idle["Iniciar escuta normal da fila"]
    Found -- Sim --> Mark["Marcar tarefas encontradas como 'failed'"]
    Mark --> Log["Registrar motivo: SYSTEM_CRASH_OR_UNEXPECTED_SHUTDOWN"]
    Log --> Unlock["Liberar trava global do CorelDRAW"]
    Unlock --> CheckCorel["Verificar se processo CorelDRW.exe ainda está responsivo"]
    CheckCorel --> Idle
```
