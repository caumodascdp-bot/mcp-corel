---
name: asset-management
description: Diretrizes para importação, validação e armazenamento seguro de ativos gráficos externos (imagens, logotipos, fontes).
---

# Procedimento Operacional: Gestão de Ativos Gráficos

## 1. Objetivo
Garantir que todas as imagens, ícones e fontes utilizados na criação das artes sejam importados de forma segura, com validação de formato, integridade e isolamento de caminhos.

---

## 2. Quando Usar
- Sempre que um briefing exigir a inclusão de fotografias ou logotipos externos fornecidos pelo cliente.
- Ao salvar e organizar os arquivos de artefatos finais gerados.

---

## 3. Entradas Necessárias
- Caminho do arquivo de ativo externo ou arquivo enviado pelo Telegram.
- Metadados do arquivo (extensão, tamanho em bytes, dimensões em pixels).

---

## 4. Procedimento Passo a Passo
1. **Verificar a Localização do Arquivo:**
   - Assegurar que o arquivo resida obrigatoriamente dentro de `SAFE_STORAGE_ROOT`.
   - Se o arquivo foi enviado via Telegram, salvar temporariamente em `data/artifacts/uploads/`.
2. **Validar Formato e Extensão:**
   - Aceitar exclusivamente formatos suportados: `.png`, `.jpg`, `.jpeg`, `.svg`, `.eps`.
   - Rejeitar executáveis, scripts ou formatos compactados desconhecidos.
3. **Inspecionar Limites de Tamanho:**
   - Conferir se o arquivo possui menos de 50 MB (limite `MAX_IMPORT_FILE_SIZE_MB`).
4. **Verificar Resolução (DPI):**
   - Para artes de impressão, calcular a resolução efetiva da imagem em relação ao tamanho impresso pretendido (mínimo 300 DPI).
5. **Registrar no Manifesto de Artefatos:**
   - Incluir o caminho do ativo no manifesto do job (`artifact-manifest.schema.json`) com seu hash SHA-256.

---

## 5. Ferramentas Permitidas
- Módulo `src/mcp_corel/security/path_guard.py`.
- Biblioteca Pillow para leitura de metadados e DPI de imagens.

---

## 6. Saídas Esperadas
- Arquivo de ativo posicionado no diretório seguro com hash verificado e pronto para importação via COM.

---

## 7. Validações
- O caminho canônico do arquivo não escapa da raiz segura.
- O arquivo não está corrompido e pode ser decodificado como imagem válida.

---

## 8. Falhas Comuns e Ações Corretivas
- **Falha:** Tentativa de importar imagem com caminho absoluto fora da pasta segura (ex: `C:\Imagens\logo.png`).
  - *Ação:* Bloquear com `ERR_SECURITY_PATH_OUT_OF_BOUNDS` e solicitar que o arquivo seja movido para `data/artifacts/`.
- **Falha:** Imagem com resolução insuficiente (ex: 72 DPI para material de impressão).
  - *Ação:* Emitir alerta `ERR_PREPRESS_RESOLUTION_LOW` e notificar o Art Director.

---

## 9. Exemplo Prático
```bash
# Validação de caminho seguro:
Origem: C:\mcp-corel\data\artifacts\uploads\logo_empresa.png
Canônico: C:\mcp-corel\data\artifacts\uploads\logo_empresa.png (VÁLIDO)

Tentativa de escape:
Origem: C:\mcp-corel\data\..\..\Windows\explorer.exe
Canônico: C:\Windows\explorer.exe (BLOQUEADO)
```

---

## 10. Critérios de Sucesso
- Ativo gráfico importado com integridade comprovada e 100% aderente às restrições de segurança de diretório.
