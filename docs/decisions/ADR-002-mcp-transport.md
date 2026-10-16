# ADR-002: Transporte Padrão para o Protocolo MCP

- **Status:** Proposto (*proposed*)
- **Data:** 2026-10-16
- **Autores:** Product Architect, MCP Engineer, Security Reviewer

---

## 1. Contexto
O protocolo Model Context Protocol (MCP) suporta múltiplos mecanismos de transporte de mensagens, sendo os mais comuns `stdio` (entrada/saída padrão de subprocessos) e `SSE` (Server-Sent Events sobre HTTP). A estação de trabalho Windows 10 hospeda a ferramenta e precisa ser protegida contra acessos externos não autenticados e varreduras de portas na rede local.

---

## 2. Decisão Proposta
Propõe-se adotar **exclusivamente o transporte `stdio`** como padrão oficial para o servidor MCP Corel.

O servidor será lançado pelo cliente de IA (AntiGravity, Claude Code ou Codex) como um processo filho local. Qualquer necessidade futura de acesso remoto via MCP deverá ser realizada exclusivamente através de túnel criptografado privado (ex: Tailscale ou WireGuard VPN) ou gateway HTTPS autenticado com proxy reverso, precedido de threat model dedicado.

---

## 3. Alternativas Avaliadas

### Alternativa 1: Transporte `stdio` Local (Opção Escolhida)
- **Vantagens:** Máximo isolamento de rede; zero portas TCP/UDP abertas no Windows; gerenciamento simples de ciclo de vida (quando o cliente fecha, o processo morre); total compatibilidade com AntiGravity e Claude Code.
- **Desvantagens:** Requer que o cliente de IA esteja rodando na mesma máquina ou que utilize túnel de transporte.

### Alternativa 2: Servidor HTTP / SSE com Porta Aberta
- **Vantagens:** Permite que clientes de IA em outras máquinas acessem o servidor diretamente pela rede.
- **Desvantagens:** Abre portas de escuta na máquina Windows (risco de ataque); exige gerenciamento de certificados SSL/TLS locais, autenticação por Bearer Token e controle de firewall. Rejeitado para o padrão inicial.

---

## 4. Consequências

### Consequências Positivas:
- Superfície de ataque na rede reduzida a zero no Windows.
- Instalação e execução simples sem conflito de portas ocupadas (ex: erro porta 8000 já em uso).

### Consequências Negativas:
- Clientes remotos de IA não podem chamar o MCP diretamente sem uma VPN ou túnel seguro pré-configurado.
