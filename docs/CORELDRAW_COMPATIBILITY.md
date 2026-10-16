# Matriz de Compatibilidade do CorelDRAW 2023

> **AVISO IMPORTANTE:**  
> O estado de automação do CorelDRAW 2023 na estação Windows 10 física é **desconhecido** até a execução do diagnóstico na Fase 1. Nenhuma capacidade deve ser dada como certa sem teste real e evidência coletada.

---

## 1. Matriz de Capacidades de Automação

| Capacidade | Estado Atual | Hipótese Técnica | Evidência Exigida | Procedimento de Verificação |
| :--- | :--- | :--- | :--- | :--- |
| **Instalação do Executável** | `not_checked` | Caminho padrão: `C:\Program Files\Corel\CorelDRAW Graphics Suite 2023\Programs64\CorelDRW.exe` | Arquivo executável presente em disco com versão 24.x | Verificar existência do arquivo no disco via `os.path.exists()` |
| **Arquitetura do Processo** | `unknown` | Executável de 64-bit (`x64`) | Cabeçalho PE do binário confirma 64-bit | Inspeção via utilitário de diagnóstico Python |
| **Registro do Servidor COM** | `unknown` | ProgID: `CorelDRAW.Application.24` ou `CorelDRAW.Application` | Registro presente na chave `HKEY_CLASSES_ROOT` | Script de diagnóstico consulta Registry e instancia objeto COM |
| **Suporte a VBA / Macros** | `unknown` | Presente se for a versão *Graphics Suite* completa; ausente se for versão *Standard/Essentials* | Objeto `Application.VBA` ou `Application.GMSManager` acessível | Chamada COM para inspecionar componentes de automação |
| **Criação de Novo Documento** | `not_checked` | Método COM `Application.CreateDocument()` | Documento aberto na interface sem erros | Teste de PoC instanciando 1 documento em branco |
| **Desenho de Retângulos e Elipses** | `not_checked` | Métodos `ActiveLayer.CreateRectangle()` e `CreateEllipse()` | Objeto adicionado à coleção de formas do documento | Consulta via COM lendo largura e altura criadas |
| **Criação de Texto Artístico** | `not_checked` | Método `ActiveLayer.CreateArtisticText()` | Texto renderizado e lido de volta via COM | Criação de texto de teste e validação de propriedades |
| **Importação de Imagens (PNG/JPG)** | `not_checked` | Método `ActiveLayer.Import()` | Bitmap inserido na página com bounding box válida | Importar arquivo de teste e conferir contagem de formas |
| **Exportação de Prévia (PNG)** | `not_checked` | Filtro de exportação `cdrPNG` nativo | Arquivo PNG salvo e validado no disco | Exportar página ativa e validar cabeçalho PNG |
| **Exportação para PDF Gráfico** | `not_checked` | Mecanismo de publicação em PDF (`PublishToPDF`) | Arquivo PDF gerado com marcas de corte | Exportar com perfil de pré-impressão e inspecionar |
| **Supressão de Avisos Modais** | `unknown` | Propriedade `Application.Optimization = True` ou similar | Nenhuma janela de diálogo trava a execução em segundo plano | Testar abertura de arquivo com fonte não instalada |

---

## 2. Roteiro de Verificação (Diagnóstico da Fase 1)

O diagnóstico oficial será executado via comando Python na máquina Windows 10:

```bash
# Executado exclusivamente no Windows após aprovação da Fase 1:
python -m mcp_corel.corel.diagnostics
```

O diagnóstico executa as seguintes etapas:
1. **Varredura de Registro do Windows:**
   - Procura por chaves COM: `CorelDRAW.Application`, `CorelDRAW.Application.24`, `CorelDRAW.Application.23`.
2. **Teste de Conexão COM sem GUI:**
   - Tenta inicializar o objeto via `win32com.client.Dispatch("CorelDRAW.Application")`.
   - Lê a propriedade `Version` e o caminho do executável.
3. **Verificação de Edição do Software:**
   - Verifica se a edição possui as bibliotecas de automação habilitadas.
4. **Emissão de Relatório Diagnóstico:**
   - Grava o resultado detalhado em `logs/corel_diagnostics_report.json`.

---

## 3. Planos de Contingência (Fallbacks) Caso o COM Esteja Inacessível

Caso o diagnóstico aponte que a versão do CorelDRAW instalada não expõe a interface COM tradicional:

| Abordagem Alternativa | Prós | Contras | Decisão |
| :--- | :--- | :--- | :--- |
| **Fallback A: Geração de SVG/PDF + Importação por Script** | Independe de COM profundo; arquivos SVG são gerados por biblioteca padrão e importados. | Menor controle sobre objetos individuais após a importação. | **Primeira Alternativa** caso o COM de criação seja limitado. |
| **Fallback B: Macros GMS / VSTA Pré-Instaladas** | Execução rápida de rotinas nativas dentro do CorelDRAW. | Exige instalação manual de arquivos `.gms` pelo operador humano. | **Segunda Alternativa** se COM externo for bloqueado por segurança. |
| **Fallback C: Automação via pywinauto / GUI** | Funciona com qualquer edição do CorelDRAW. | Extremamente frágil a mudanças de resolução, temas de tela e janelas sobrepostas. | **Último Recurso** (não recomendado para produção). |
