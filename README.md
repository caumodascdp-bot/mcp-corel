# MCP Corel — Automação de CorelDRAW via IA

> **AVISO IMPORTANTE: FASE 0 (ARQUITETURA E ESTRUTURAÇÃO)**  
> Este repositório está atualmente na **Fase 0**. Toda a estrutura de pastas, contratos, agentes, skills e documentação técnica foi preparada de forma rigorosa. **Nenhum código funcional de automação, macro ou servidor foi implementado ainda**, e a integração real com o CorelDRAW 2023 na máquina Windows 10 **NÃO foi confirmada nem testada**.

---

## 1. Visão Geral do Produto

O **MCP Corel** é um servidor compatível com o protocolo aberto **Model Context Protocol (MCP)** projetado para permitir que agentes de inteligência artificial (como AntiGravity, Codex, Claude Code e outros clientes MCP) controlem de forma segura e determinística uma instalação local do **CorelDRAW 2023** em ambiente Windows 10.

O fluxo de trabalho permite:
1. Receber briefings de design em linguagem natural ou estruturados via JSON.
2. Planejar o leiaute vetorial (formas, textos, guias, camadas e cores).
3. Comandar o CorelDRAW via fila de execução serializada.
4. Gerar prévias visuais em alta e baixa resolução (PNG/JPEG) e coletar feedback.
5. Salvar o arquivo mestre (.CDR) e exportar formatos finais para produção ou impressão (PDF, EPS, SVG, PNG).
6. Permitir acompanhamento e aprovação remota via bot do Telegram (operando em modo seguro de saída por *long polling*).

---

## 2. Estado Atual do Projeto

| Componente | Estado Atual | Evidência / Observação |
| :--- | :--- | :--- |
| **Documentação e Arquitetura** | `verified` | Fase 0 concluída com contratos, schemas e agentes definidos. |
| **Contratos JSON e Schemas** | `verified` | Schemas de arte e manifesto validados sintaticamente. |
| **Integração COM/VBA CorelDRAW** | `unknown` | Precisa de diagnóstico na máquina Windows 10 física. |
| **Servidor MCP (stdio)** | `planned` | Aguardando autorização para implementação (Fase 1). |
| **Fila de Jobs e SQLite** | `planned` | Estrutura definida, aguardando implementação (Fase 2). |
| **Bot do Telegram** | `planned` | Arquitetura de long polling aprovada, aguardando Fase 4. |

---

## 3. Diagrama Geral de Arquitetura

```mermaid
flowchart TD
    subgraph Clientes["Clientes MCP Autorizados"]
        AG["AntiGravity IDE"]
        CC["Claude Code"]
        CX["Codex"]
    end

    subgraph CanalRemoto["Acesso Remoto Seguro"]
        TG["Operador via Telegram<br/>(Celular / Web)"]
        TGAPI["Telegram Bot API<br/>(Nuvem)"]
    end

    subgraph HostWindows["Máquina Local Windows 10 (Área Segura)"]
        subgraph CamadaEntrada["Camada de Entrada"]
            STDIO["MCP Server<br/>(stdio)"]
            TGBOT["Telegram Worker<br/>(Long Polling de Saída)"]
        end

        subgraph Nucleo["Núcleo de Orquestração & Segurança"]
            SEC["Guarda de Segurança<br/>(Allowlist / Path Traversal)"]
            QUEUE["Fila de Tarefas & Estado<br/>(SQLite Local)"]
        end

        subgraph AdaptadorCorel["Camada de Automação"]
            ADAPT["Adaptador CorelDRAW<br/>(Driver COM / pywin32)"]
            APP["CorelDRAW 2023<br/>(Executável Local)"]
        end

        subgraph ArmazenamentoLocal["Armazenamento Local (data/)"]
            JOBS["Jobs & Metadados"]
            PREVIEWS["Prévias (PNG/JPG)"]
            ARTIFACTS["Artefatos (.CDR / .PDF)"]
        end
    end

    AG -->|stdio| STDIO
    CC -->|stdio| STDIO
    CX -->|stdio| STDIO

    TG <--> TGAPI
    TGBOT <-->|HTTPS Long Polling<br/>(Apenas Conexão de Saída)| TGAPI

    STDIO --> SEC
    TGBOT --> SEC
    SEC --> QUEUE
    QUEUE --> ADAPT
    ADAPT --> APP

    ADAPT --> PREVIEWS
    ADAPT --> ARTIFACTS
    QUEUE --> JOBS
```

---

## 4. Requisitos do Ambiente

### Máquina de Produção / Alvo
- **Sistema Operacional:** Windows 10 (64-bit recomendado).
- **Software Gráfico:** CorelDRAW Graphics Suite 2023 instalado e ativado com licença válida.
- **Ambiente de Execução:** Python 3.11 ou superior instalado.
- **Rede:** Acesso à internet apenas para comunicação com a API do Telegram (caso o bot esteja ativado). **Nenhuma porta de entrada deve ser aberta no firewall ou roteador.**

### Ambiente de Desenvolvimento / Planejamento
- Suporte a ambientes Linux / WSL para edição de documentação, validação de schemas JSON e execução de testes unitários que utilizam simulações (*mocks*).

---

## 5. Como Será Feito o Diagnóstico do CorelDRAW

Quando a fase de implementação for autorizada, o primeiro passo prático na máquina Windows 10 será rodar o utilitário de diagnóstico:

```bash
# Executado exclusivamente na máquina Windows com CorelDRAW instalado
python -m mcp_corel.corel.diagnostics
```

O diagnóstico verificará sem alterar documentos:
1. Existência do executável no caminho padrão de 64-bit e 32-bit.
2. Registro do componente COM (`CorelDRAW.Application.24` ou variante da versão 2023).
3. Disponibilidade de interface de automação via COM/VBA.
4. Formatos de exportação nativamente suportados pela instalação.
5. Permissões de escrita e leitura nos diretórios de trabalho `data/`.

---

## 6. Como Rodar os Testes (Quando Implementado)

```bash
# Rodar testes unitários (não requerem CorelDRAW físico)
pytest tests/unit/

# Rodar testes de validação de schemas e contratos
pytest tests/security/

# Rodar testes de integração (exige Windows 10 com CorelDRAW aberto)
pytest tests/integration/ -m live_corel
```

---

## 7. Navegação na Documentação

- [Regras dos Agentes de IA](file:///home/kali/mcp-corel/AGENTS.md)
- [Contexto do Projeto](file:///home/kali/mcp-corel/CONTEXT.md)
- [Especificação de Requisitos](file:///home/kali/mcp-corel/docs/SPEC.md)
- [Arquitetura Detalhada](file:///home/kali/mcp-corel/docs/ARCHITECTURE.md)
- [Fluxos de Dados e Diagramas](file:///home/kali/mcp-corel/docs/DATA_FLOWS.md)
- [Compatibilidade com CorelDRAW](file:///home/kali/mcp-corel/docs/CORELDRAW_COMPATIBILITY.md)
- [Compatibilidade MCP por Cliente](file:///home/kali/mcp-corel/docs/MCP_COMPATIBILITY.md)
- [Operação via Telegram](file:///home/kali/mcp-corel/docs/TELEGRAM.md)
- [Modelo de Ameaças e Segurança](file:///home/kali/mcp-corel/docs/THREAT_MODEL.md)
- [Critérios de Aceite](file:///home/kali/mcp-corel/docs/ACCEPTANCE_CRITERIA.md)
- [Relatório da Fase 0](file:///home/kali/mcp-corel/docs/PHASE-0-REPORT.md)
