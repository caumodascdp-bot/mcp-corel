# Espaço de Trabalho: Release e Operação

Este documento estabelece as rotinas de publicação de versões, operação em produção, contingência em caso de falhas e monitoramento contínuo da estação Windows 10.

---

## 1. Missão da Área
Manter os serviços do MCP Corel operando com alta disponibilidade, gerenciar atualizações sem interrupções traumáticas, monitorar a saúde da estação de trabalho e resolver incidentes operacionais rapidamente.

---

## 2. Agentes Vinculados
- **Telegram Operator** (Atendimento e entrega remota)
- **Orchestrator** (Despacho e recuperação de jobs)
- **DevOps / Operador Humano** (Administração do Windows)

---

## 3. Arquivos e Recursos Centrais
- [Manual de Operações (Runbook)](file:///home/kali/mcp-corel/docs/OPERATIONS_RUNBOOK.md)
- [Resolução de Problemas (Troubleshooting)](file:///home/kali/mcp-corel/docs/TROUBLESHOOTING.md)
- [Dicionário de Logs Operacionais](file:///home/kali/mcp-corel/docs/dicionario-logs.md)
- [Checklist de Release](file:///home/kali/mcp-corel/skills/release-checklist/SKILL.md)
- [Registro de Mudanças (Changelog)](file:///home/kali/mcp-corel/CHANGELOG.md)

---

## 4. Regras e Restrições Vigentes
1. Tarefas interrompidas por desligamento abrupto devem ser recuperadas na reinicialização.
2. Prévias com mais de 7 dias devem ser rotineiramente limpas, preservando os arquivos `.cdr` mestres.
3. Toda nova versão do sistema deve cumprir 100% do checklist de liberação antes de ser implantada.
