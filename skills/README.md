# Catálogo de Habilidades Operacionais (Skills) — MCP Corel

Este diretório contém os procedimentos padronizados de trabalho (*Standard Operating Procedures - SOPs*) utilizados pelos agentes de IA e operadores do projeto **MCP Corel**.

Cada skill é uma instrução operacional estruturada em formato Markdown com cabeçalho YAML frontmatter, reutilizável e independente de linguagem de programação específica.

---

## 1. Lista de Skills Disponíveis

| Habilidade (Skill) | Caminho | Finalidade Operacional |
| :--- | :--- | :--- |
| **artwork-brief** | `skills/artwork-brief/SKILL.md` | Como transformar um pedido informal em briefing JSON estruturado e validado. |
| **vector-layout** | `skills/vector-layout/SKILL.md` | Metodologia para cálculo de coordenadas, grids modulares e camadas. |
| **corel-automation**| `skills/corel-automation/SKILL.md`| Execução de chamadas COM controladas, supressão de modais e watchdog. |
| **asset-management**| `skills/asset-management/SKILL.md`| Gerenciamento seguro de imagens externas, formatos e limites de disco. |
| **visual-qa** | `skills/visual-qa/SKILL.md` | Roteiro de conferência e auditoria de imagens de prévia (PNG). |
| **print-preflight** | `skills/print-preflight/SKILL.md` | Validação técnica de requisitos gráficos (sangria, CMYK, curvas e DPI). |
| **telegram-operations**| `skills/telegram-operations/SKILL.md`| Procedimentos para interações remotas, notificações e aprovações. |
| **mcp-tool-authoring**| `skills/mcp-tool-authoring/SKILL.md`| Como projetar e documentar novos contratos de ferramentas MCP. |
| **security-review** | `skills/security-review/SKILL.md` | Procedimento de auditoria contra path traversal, injeção e vazamentos. |
| **release-checklist**| `skills/release-checklist/SKILL.md`| Roteiro de passagem de fases e verificação de critérios de liberação. |

---

## 2. Padrão Estrutural de uma Skill

Cada arquivo `SKILL.md` obedece rigorosamente aos seguintes campos:
1. **Frontmatter YAML:** `name` e `description`.
2. **Objetivo:** O que o procedimento realiza.
3. **Quando Usar:** Critérios de ativação da skill.
4. **Entradas Necessárias:** Parâmetros mínimos para início.
5. **Procedimento Passo a Passo:** Fases operacionais ordenadas.
6. **Ferramentas Permitidas:** Utilitários e comandos autorizados.
7. **Saídas Esperadas:** Artefatos gerados.
8. **Validações:** Como aferir a qualidade da entrega.
9. **Falhas Comuns:** O que pode dar errado e ação corretiva.
10. **Exemplos:** Casos reais de uso.
11. **Critérios de Sucesso:** Condição final de conclusão.
