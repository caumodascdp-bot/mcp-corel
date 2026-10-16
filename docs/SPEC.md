# Especificação Técnica — MCP Corel

Este documento detalha todos os requisitos funcionais e não-funcionais do sistema **MCP Corel**, numerados para rastreabilidade em testes e critérios de aceite.

---

## 1. Requisitos Funcionais (RF)

### 1.1 Gerenciamento de Documentos e Diagnóstico
- **RF-001 (Diagnóstico de Ambiente):** O sistema deve fornecer ferramenta para inspecionar e relatar a presença do CorelDRAW 2023, registro COM, versão do executável e formatos de exportação disponíveis, sem alterar arquivos.
- **RF-002 (Criação de Documento):** O sistema deve permitir a criação de novos documentos CorelDRAW especificando nome, largura, altura, unidade de medida (mm, cm, pt, px), orientação (retrato/paisagem) e espaço de cor principal (CMYK ou RGB).
- **RF-003 (Listagem de Documentos):** O sistema deve listar documentos atualmente abertos no CorelDRAW, indicando nome, caminho em disco, quantidade de páginas e se há alterações não salvas.
- **RF-004 (Obtenção de Metadados do Documento):** O sistema deve detalhar dimensões da página ativa, sangria configurada, quantidade de camadas e contagem de objetos vetoriais/texto/bitmap.
- **RF-005 (Salvamento de Documento):** O sistema deve salvar o documento ativo no formato nativo `.cdr`. Tentativas de salvar com mesmo nome de arquivo existente exigem confirmação ou flag de sobrescrita.

### 1.2 Criação e Edição Gráfica Vetorial
- **RF-006 (Criação de Formas Vetoriais Básicas):** O sistema deve permitir a criação de retângulos, elipses/círculos e linhas retas com coordenadas exatas, espessura de contorno, cor de contorno e cor de preenchimento.
- **RF-007 (Inserção e Formatação de Texto):** O sistema deve suportar criação de texto artístico e texto de parágrafo, especificando família da fonte, tamanho em pontos, alinhamento, cor e texto literal.
- **RF-008 (Importação de Imagens e Vetores Externos):** O sistema deve permitir importar imagens raster (PNG, JPG) e vetores (SVG, EPS) locais a partir da pasta autorizada, aplicando posicionamento e escala paramétrica.
- **RF-009 (Alinhamento e Distribuição):** O sistema deve permitir alinhar objetos selecionados entre si ou em relação ao centro ou bordas da página.
- **RF-010 (Agrupamento e Ordenação de Camadas):** O sistema deve suportar agrupar objetos selecionados e alterar a ordem visual (trazer para frente, enviar para trás).

### 1.3 Prévias e Exportação
- **RF-011 (Geração de Prévia Visual):** O sistema deve exportar uma imagem de prévia (PNG/JPEG) da página ativa ou de objetos selecionados com resolução ajustável (ex: 72 a 150 DPI) para inspeção rápida da IA e do usuário.
- **RF-012 (Exportação para Gráfica e Web):** O sistema deve exportar a arte final para os formatos:
  - PDF (padrão de pré-impressão com marcas de corte e sangria ou padrão web).
  - EPS / SVG (vetorial aberto).
  - PNG / JPG (raster de alta resolução).
- **RF-013 (Verificação de Pré-Impressão / Preflight):** O sistema deve validar se o documento atende às exigências de impressão: sangria mínima (ex: 3mm), presença de textos em RGB em documentos CMYK, e resolução mínima de imagens (300 DPI).

### 1.4 Gerenciamento de Fila de Tarefas (Jobs)
- **RF-014 (Criação e Consulta de Tarefa):** O sistema deve registrar cada operação solicitada em uma fila local persistente (SQLite) com ID único de tarefa (Job ID).
- **RF-015 (Acompanhamento de Estado):** O sistema deve expor o estado da tarefa através da máquina de estados: `queued`, `running`, `needs_approval`, `succeeded`, `failed`, `cancelled`.
- **RF-016 (Cancelamento de Tarefa):** O sistema deve permitir cancelar tarefas pendentes na fila antes de sua execução iniciar.
- **RF-017 (Consulta de Artefatos Gerados):** O sistema deve permitir consultar a lista de caminhos e metadados dos arquivos finais gerados por uma tarefa concluída.

### 1.5 Interface Remota via Telegram
- **RF-018 (Consulta Remota de Status):** O bot do Telegram deve permitir comandos `/status` para verificar se o CorelDRAW e a fila estão ativos.
- **RF-019 (Envio de Prévias no Chat):** O bot deve enviar a imagem de prévia renderizada diretamente no chat do Telegram com botões inline de aprovação.
- **RF-020 (Aprovação Interativa):** O bot deve permitir aprovação (`/aprovar <job_id>`) ou rejeição com comentário de uma tarefa em estado `needs_approval`.
- **RF-021 (Entrega de Arquivo Final):** Após aprovação e exportação, o bot deve enviar o arquivo final (PDF/PNG) como documento anexado no chat do Telegram, respeitando o limite de tamanho da API.

---

## 2. Requisitos Não-Funcionais (RNF)

### 2.1 Desempenho e Recursos
- **RNF-001 (Serialização Estrita de Operações):** O sistema deve processar modificações no CorelDRAW de forma estritamente sequencial (uma por vez) para evitar condições de corrida na interface COM.
- **RNF-002 (Tempo Limite de Operação COM):** Toda chamada ao CorelDRAW deve possuir timeout configurável (padrão: 60 segundos). Caso a aplicação trave em um diálogo modal, a tarefa deve falhar com erro específico sem derrubar o servidor MCP.
- **RNF-003 (Armazenamento Leve e Rápido):** O banco de dados de tarefas deve utilizar SQLite em modo WAL, garantindo operações de leitura sem travamento durante escritas.

### 2.2 Segurança e Privacidade
- **RNF-004 (Comunicação MCP Local Padrão):** O transporte oficial entre o cliente de IA e o servidor MCP deve ser via `stdio`.
- **RNF-005 (Conectividade Telegram Segura):** A comunicação com o Telegram deve operar exclusivamente por *HTTPS Long Polling* de saída. Nenhuma porta TCP/UDP de entrada deve ser aberta no Windows.
- **RNF-006 (Controle de Acesso Telegram):** O bot do Telegram deve rejeitar e descartar silenciosamente qualquer mensagem vinda de IDs de usuário não listados na variável `TELEGRAM_ALLOWED_USER_IDS`.
- **RNF-007 (Proteção de Sandbox de Arquivos):** Nenhum arquivo pode ser lido, criado ou apagado fora do diretório delimitado por `SAFE_STORAGE_ROOT`.
- **RNF-008 (Auditoria Limpa de Segredos):** Logs de execução nunca devem conter tokens de API, senhas ou dados cadastrais.

### 2.3 Confiabilidade e Tolerância a Falhas
- **RNF-009 (Recuperação após Reinício):** Caso o servidor ou o computador seja reiniciado inesperadamente, tarefas em estado `running` devem ser marcadas como `failed` na inicialização seguinte, com motivo documentado ("Servidor reiniciado durante execução").
- **RNF-010 (Tratamento de Janelas Modais Bloqueantes):** O sistema deve configurar o ambiente CorelDRAW para suprimir ou tratar janelas de aviso automático (ex: aviso de fontes ausentes), registrando o evento no log de auditoria.

### 2.4 Retenção e Limpeza de Disco
- **RNF-011 (Política de Retenção de Prévias):** Arquivos temporários de prévia (`data/previews/`) mais antigos que 7 dias devem ser elegíveis para limpeza automática pelo operador.
- **RNF-012 (Preservação de Arquivos Mestres):** Documentos mestres (.cdr) e artefatos de entrega (`data/artifacts/`) nunca devem ser excluídos automaticamente por rotinas de limpeza.
