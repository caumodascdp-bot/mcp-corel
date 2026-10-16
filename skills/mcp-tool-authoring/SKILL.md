---
name: mcp-tool-authoring
description: Metodologia e padrões para especificação, documentação e implementação de novas ferramentas MCP no ecossistema Corel.
---

# Procedimento Operacional: Criação de Ferramentas MCP

## 1. Objetivo
Padronizar a criação e o registro de novas ferramentas (*tools*) no servidor Model Context Protocol (MCP), garantindo schemas rigorosos, idempotência documentada, mensagens de erro limpas e compatibilidade entre múltiplos clientes de IA.

---

## 2. Quando Usar
- Ao expandir as capacidades de automação do CorelDRAW com novas operações gráficas.
- Ao revisar ou atualizar contratos existentes em `contracts/mcp-tools.md`.

---

## 3. Entradas Necessárias
- Nome canônico da ferramenta no padrão de namespace (ex: `corel.nome_da_acao`).
- Especificação dos parâmetros de entrada e de saída em JSON Schema.
- Definição dos efeitos colaterais e comportamento em caso de falha.

---

## 4. Procedimento Passo a Passo
1. **Definir o Namespace e o Nome:**
   - Utilizar sempre o prefixo `corel.` seguido de verbo no infinitivo ou substantivo em inglês simples (ex: `corel.create_shape`, `corel.align_objects`).
2. **Definir o Schema de Entrada (Input Schema):**
   - Especificar todas as propriedades com tipo estrito (`string`, `number`, `boolean`, `array`, `object`).
   - Marcar campos essenciais em `required`.
   - Adicionar descrições claras para que a IA entenda a finalidade de cada parâmetro.
3. **Definir o Schema de Saída (Output Schema):**
   - Retornar sempre identificadores claros (`job_id`, `document_id`, `shape_id`) ou caminhos relativos de arquivos gerados.
4. **Documentar Efeitos Colaterais e Idempotência:**
   - Declarar explicitamente se a chamada altera o documento ativo ou se apenas lê informações.
5. **Implementar Validação com Pydantic:**
   - Criar modelo de dados tipado na camada de servidor (`src/mcp_corel/mcp/tools/`).
6. **Integrar ao Catálogo:**
   - Atualizar a documentação técnica em `contracts/mcp-tools.md`.

---

## 5. Ferramentas Permitidas
- Biblioteca oficial `mcp` em Python.
- Pydantic v2 para validação automática de dados.

---

## 6. Saídas Esperadas
- Ferramenta documentada em `contracts/mcp-tools.md` com testes unitários em `tests/unit/test_tools.py`.

---

## 7. Validações
- O schema da ferramenta é 100% compatível com a especificação JSON Schema Draft-07.
- A ferramenta responde corretamente ao comando `tools/list` do MCP.

---

## 8. Falhas Comuns e Ações Corretivas
- **Falha:** Ferramenta retornando texto longo desestruturado em vez de JSON limpo.
  - *Ação:* Encapsular a resposta em modelo Pydantic com serialização JSON garantida.
- **Falha:** Falha interna retornando código 500 sem detalhes.
  - *Ação:* Retornar dicionário estruturado com código de erro amigável em `isError: true`.

---

## 9. Exemplo de Contrato de Ferramenta
```json
{
  "name": "corel.create_shape",
  "description": "Cria uma nova forma geométrica vetorial no documento ativo do CorelDRAW.",
  "parameters": {
    "type": "object",
    "properties": {
      "shape_type": {
        "type": "string",
        "enum": ["rectangle", "ellipse", "line"]
      },
      "x_mm": { "type": "number", "description": "Posição horizontal em mm" },
      "y_mm": { "type": "number", "description": "Posição vertical em mm" },
      "width_mm": { "type": "number", "description": "Largura em mm" },
      "height_mm": { "type": "number", "description": "Altura em mm" }
    },
    "required": ["shape_type", "x_mm", "y_mm", "width_mm", "height_mm"]
  }
}
```

---

## 10. Critérios de Sucesso
- Ferramenta registrada, visível pelo cliente MCP e com suíte de testes de schema aprovada.
