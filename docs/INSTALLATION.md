# Guia de Instalação — MCP Corel

> [!NOTE]
> Este guia documenta o procedimento de instalação que será executado **a partir da Fase 1**, após a aprovação formal da arquitetura. **Na Fase 0 atual, nenhum pacote deve ser instalado.**

---

## 1. Pré-Requisitos do Sistema

1. **Estação de Trabalho:** Computador com Windows 10 (versão 21H2 ou mais recente, 64 bits recomendada).
2. **CorelDRAW:** CorelDRAW Graphics Suite 2023 instalado e ativado com licença válida.
3. **Python:** Python 3.11.x ou 3.12.x instalado no Windows (certifique-se de marcar a opção *"Add python.exe to PATH"* durante o instalador).
4. **Git:** Git for Windows para clonagem do repositório.

---

## 2. Passo a Passo de Instalação (Para Fase 1 em Diante)

### Passo 1: Clonar ou Posicionar o Repositório
No terminal PowerShell ou CMD do Windows:
```powershell
cd C:\
git clone https://github.com/seu-usuario/mcp-corel.git
cd C:\mcp-corel
```

### Passo 2: Criar o Ambiente Virtual Isolado
```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
```

### Passo 3: Instalar as Dependências do Projeto
```powershell
# Instalação do pacote em modo editável com dependências
pip install --upgrade pip
pip install -e ".[dev]"

# Se for utilizar o bot do Telegram:
pip install -e ".[telegram]"
```

### Passo 4: Configurar o Arquivo de Ambiente
```powershell
copy .env.example .env
notepad .env
```
Ajuste os caminhos de diretório conforme a sua máquina local (ex: `C:\mcp-corel\data`).

### Passo 5: Executar o Diagnóstico de Ambiente
Antes de rodar qualquer automação, rode o utilitário de diagnóstico:
```powershell
python -m mcp_corel.corel.diagnostics
```
O utilitário confirmará se o CorelDRAW 2023 responde via COM e se os diretórios têm permissão de escrita.

---

## 3. Verificação Rápida da Instalação

Para testar se os componentes básicos e schemas estão em ordem:
```powershell
pytest tests/unit/
```
Se todos os testes unitários passarem, o ambiente de desenvolvimento está pronto.
