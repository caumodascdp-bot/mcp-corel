# Relatório da Fase 0 e Guia de Próximos Passos na Máquina Windows

Este documento serve como guia prático e direto para o operador quando for baixar (*git pull*) este repositório no computador com **Windows 10** e **CorelDRAW 2023**.

---

## 1. O Que Foi Realizado na Fase 0 (Nesta Máquina)

1. **Repositório Criado e Estruturado:**
   - O projeto foi criado de forma totalmente limpa e organizada no repositório `mcp-corel`.
   - **Nenhum código funcional de automação foi escrito ainda**, garantindo que não haja bugs prematuros ou pressupostos falsos sobre o CorelDRAW.
   - Foram estabelecidos **95 arquivos obrigatórios**, incluindo documentações, modelos, contratos e agentes.

2. **Segurança de Credenciais Blindada:**
   - **Nenhum token real, chave ou credencial oficial foi gravada.**
   - O arquivo `.env.example` contém apenas valores de exemplo e placeholders seguros (`INSIRA_SEU_TOKEN_AQUI_DO_BOTFATHER`).
   - O arquivo `.env` real está no `.gitignore` para nunca ser enviado ao GitHub.

3. **Governança e Contratos Técnicos:**
   - 11 agentes de IA definidos com separação de responsabilidades (ninguém aprova o próprio código).
   - 10 manuais operacionais (skills) cobrindo desde o briefing até a pré-impressão gráfica.
   - Schemas JSON validados para criação de arte e manifesto de arquivos.
   - 5 Decisões de Arquitetura (ADRs) aprovadas conceitualmente:
     - Uso de `stdio` local para o servidor MCP (zero portas abertas no Windows).
     - Uso de conexão de saída (*Long Polling*) para o Telegram.
     - Fila serializada com SQLite (`jobs.sqlite3`) para evitar travamento da automação COM.
     - Diretório raiz seguro (`C:\mcp-corel\data`) com proteção contra invasão de pastas.

---

## 2. Passo a Passo para Puxar e Preparar na Máquina Windows 10

Quando você estiver em frente ao computador Windows 10 que possui o CorelDRAW 2023 instalado:

### Passo 1: Abrir o Terminal no Windows
Pressione `Win + X` e escolha **Terminal**, **PowerShell** ou **Prompt de Comando**.

### Passo 2: Clonar o Repositório do GitHub
Navegue até a pasta onde deseja salvar o projeto (por exemplo, na raiz `C:\`):
```powershell
cd C:\
git clone https://github.com/caumodascdp-bot/mcp-corel.git
cd C:\mcp-corel
```
*(Caso a pasta já exista e você só queira atualizar, execute: `git pull origin main`)*

### Passo 3: Criar o Ambiente Isolado do Python
Certifique-se de que o Python 3.11 ou superior esteja instalado no Windows:
```powershell
python --version
python -m venv .venv
.\.venv\Scripts\Activate.ps1
```

### Passo 4: Criar o Arquivo Local de Configuração (.env)
Copie o modelo de exemplo para criar o arquivo oficial da sua máquina:
```powershell
copy .env.example .env
notepad .env
```
No arquivo `.env`, confira se os caminhos no Windows correspondem à sua máquina:
- `SAFE_STORAGE_ROOT=C:\mcp-corel\data`
- `WORK_DIR=C:\mcp-corel\data`
- Se for usar o Telegram: coloque o token do seu bot e o seu ID numérico.

---

## 3. Próximos Passos (Fase 1: Diagnóstico e Validação do CorelDRAW)

Assim que o projeto estiver clonado na máquina Windows correta e a **Fase 1** for autorizada:

1. **Rodar o Diagnóstico de Automação:**
   - Criaremos e executaremos o utilitário `python -m mcp_corel.corel.diagnostics` para verificar:
     - O caminho exato do executável `CorelDRW.exe`.
     - O identificador COM registrado (`CorelDRAW.Application.24`).
     - Se o Python e o CorelDRAW estão rodando na mesma arquitetura (64-bit).
2. **Prova de Conceito (PoC de 1 Quadrado):**
   - O sistema abrirá o CorelDRAW por alguns segundos, desenhará um retângulo azul de teste, salvará em `data/artifacts/poc_test.cdr`, exportará uma prévia em imagem `data/previews/poc_test.png` e fechará limpo.
3. **Evidência Real:**
   - Coletaremos os logs reais do Windows para confirmar que a automação funciona de verdade antes de construir o servidor MCP completo.

---

## 4. Dúvidas Frequentes

- **Por que o repositório é público?**  
  Porque ele contém apenas documentação técnica, padrões abertos e exemplos, sem nenhuma senha, dado pessoal ou segredo comercial. Qualquer pessoa pode estudar a arquitetura sem riscos à sua máquina.
- **E se o bot do Telegram não estiver configurado agora?**  
  Não tem problema: o bot é opcional (`TELEGRAM_BOT_ENABLED=false`). O servidor MCP funciona perfeitamente apenas de forma local com o AntiGravity, Claude Code ou Codex.
