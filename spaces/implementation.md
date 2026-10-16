# Espaço de Trabalho: Implementação

Este documento reúne as diretrizes de engenharia de software, contratos de ferramentas e referências técnicas para os agentes que desenvolvem os módulos do **MCP Corel**.

---

## 1. Missão da Área
Construir código Python limpo, tipado, modular e performático, encapsulando com segurança a interface COM do CorelDRAW, o protocolo MCP e as rotinas do worker do Telegram.

---

## 2. Agentes Vinculados
- **MCP Engineer** (Servidor e ferramentas MCP)
- **Corel Automation Engineer** (Adaptador COM do CorelDRAW)
- **Layout Engineer** (Cálculo de leiaute paramétrico)

---

## 3. Arquivos e Recursos Centrais
- [Contratos de Ferramentas MCP](file:///home/kali/mcp-corel/contracts/mcp-tools.md)
- [Máquina de Estados de Jobs](file:///home/kali/mcp-corel/contracts/job-state-machine.md)
- [Códigos de Erro Estruturados](file:///home/kali/mcp-corel/contracts/error-codes.md)
- [Guia de Configuração](file:///home/kali/mcp-corel/docs/CONFIGURATION.md)
- [Código-Fonte Principal](file:///home/kali/mcp-corel/src/mcp_corel/)

---

## 4. Regras e Restrições Vigentes
1. Toda operação que altere o CorelDRAW deve ser estritamente sequencial (uma por vez) controlada pela fila.
2. Todas as chamadas COM devem possuir timeout com watchdog de 60 segundos.
3. Todo código deve ser formatado com Ruff e tipado com Mypy.
4. Nenhum código de negócio deve usar `print()` solto no stdout (usar logging em stderr).
