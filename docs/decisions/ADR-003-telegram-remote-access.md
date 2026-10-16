# ADR-003: Arquitetura de Acesso Remoto Seguro via Telegram

- **Status:** Proposto (*proposed*)
- **Data:** 2026-10-16
- **Autores:** Product Architect, Telegram Operator, Security Reviewer

---

## 1. Contexto
Os operadores humanos necessitam acompanhar a geração de artes, visualizar prévias em tempo real e aprovar fechamentos gráficos a partir de dispositivos móveis (smartphones) fora da estação de trabalho. A API do Telegram suporta dois modos de operação para bots: *Webhooks* (o Telegram envia requisições HTTPS para o servidor do bot) e *Long Polling* (o bot faz requisições periódicas de saída para os servidores do Telegram).

---

## 2. Decisão Proposta
Propõe-se adotar o modelo de **Conexão de Saída por Long Polling (método `getUpdates`)**, combinado com uma **Allowlist estrita de identificadores de usuários numéricos (`TELEGRAM_ALLOWED_USER_IDS`)**.

É expressamente proibido o uso de Webhook que exija portas de entrada abertas no Windows ou o uso de ferramentas de redirecionamento público de túneis desprotegidos.

---

## 3. Alternativas Avaliadas

### Alternativa 1: Long Polling de Saída (Opção Escolhida)
- **Vantagens:** Não necessita de portas abertas no firewall do Windows nem de IP público ou domínio com certificado SSL próprio; a conexão é iniciada de dentro para fora (outbound HTTPS na porta 443 padrão).
- **Desvantagens:** Leve latência adicional (menos de 1 segundo) no recebimento de mensagens se comparado a webhooks instantâneos.

### Alternativa 2: Webhook com Porta Aberta e ngrok / Cloudflare Tunnel
- **Vantagens:** Entrega instantânea de notificações sem polling contínuo.
- **Desvantagens:** Expõe um endpoint público na internet apontando para o computador local; exige dependências de túneis externos; maior risco de tentativas de invasão e complexidade operacional.

---

## 4. Consequências

### Consequências Positivas:
- O operador pode comandar e aprovar artes de qualquer lugar pelo celular com máxima segurança de rede.
- Zero configurações complexas de roteador de internet ou regras de firewall na máquina local.

### Consequências Negativas:
- O processo do worker do Telegram precisa permanecer rodando em segundo plano enquanto o operador desejar receber alertas remotos.
