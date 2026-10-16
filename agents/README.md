# Catálogo de Agentes Especializados — MCP Corel

Este diretório define os 11 agentes autônomos e cooperativos que compõem o sistema **MCP Corel**.

Nenhum agente possui autoridade unilateral para aprovar as próprias alterações críticas em código ou regras de segurança. O fluxo de trabalho é orquestrado de forma colaborativa com separação rigorosa de responsabilidades.

---

## 1. Quadro Geral de Agentes

| Agente | Diretório | Papel Principal | Dependências Imediatas |
| :--- | :--- | :--- | :--- |
| **Orchestrator** | `agents/orchestrator/` | Orquestração do fluxo, controle de fila e prevenção de loops. | Todos os agentes |
| **Product Architect** | `agents/product-architect/` | Arquitetura técnica, requisitos, ADRs e governança de software. | Orchestrator, Security Reviewer |
| **Art Director** | `agents/art-director/` | Concepção artística, estilo, hierarquia visual e textos literais. | Orchestrator, Layout Engineer |
| **Layout Engineer** | `agents/layout-engineer/` | Conversão do conceito em coordenadas matemáticas (mm) e formas. | Art Director, Corel Automation |
| **Corel Automation** | `agents/corel-automation/` | Automação COM controlada no CorelDRAW 2023. | Layout Engineer, Prepress |
| **Visual QA** | `agents/visual-qa/` | Inspeção visual minuciosa das imagens de prévia (PNG). | Corel Automation, Art Director |
| **Prepress** | `agents/prepress/` | Conformidade técnica de gráfica (sangrias, margens, CMYK, DPI). | Layout Engineer, Visual QA |
| **Telegram Operator** | `agents/telegram-operator/` | Gestão de comandos remotos, permissões e entrega no chat. | Orchestrator, Visual QA |
| **MCP Engineer** | `agents/mcp-engineer/` | Especificação de contratos de ferramentas e integração de clientes. | Product Architect, Corel Automation |
| **Security Reviewer** | `agents/security-reviewer/` | Auditoria de segurança, isolamento de diretórios e ameaças. | Todos os agentes |
| **Test Engineer** | `agents/test-engineer/` | Desenvolvimento de suítes de testes unitários, integração e E2E. | MCP Engineer, Corel Automation |

---

## 2. Regra de Separação de Poderes

- **Código x Segurança:** O agente que implementa o código (**MCP Engineer** ou **Corel Automation**) não pode aprovar os relatórios de segurança; a aprovação cabe ao **Security Reviewer**.
- **Arte x Qualidade:** O agente que planeja o leiaute (**Layout Engineer**) não pode aprovar a entrega visual; a aprovação exige parecer do **Visual QA** e do **Prepress**.
- **Aprovação Final:** Nenhuma arte final é enviada para gráfica sem o portão de aprovação do operador humano registrado no estado da tarefa.
