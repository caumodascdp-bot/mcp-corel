# Organização de Espaços de Trabalho (Spaces) — MCP Corel

Este diretório estabelece uma organização conceitual e documental das frentes de trabalho do projeto **MCP Corel**.

> [!NOTE]
> **Status de Integração com o AntiGravity:**  
> O suporte a "Spaces" como recurso nativo depende da versão e das extensões instaladas no ambiente AntiGravity. Para garantir portabilidade universal e clareza documental sem presunções não verificadas, esta pasta funciona atualmente como uma **organização documental canônica do repositório**. Caso o cliente AntiGravity do usuário suporte espaços de trabalho ou regras por contexto, os arquivos aqui descritos podem ser mapeados diretamente como referências de contexto para as respectivas áreas.

---

## 1. Espaços de Trabalho Documentados

| Espaço de Trabalho | Arquivo de Definição | Finalidade Principal | Agentes Chave |
| :--- | :--- | :--- | :--- |
| **Produto e Arquitetura** | `spaces/product-and-architecture.md` | Requisitos, escopo, decisões de arquitetura (ADRs) e fluxos gerais de dados. | Product Architect, Art Director, Orchestrator |
| **Implementação** | `spaces/implementation.md` | Desenvolvimento do servidor MCP, adaptador COM do CorelDRAW e worker Telegram. | MCP Engineer, Corel Automation, Layout Engineer |
| **Qualidade e Segurança** | `spaces/quality-and-security.md` | Testes automatizados, inspeção visual, pré-impressão e auditoria de segurança. | Security Reviewer, Test Engineer, Visual QA, Prepress |
| **Release e Operação** | `spaces/release-operations.md` | Implantação, monitoramento, runbooks, resolução de incidentes e checklist de versões. | Telegram Operator, Orchestrator, DevOps |

---

## 2. Como Utilizar Estes Documentos

Ao iniciar uma sessão de trabalho focada em uma área específica, o desenvolvedor ou o agente de IA deve carregar o documento correspondente da pasta `spaces/` para obter de imediato os links essenciais, regras vigentes e arquivos prioritários para aquela tarefa.
