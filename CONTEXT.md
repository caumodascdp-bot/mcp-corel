# CONTEXT.md — Contexto Duradouro do Produto

Este documento reúne o estado permanente do conhecimento sobre o projeto **MCP Corel**, registrando fatos verificados, hipóteses a testar, decisões tomadas e regras de negócio.

---

## 1. O Que é o Produto

O **MCP Corel** é uma ponte de software baseada no padrão **Model Context Protocol (MCP)** que conecta assistentes de inteligência artificial a uma estação de trabalho local Windows 10 equipada com o software vetorial **CorelDRAW 2023**. 

Seu objetivo é viabilizar um fluxo de criação gráfica semiautônomo, onde a IA entende um briefing, monta o leiaute vetorial no CorelDRAW, gera prévias visuais para aprovação humana e exporta os arquivos finais fechados para gráfica ou mídias digitais.

---

## 2. Fatos Conhecidos vs. Fatos Desconhecidos

### Fatos Conhecidos (Verificados)
1. **Padrão de Comunicação de IA:** O protocolo MCP suporta transporte local via `stdio` (comunicação por entrada/saída padrão de processos), ideal para isolamento no Windows.
2. **Segurança de Rede Local:** Abrir portas públicas (como portas HTTP normais) em uma máquina Windows residencial/comercial expõe a máquina a varreduras de portas e ataques externos.
3. **Comunicação Segura Telegram:** A API de bots do Telegram suporta *long polling* (onde o computador faz requisições HTTPS de saída para os servidores do Telegram), dispensando portas de entrada abertas ou IP público fixo.
4. **Armazenamento de Jobs:** O SQLite em modo WAL (Write-Ahead Logging) atende com sobra às necessidades de persistência local de histórico de tarefas e estados.

### Fatos Desconhecidos (Exigem Diagnóstico na Máquina Alvo)
1. **Presença do Objeto COM do Corel:** Qual é o `ProgID` exato registrado no Windows Registry para o CorelDRAW 2023? (Candidatos: `CorelDRAW.Application.24`, `CorelDRAW.Application`).
2. **Suporte a Automação por Versão/Edição:** A versão instalada do CorelDRAW 2023 é a versão completa (Graphics Suite com suporte a COM/VBA) ou é uma versão restrita (como Essentials ou Standard, que removem componentes de automação)?
3. **Arquitetura da Instalação:** O executável instalado é 64-bit ou 32-bit? (O processo Python que invoca o COM precisa ser compatível ou usar ponte de bits).
4. **Diálogos Modais Bloqueantes:** O CorelDRAW 2023 exibe janelas de alerta modal (ex: fontes ausentes, perfil de cor incompatível) que possam travar a automação em segundo plano?
5. **Formatos Nativos de Exportação:** Quais filtros de exportação (PDF, EPS, SVG, PNG) estão instalados e licenciados no ambiente local?

---

## 3. Estado Atual de Implementação

- **Fase Atual:** Fase 0 (Planejamento, Arquitetura e Contratos).
- **Código Funcional:** Nenhum implementado.
- **Estrutura de Pastas:** Criada e padronizada.
- **Contratos e Schemas:** Prontos e validados.
- **Próximo Marco:** Fase 1 — Diagnóstico de ambiente e Prova de Conceito (PoC) da automação COM na máquina Windows real.

---

## 4. Glossário Rápido do Domínio

- **MCP (Model Context Protocol):** Padrão aberto criado pela Anthropic para permitir que modelos de IA usem ferramentas e acessem dados estruturados.
- **COM (Component Object Model):** Tecnologia do Windows que permite a um programa (ex: script Python) controlar outro programa aberto (ex: CorelDRAW).
- **ProgID (Programmatic Identifier):** O nome do programa registrado no Windows que permite ao script localizá-lo (ex: `CorelDRAW.Application`).
- **Sangria (Bleed):** Área extra de arte além da linha de corte de um material gráfico para evitar bordas brancas após o refile na gráfica.
- **Margem de Segurança:** Espaço interno entre os textos/elementos importantes e a borda de corte.
- **Long Polling:** Método em que o bot faz chamadas para buscar novas mensagens no servidor do Telegram, sem precisar que a internet acesse o computador diretamente.

---

## 5. Regras de Segurança Permanentes

1. O servidor MCP padrão só escutará conexões locais em `stdio`.
2. Toda escrita de arquivos (.cdr, .pdf, .png) deve ficar restrita à pasta autorizada (`SAFE_STORAGE_ROOT`).
3. O bot do Telegram só responderá a IDs de usuários previamente cadastrados em uma lista restrita (*allowlist*).
4. Nenhuma instrução em texto recebida de um chat será repassada diretamente para execução no terminal Windows (`cmd` ou `powershell`).
