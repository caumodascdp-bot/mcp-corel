# Critérios de Aceite Verificáveis — MCP Corel

Este documento estabelece os critérios objetivos que definem quando uma fase ou funcionalidade é considerada oficialmente aceita. Cada critério possui método de verificação explícito e evidência exigida.

---

## 1. Critérios de Aceite da Fase 0 (Arquitetura e Governança)

- [x] **AC-001 (Estrutura de Pastas):** Todas as pastas obrigatórias (`docs/`, `agents/`, `skills/`, `spaces/`, `contracts/`, `templates/`, `src/`, `tests/`, `data/`, `logs/`) existem no repositório.
  - *Verificação:* Inspeção de diretório no sistema de arquivos.
- [x] **AC-002 (Ausência de Código Executável Prematuro):** Nenhum script de automação, servidor em execução ou macro funcional foi criado ou executado na Fase 0.
  - *Verificação:* Busca por arquivos `.py` com lógica de negócio ou `.exe` executados.
- [x] **AC-003 (Validação de Schemas JSON):** Os schemas de contrato (`artwork-brief.schema.json` e `artifact-manifest.schema.json`) são arquivos JSON sintaticamente válidos segundo a especificação JSON Schema Draft-07.
  - *Verificação:* Validador JSON e schema parser executado sem erros.
- [x] **AC-004 (Definição de Agentes e Skills):** Os 11 agentes e as 10 skills possuem seus respectivos arquivos `AGENT.md` e `SKILL.md` com todos os campos obrigatórios preenchidos.
  - *Verificação:* Inspeção de conteúdo dos arquivos.
- [x] **AC-005 (Relatório de Fechamento da Fase 0):** O arquivo `docs/PHASE-0-REPORT.md` detalha o inventário, riscos, pendências e solicitação formal de autorização humana.

---

## 2. Critérios de Aceite da Fase 1 (Diagnóstico e PoC COM)

- [ ] **AC-101 (Diagnóstico da Instalação do CorelDRAW):** O comando `mcp-corel-diag` executa no Windows 10 e emite relatório contendo: ProgID COM ativo, caminho do executável `CorelDRW.exe`, arquitetura (64-bit/32-bit) e status de licença sem falhar.
  - *Evidência:* Saída completa do console registrada em log.
- [ ] **AC-102 (Prova de Conceito de Desenho COM):** Um script simples de teste consegue instanciar o CorelDRAW, criar um documento temporário de 100x100mm, desenhar um quadrado azul, salvar o arquivo como `poc_test.cdr` e fechar o documento sem deixar processo zumbi.
  - *Evidência:* Arquivo `poc_test.cdr` gerado e log com tempo de execução.
- [ ] **AC-103 (Verificação de Exportação PNG):** O script de PoC exporta uma prévia PNG a 150 DPI a partir do documento criado.
  - *Evidência:* Arquivo `poc_test.png` legível e íntegro.

---

## 3. Critérios de Aceite da Fase 2 (Servidor MCP & Fila de Jobs)

- [ ] **AC-201 (Comunicação MCP via stdio):** O servidor inicia como subprocesso local e responde com sucesso ao comando MCP `initialize` e à listagem de ferramentas (`tools/list`).
  - *Evidência:* Sessão JSON-RPC gravada em arquivo de teste.
- [ ] **AC-202 (Persistência da Fila em SQLite):** Ao invocar uma ferramenta de criação, uma entrada é gravada no banco `jobs.sqlite3` com status inicial `queued`.
  - *Evidência:* Consulta SQL `SELECT id, status FROM jobs;` mostrando a linha inserida.
- [ ] **AC-203 (Serialização de Operações):** Ao receber 3 requisições simultâneas de alteração gráfica, o sistema as processa estritamente uma após a outra (sequencialmente), sem concorrência na interface COM.
  - *Evidência:* Timestamps de início e término no banco comprovando execução não sobreposta.
- [ ] **AC-204 (Recuperação de Falha por Timeout):** Se uma chamada COM congelar por mais de 60 segundos, o watchdog cancela a operação e atualiza o estado para `failed` com código `TIMEOUT_COREL_COM`.
  - *Evidência:* Teste unitário de simulação de timeout atestado.

---

## 4. Critérios de Aceite da Fase 3 (Motor Gráfico & Exportação)

- [ ] **AC-301 (Criação de Documento e Formas Paramétricas):** A ferramenta `corel.create_document` cria documento em milímetros (RGB ou CMYK) e `corel.create_shape` insere retângulos e elipses com contorno e preenchimento calculados corretamente.
  - *Evidência:* Documento CDR gerado contendo os objetos nas posições especificadas.
- [ ] **AC-302 (Inserção de Texto Artístico e Parágrafo):** A ferramenta `corel.add_text` insere textos respeitando família de fonte, tamanho e quebra de linha.
  - *Evidência:* Prévia visual comprovando posicionamento correto do texto.
- [ ] **AC-303 (Geração de Prévia Rápida):** A ferramenta `corel.preview` gera imagem PNG da página em menos de 5 segundos.
  - *Evidência:* Imagem gerada no diretório `data/previews/` com menos de 2 MB de tamanho.
- [ ] **AC-304 (Exportação de PDF Gráfico com Marcas):** A ferramenta `corel.export_document` gera arquivo PDF contendo sangria de 3mm e marcas de corte padrão de gráfica.
  - *Evidência:* PDF aberto e inspecionado atestando presença das marcas de sangria.

---

## 5. Critérios de Aceite da Fase 4 (Operação Remota Telegram)

- [ ] **AC-401 (Conexão sem Portas de Entrada):** O worker do Telegram estabelece conexão de saída via *getUpdates* (long polling) sem necessidade de portas de entrada abertas no firewall do Windows.
  - *Evidência:* Verificação de portas do sistema (`netstat -ano`) mostrando nenhuma porta aberta em modo LISTENING pelo bot.
- [ ] **AC-402 (Bloqueio de Mensagens Não Autorizadas):** Mensagens enviadas por IDs do Telegram que não estejam em `TELEGRAM_ALLOWED_USER_IDS` são ignoradas sem resposta e registradas em log de segurança.
  - *Evidência:* Log de auditoria registrando tentativa de acesso não autorizado bloqueada.
- [ ] **AC-403 (Envio de Imagem de Prévia com Aprovação):** O bot envia foto da arte com botões inline "Aprovar" e "Rejeitar". Ao clicar em "Aprovar", a tarefa transita de `needs_approval` para `running` (gerando o fechamento final).
  - *Evidência:* Captura de tela ou log de callback query processado.

---

## 6. Critérios de Aceite do MVP Final (Versão 1.0)

- [ ] **AC-MVP-001 (Fluxo Completo de Ponta a Ponta):** Um briefing em linguagem natural ("Criar flyer promocional de inauguração 10x15cm com logotipo, título e telefone") é transformado em um documento CDR editável, com prévia inspecionada pela IA, aprovada pelo operador e exportada em PDF final fechado.
  - *Evidência:* Vídeo ou histórico completo de logs e arquivos finais gerados no disco.
- [ ] **AC-MVP-002 (Conformidade com Modelo de Ameaças):** 100% dos testes de segurança contra injeção de parâmetros, path traversal e sobrescrita acidental executados com sucesso.
- [ ] **AC-MVP-003 (Documentação e Manuais Completos):** Guias de instalação (`INSTALLATION.md`), configuração (`CONFIGURATION.md`) e operação (`OPERATIONS_RUNBOOK.md`) validados por teste limpo em nova estação de trabalho.
