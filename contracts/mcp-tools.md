# Contratos das Ferramentas MCP — MCP Corel

> [!IMPORTANT]
> **ESTADO DE TODAS AS FERRAMENTAS: `planned` (Planejadas)**  
> Nenhuma das ferramentas abaixo possui código funcional implementado na Fase 0. Elas representam a especificação de interface que guiará a implementação nas Fases 1, 2 e 3.

---

## 1. Visão Geral das Ferramentas

| Ferramenta | Categoria | Finalidade | Efeito Colateral | Idempotente? |
| :--- | :--- | :--- | :--- | :--- |
| `corel.get_status` | Diagnóstico | Retorna saúde da estação e da fila | Leitura pura | Sim |
| `corel.run_diagnostics` | Diagnóstico | Executa diagnóstico profundo de COM e licença | Leitura pura | Sim |
| `corel.list_documents` | Documentos | Lista documentos abertos no CorelDRAW | Leitura pura | Sim |
| `corel.create_document` | Documentos | Cria novo documento vetorial | Cria documento | Não |
| `corel.get_document_info` | Documentos | Retorna metadados detalhados da página ativa | Leitura pura | Sim |
| `corel.create_shape` | Vetorial | Insere forma geométrica (retângulo, elipse) | Modifica documento | Não |
| `corel.add_text` | Vetorial | Adiciona texto artístico ou de parágrafo | Modifica documento | Não |
| `corel.import_asset` | Ativos | Importa imagem externa da pasta segura | Modifica documento | Não |
| `corel.align_objects` | Vetorial | Alinha ou distribui objetos selecionados | Modifica documento | Sim |
| `corel.preview` | Renderização | Exporta prévia visual PNG em 150 DPI | Grava imagem | Sim |
| `corel.validate_artwork` | Qualidade | Executa inspeção de pré-impressão técnica | Leitura pura | Sim |
| `corel.save_document` | Documentos | Salva documento ativo em formato `.cdr` | Grava arquivo | Sim |
| `corel.export_document` | Exportação | Exporta arte fechada para PDF, EPS, SVG | Grava arquivo | Sim |
| `corel.get_job_status` | Fila | Consulta o estado de uma tarefa | Leitura pura | Sim |
| `corel.cancel_job` | Fila | Cancela tarefa pendente na fila | Altera fila | Sim |
| `corel.get_job_artifacts`| Fila | Retorna caminhos e hashes dos arquivos gerados | Leitura pura | Sim |

---

## 2. Especificação Detalhada por Ferramenta

### 2.1 `corel.get_status`
- **Estado:** `planned`
- **Descrição:** Consulta o status operacional do servidor MCP, a disponibilidade do processo do CorelDRAW e o tamanho da fila.
- **Input Schema:**
  ```json
  {
    "type": "object",
    "properties": {},
    "additionalProperties": false
  }
  ```
- **Output Schema:**
  ```json
  {
    "type": "object",
    "properties": {
      "server_version": { "type": "string" },
      "corel_process_active": { "type": "boolean" },
      "queue_size": { "type": "integer" },
      "active_job_id": { "type": ["integer", "null"] }
    },
    "required": ["server_version", "corel_process_active", "queue_size"]
  }
  ```
- **Erros:** `ERR_INTERNAL_SERVER_ERROR`.
- **Efeitos Colaterais:** Nenhum (leitura pura).
- **Permissões:** Livre para clientes MCP autenticados.
- **Idempotência:** Totalmente idempotente.
- **Testes:** `tests/unit/test_status.py`.

---

### 2.2 `corel.run_diagnostics`
- **Estado:** `planned`
- **Descrição:** Executa checagem profunda das chaves COM do Windows, versão do executável e formatos suportados.
- **Input Schema:**
  ```json
  {
    "type": "object",
    "properties": {
      "force_com_probe": { "type": "boolean", "default": false }
    },
    "additionalProperties": false
  }
  ```
- **Output Schema:**
  ```json
  {
    "type": "object",
    "properties": {
      "com_progid": { "type": "string" },
      "executable_path": { "type": "string" },
      "architecture": { "type": "string" },
      "vba_available": { "type": "boolean" },
      "supported_filters": { "type": "array", "items": { "type": "string" } }
    },
    "required": ["com_progid", "executable_path", "architecture", "vba_available"]
  }
  ```
- **Erros:** `ERR_COREL_NOT_INSTALLED`, `ERR_COREL_COM_INIT_FAILED`.
- **Efeitos Colaterais:** Pode instanciar brevemente o processo COM.
- **Permissões:** Restrito a clientes MCP locais.
- **Idempotência:** Idempotente.
- **Testes:** `tests/unit/test_diagnostics.py`.

---

### 2.3 `corel.list_documents`
- **Estado:** `planned`
- **Descrição:** Lista todos os documentos atualmente abertos na instância ativa do CorelDRAW.
- **Input Schema:**
  ```json
  { "type": "object", "properties": {}, "additionalProperties": false }
  ```
- **Output Schema:**
  ```json
  {
    "type": "object",
    "properties": {
      "documents": {
        "type": "array",
        "items": {
          "type": "object",
          "properties": {
            "document_id": { "type": "integer" },
            "name": { "type": "string" },
            "path": { "type": "string" },
            "dirty": { "type": "boolean" },
            "pages_count": { "type": "integer" }
          },
          "required": ["document_id", "name", "dirty", "pages_count"]
        }
      }
    },
    "required": ["documents"]
  }
  ```
- **Erros:** `ERR_COREL_COM_TIMEOUT`.
- **Efeitos Colaterais:** Leitura pura.
- **Permissões:** Leitura.
- **Idempotência:** Idempotente.
- **Testes:** `tests/unit/test_documents.py`.

---

### 2.4 `corel.create_document`
- **Estado:** `planned`
- **Descrição:** Cria um novo documento vetorial no CorelDRAW com dimensões e espaço de cor definidos.
- **Input Schema:**
  ```json
  {
    "type": "object",
    "properties": {
      "name": { "type": "string" },
      "width_mm": { "type": "number", "minimum": 1.0 },
      "height_mm": { "type": "number", "minimum": 1.0 },
      "bleed_mm": { "type": "number", "default": 0.0 },
      "color_mode": { "type": "string", "enum": ["CMYK", "RGB"] }
    },
    "required": ["name", "width_mm", "height_mm", "color_mode"],
    "additionalProperties": false
  }
  ```
- **Output Schema:**
  ```json
  {
    "type": "object",
    "properties": {
      "job_id": { "type": "integer" },
      "document_id": { "type": "integer" },
      "name": { "type": "string" },
      "status": { "type": "string" }
    },
    "required": ["job_id", "status"]
  }
  ```
- **Erros:** `ERR_VALIDATION_INVALID_DIMENSIONS`, `ERR_COREL_COM_TIMEOUT`.
- **Efeitos Colaterais:** Cria um documento na aplicação.
- **Permissões:** Requer enfileiramento serializado.
- **Idempotência:** Não (cada chamada cria um novo documento).
- **Testes:** `tests/unit/test_document_create.py`.

---

### 2.5 `corel.get_document_info`
- **Estado:** `planned`
- **Descrição:** Retorna metadados detalhados sobre o documento ativo ou especificado (dimensões, camadas, fontes, contagem de formas).
- **Input Schema:**
  ```json
  {
    "type": "object",
    "properties": {
      "document_id": { "type": "integer" }
    },
    "additionalProperties": false
  }
  ```
- **Output Schema:**
  ```json
  {
    "type": "object",
    "properties": {
      "document_id": { "type": "integer" },
      "name": { "type": "string" },
      "width_mm": { "type": "number" },
      "height_mm": { "type": "number" },
      "bleed_mm": { "type": "number" },
      "color_mode": { "type": "string" },
      "shapes_count": { "type": "integer" },
      "layers": { "type": "array", "items": { "type": "string" } }
    },
    "required": ["document_id", "width_mm", "height_mm", "shapes_count"]
  }
  ```
- **Erros:** `ERR_COREL_DOCUMENT_NOT_FOUND`.
- **Efeitos Colaterais:** Leitura pura.
- **Idempotência:** Idempotente.
- **Testes:** `tests/unit/test_document_info.py`.

---

### 2.6 `corel.create_shape`
- **Estado:** `planned`
- **Descrição:** Cria uma forma geométrica (retângulo, elipse ou linha) com coordenadas, espessura de contorno e preenchimento.
- **Input Schema:**
  ```json
  {
    "type": "object",
    "properties": {
      "shape_type": { "type": "string", "enum": ["rectangle", "ellipse", "line"] },
      "x_mm": { "type": "number" },
      "y_mm": { "type": "number" },
      "width_mm": { "type": "number", "minimum": 0.1 },
      "height_mm": { "type": "number", "minimum": 0.1 },
      "fill_color": {
        "type": "object",
        "properties": {
          "mode": { "type": "string", "enum": ["CMYK", "RGB"] },
          "values": { "type": "array", "items": { "type": "number" } }
        },
        "required": ["mode", "values"]
      },
      "outline_width_pt": { "type": "number", "default": 0.5 }
    },
    "required": ["shape_type", "x_mm", "y_mm", "width_mm", "height_mm"],
    "additionalProperties": false
  }
  ```
- **Output Schema:**
  ```json
  {
    "type": "object",
    "properties": {
      "job_id": { "type": "integer" },
      "shape_id": { "type": "integer" },
      "status": { "type": "string" }
    },
    "required": ["job_id", "status"]
  }
  ```
- **Erros:** `ERR_COREL_COM_TIMEOUT`, `ERR_VALIDATION_COLOR_INVALID`.
- **Efeitos Colaterais:** Modifica o documento ativo.
- **Idempotência:** Não.
- **Testes:** `tests/unit/test_shapes.py`.

---

### 2.7 `corel.add_text`
- **Estado:** `planned`
- **Descrição:** Adiciona texto artístico ou texto de parágrafo formatado no documento.
- **Input Schema:**
  ```json
  {
    "type": "object",
    "properties": {
      "text": { "type": "string" },
      "text_type": { "type": "string", "enum": ["artistic", "paragraph"], "default": "artistic" },
      "font_family": { "type": "string", "default": "Arial" },
      "size_pt": { "type": "number", "minimum": 4.0 },
      "x_mm": { "type": "number" },
      "y_mm": { "type": "number" },
      "width_mm": { "type": "number" },
      "height_mm": { "type": "number" },
      "align": { "type": "string", "enum": ["left", "center", "right", "justify"], "default": "left" }
    },
    "required": ["text", "x_mm", "y_mm", "size_pt"],
    "additionalProperties": false
  }
  ```
- **Output Schema:**
  ```json
  {
    "type": "object",
    "properties": {
      "job_id": { "type": "integer" },
      "text_shape_id": { "type": "integer" },
      "status": { "type": "string" }
    },
    "required": ["job_id", "status"]
  }
  ```
- **Erros:** `ERR_FONT_NOT_FOUND`, `ERR_COREL_COM_TIMEOUT`.
- **Efeitos Colaterais:** Insere objeto de texto.
- **Idempotência:** Não.
- **Testes:** `tests/unit/test_text.py`.

---

### 2.8 `corel.import_asset`
- **Estado:** `planned`
- **Descrição:** Importa um arquivo gráfico externo (PNG, JPG, SVG) contido na pasta autorizada para a página ativa.
- **Input Schema:**
  ```json
  {
    "type": "object",
    "properties": {
      "file_path": { "type": "string" },
      "x_mm": { "type": "number" },
      "y_mm": { "type": "number" },
      "width_mm": { "type": "number" },
      "height_mm": { "type": "number" },
      "maintain_aspect_ratio": { "type": "boolean", "default": true }
    },
    "required": ["file_path", "x_mm", "y_mm"],
    "additionalProperties": false
  }
  ```
- **Output Schema:**
  ```json
  {
    "type": "object",
    "properties": {
      "job_id": { "type": "integer" },
      "imported_shape_id": { "type": "integer" },
      "status": { "type": "string" }
    },
    "required": ["job_id", "status"]
  }
  ```
- **Erros:** `ERR_SECURITY_PATH_OUT_OF_BOUNDS`, `ERR_ASSET_CORRUPTED`.
- **Efeitos Colaterais:** Insere bitmap ou vetor na página.
- **Idempotência:** Não.
- **Testes:** `tests/security/test_import_security.py`.

---

### 2.9 `corel.align_objects`
- **Estado:** `planned`
- **Descrição:** Alinha ou distribui uma lista de objetos selecionados em relação à página ou ao objeto líder.
- **Input Schema:**
  ```json
  {
    "type": "object",
    "properties": {
      "shape_ids": { "type": "array", "items": { "type": "integer" }, "minItems": 1 },
      "align_type": { "type": "string", "enum": ["center_horizontal", "center_vertical", "left", "right", "top", "bottom"] },
      "align_relative_to": { "type": "string", "enum": ["page", "selection"], "default": "page" }
    },
    "required": ["shape_ids", "align_type"],
    "additionalProperties": false
  }
  ```
- **Output Schema:**
  ```json
  {
    "type": "object",
    "properties": {
      "job_id": { "type": "integer" },
      "aligned_count": { "type": "integer" },
      "status": { "type": "string" }
    },
    "required": ["job_id", "aligned_count", "status"]
  }
  ```
- **Erros:** `ERR_SHAPE_NOT_FOUND`.
- **Efeitos Colaterais:** Modifica posições de formas existentes.
- **Idempotência:** Sim.
- **Testes:** `tests/unit/test_alignment.py`.

---

### 2.10 `corel.preview`
- **Estado:** `planned`
- **Descrição:** Exporta uma imagem de prévia visual rápida (PNG) da página ativa para inspeção visual do agente ou usuário.
- **Input Schema:**
  ```json
  {
    "type": "object",
    "properties": {
      "dpi": { "type": "integer", "default": 150, "minimum": 72, "maximum": 300 },
      "page_number": { "type": "integer", "default": 1 }
    },
    "additionalProperties": false
  }
  ```
- **Output Schema:**
  ```json
  {
    "type": "object",
    "properties": {
      "job_id": { "type": "integer" },
      "preview_path": { "type": "string" },
      "preview_url_relative": { "type": "string" },
      "width_px": { "type": "integer" },
      "height_px": { "type": "integer" }
    },
    "required": ["job_id", "preview_path"]
  }
  ```
- **Erros:** `ERR_EXPORT_FILTER_FAILED`.
- **Efeitos Colaterais:** Grava imagem em `data/previews/`.
- **Idempotência:** Sim.
- **Testes:** `tests/unit/test_preview.py`.

---

### 2.11 `corel.validate_artwork`
- **Estado:** `planned`
- **Descrição:** Executa auditoria técnica de pré-impressão checando sangrias, fontes não convertidas e resolução de imagens.
- **Input Schema:**
  ```json
  {
    "type": "object",
    "properties": {
      "target_profile": { "type": "string", "default": "CMYK_PRINT" }
    },
    "additionalProperties": false
  }
  ```
- **Output Schema:**
  ```json
  {
    "type": "object",
    "properties": {
      "is_valid": { "type": "boolean" },
      "issues": {
        "type": "array",
        "items": {
          "type": "object",
          "properties": {
            "code": { "type": "string" },
            "message": { "type": "string" },
            "severity": { "type": "string", "enum": ["warning", "error"] }
          },
          "required": ["code", "message", "severity"]
        }
      }
    },
    "required": ["is_valid", "issues"]
  }
  ```
- **Erros:** `ERR_COREL_COM_TIMEOUT`.
- **Efeitos Colaterais:** Leitura pura.
- **Idempotência:** Sim.
- **Testes:** `tests/unit/test_validate_artwork.py`.

---

### 2.12 `corel.save_document`
- **Estado:** `planned`
- **Descrição:** Salva o documento ativo no formato mestre `.cdr` dentro do diretório de artefatos.
- **Input Schema:**
  ```json
  {
    "type": "object",
    "properties": {
      "file_name": { "type": "string" },
      "overwrite": { "type": "boolean", "default": false }
    },
    "required": ["file_name"],
    "additionalProperties": false
  }
  ```
- **Output Schema:**
  ```json
  {
    "type": "object",
    "properties": {
      "job_id": { "type": "integer" },
      "saved_path": { "type": "string" },
      "size_bytes": { "type": "integer" },
      "sha256": { "type": "string" }
    },
    "required": ["job_id", "saved_path", "sha256"]
  }
  ```
- **Erros:** `ERR_FILE_ALREADY_EXISTS`, `ERR_SECURITY_PATH_OUT_OF_BOUNDS`.
- **Efeitos Colaterais:** Grava arquivo `.cdr` em disco.
- **Idempotência:** Sim (com flag overwrite).
- **Testes:** `tests/unit/test_save_document.py`.

---

### 2.13 `corel.export_document`
- **Estado:** `planned`
- **Descrição:** Exporta a arte final fechada para PDF gráfico, EPS ou SVG.
- **Input Schema:**
  ```json
  {
    "type": "object",
    "properties": {
      "format": { "type": "string", "enum": ["PDF", "EPS", "SVG", "PNG"] },
      "file_name": { "type": "string" },
      "preset": { "type": "string", "enum": ["prepress_cmyk", "web_rgb", "standard"], "default": "prepress_cmyk" },
      "include_crop_marks": { "type": "boolean", "default": true }
    },
    "required": ["format", "file_name"],
    "additionalProperties": false
  }
  ```
- **Output Schema:**
  ```json
  {
    "type": "object",
    "properties": {
      "job_id": { "type": "integer" },
      "exported_path": { "type": "string" },
      "format": { "type": "string" },
      "sha256": { "type": "string" }
    },
    "required": ["job_id", "exported_path", "sha256"]
  }
  ```
- **Erros:** `ERR_EXPORT_FILTER_FAILED`, `ERR_SECURITY_PATH_OUT_OF_BOUNDS`.
- **Efeitos Colaterais:** Grava arquivo de entrega em disco.
- **Idempotência:** Sim.
- **Testes:** `tests/unit/test_export_document.py`.

---

### 2.14 `corel.get_job_status`
- **Estado:** `planned`
- **Descrição:** Consulta o andamento, estado atual e mensagem de erro de uma tarefa específica.
- **Input Schema:**
  ```json
  {
    "type": "object",
    "properties": {
      "job_id": { "type": "integer" }
    },
    "required": ["job_id"],
    "additionalProperties": false
  }
  ```
- **Output Schema:**
  ```json
  {
    "type": "object",
    "properties": {
      "job_id": { "type": "integer" },
      "status": { "type": "string", "enum": ["queued", "running", "needs_approval", "succeeded", "failed", "cancelled"] },
      "progress_percent": { "type": "integer" },
      "error_code": { "type": ["string", "null"] },
      "error_message": { "type": ["string", "null"] }
    },
    "required": ["job_id", "status", "progress_percent"]
  }
  ```
- **Erros:** `ERR_JOB_NOT_FOUND`.
- **Efeitos Colaterais:** Leitura pura.
- **Idempotência:** Idempotente.
- **Testes:** `tests/unit/test_job_status.py`.

---

### 2.15 `corel.cancel_job`
- **Estado:** `planned`
- **Descrição:** Cancela uma tarefa que esteja aguardando execução na fila.
- **Input Schema:**
  ```json
  {
    "type": "object",
    "properties": {
      "job_id": { "type": "integer" },
      "reason": { "type": "string" }
    },
    "required": ["job_id"],
    "additionalProperties": false
  }
  ```
- **Output Schema:**
  ```json
  {
    "type": "object",
    "properties": {
      "job_id": { "type": "integer" },
      "status": { "type": "string" },
      "cancelled": { "type": "boolean" }
    },
    "required": ["job_id", "status", "cancelled"]
  }
  ```
- **Erros:** `ERR_JOB_NOT_FOUND`, `ERR_JOB_ALREADY_RUNNING`.
- **Efeitos Colaterais:** Atualiza estado no SQLite.
- **Idempotência:** Sim.
- **Testes:** `tests/unit/test_job_cancel.py`.

---

### 2.16 `corel.get_job_artifacts`
- **Estado:** `planned`
- **Descrição:** Retorna a lista de todos os arquivos gerados por uma tarefa concluída com metadados e hashes.
- **Input Schema:**
  ```json
  {
    "type": "object",
    "properties": {
      "job_id": { "type": "integer" }
    },
    "required": ["job_id"],
    "additionalProperties": false
  }
  ```
- **Output Schema:**
  ```json
  {
    "type": "object",
    "properties": {
      "job_id": { "type": "integer" },
      "manifest": {
        "type": "array",
        "items": {
          "type": "object",
          "properties": {
            "type": { "type": "string", "enum": ["master_cdr", "preview_png", "export_pdf", "log"] },
            "path": { "type": "string" },
            "size_bytes": { "type": "integer" },
            "sha256": { "type": "string" }
          },
          "required": ["type", "path", "sha256"]
        }
      }
    },
    "required": ["job_id", "manifest"]
  }
  ```
- **Erros:** `ERR_JOB_NOT_FOUND`.
- **Efeitos Colaterais:** Leitura pura.
- **Idempotência:** Idempotente.
- **Testes:** `tests/unit/test_job_artifacts.py`.
