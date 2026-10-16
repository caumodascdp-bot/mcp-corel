# Contratos de Integração e Schemas — MCP Corel

Este diretório armazena os contratos técnicos fundamentais do sistema **MCP Corel**, incluindo a definição de ferramentas do protocolo MCP, a máquina de estados de tarefas, códigos de erro padronizados e schemas JSON formais para briefings de arte e manifestos de artefatos.

---

## 1. Estrutura dos Contratos

| Arquivo | Descrição |
| :--- | :--- |
| **`contracts/mcp-tools.md`** | Especificação detalhada de todas as 16 ferramentas propostas para o servidor MCP, com schemas de entrada/saída, efeitos colaterais, idempotência e testes. |
| **`contracts/job-state-machine.md`** | Definição formal dos estados das tarefas (`queued`, `running`, `needs_approval`, `succeeded`, `failed`, `cancelled`), transições permitidas e recuperação após desligamento. |
| **`contracts/artwork-brief.schema.json`** | JSON Schema (Draft-07) formal para validação de briefings estruturados de arte gráfica. |
| **`contracts/artifact-manifest.schema.json`** | JSON Schema (Draft-07) formal para o manifesto de artefatos e arquivos produzidos por cada tarefa. |
| **`contracts/error-codes.md`** | Tabela canônica de códigos de erro padronizados retornados pelas ferramentas e pela API. |

---

## 2. Princípios de Governança dos Contratos

1. **Estado Planejado:** Todas as ferramentas MCP documentadas estão categorizadas como `planned` (planejadas), uma vez que nenhuma implementação em código funcional foi realizada na Fase 0.
2. **Validação Rígida:** Todos os arquivos `.schema.json` devem ser estritamente válidos de acordo com a especificação JSON Schema Draft-07.
3. **Imutabilidade de Contratos:** Alterações em contratos de ferramentas públicas exigem incremento de versão semântica e revisão arquitetural prévia.
