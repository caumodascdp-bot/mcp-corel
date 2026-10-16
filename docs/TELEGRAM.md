# Operação Remota via Telegram — MCP Corel

Este documento detalha o funcionamento, as regras de segurança, o protocolo de comunicação e os limites operacionais do bot do Telegram integrado ao **MCP Corel**.

---

## 1. Princípios de Segurança e Conectividade

1. **Apenas Conexões de Saída (Long Polling):**
   - O worker do Telegram executa um loop de *long polling* através do método `getUpdates` da API oficial do Telegram.
   - **NENHUMA porta de entrada é aberta no computador Windows.** O firewall pode permanecer totalmente bloqueado para conexões de entrada. Não é necessário IP público, portas redirecionadas no roteador (port forwarding) ou serviços como ngrok.

2. **Allowlist Rígida por ID Numérico:**
   - O bot só aceita interações vindas de IDs de usuário configurados na variável de ambiente `TELEGRAM_ALLOWED_USER_IDS` (ex: `12345678,87654321`).
   - Mensagens, cliques ou comandos vindos de contas desconhecidas são **descartados silenciosamente**, sem enviar nenhuma mensagem de retorno que confirme a existência do bot.

3. **Proteção Total de Tokens:**
   - O token gerado pelo BotFather (`TELEGRAM_BOT_TOKEN`) deve residir exclusivamente no arquivo local `.env` da máquina Windows.
   - O repositório Git ignora o `.env`. O token nunca deve ser inserido em arquivos Markdown, issues ou logs de execução.

---

## 2. Comandos Disponíveis para o Operador

| Comando | Descrição | Requer Aprovação? | Resposta do Bot |
| :--- | :--- | :--- | :--- |
| `/start` | Mensagem de boas-vindas e status da estação. | Não | Texto com versão do sistema e estado da fila. |
| `/status` | Exibe se o CorelDRAW está ativo, uso de memória e tarefas pendentes. | Não | Resumo do sistema em português. |
| `/jobs` | Lista as últimas 5 tarefas e seus estados atuais. | Não | Lista formatada com Job IDs e status. |
| `/previa <job_id>` | Reenvia a imagem da prévia de uma tarefa específica. | Não | Envio de foto (PNG/JPG) com botões inline. |
| `/aprovar <job_id>` | Aprova o documento em prévia para geração do PDF final. | **SIM** | Atualiza estado para `running` e fecha o PDF. |
| `/rejeitar <job_id>` | Rejeita a arte e cancela o fechamento, permitindo novo briefing. | **SIM** | Atualiza estado para `cancelled`. |
| `/cancelar <job_id>` | Cancela uma tarefa que ainda esteja aguardando na fila. | **SIM** | Cancela execução da tarefa. |

---

## 3. Fluxo de Interação com Prévias e Botões Inline

Quando uma tarefa atinge o estado `needs_approval`, o worker do Telegram envia automaticamente uma mensagem com a imagem renderizada:

```
[IMAGEM DA PRÉVIA RENDERIZADA: preview_job_104.png]

🎨 Nova Arte Pronta para Revisão!
- Tarefa: #104
- Tipo: Flyer Inauguração (100x150mm)
- Cores: CMYK
- Sangria: 3mm verificada

Selecione a ação abaixo:
[ ✅ Aprovar e Gerar PDF ]    [ ❌ Rejeitar Arte ]
```

- Clicar em **Aprovar** dispara o callback que altera a tarefa para `running` e comanda o CorelDRAW a publicar o PDF final com marcas de corte.
- Após o fechamento, o arquivo PDF é enviado diretamente no chat como anexo.

---

## 4. Restrições e Limites Operacionais

- **Limite de Frequência (Rate Limiting):** O worker processa no máximo 20 comandos por minuto por operador para evitar sobrecarga de consultas ao banco SQLite.
- **Limite de Tamanho de Arquivo para Upload:** A API oficial de bots do Telegram limita o envio de arquivos pelo bot a 50 MB. Documentos finais que ultrapassarem 50 MB serão salvos na pasta local `data/artifacts/` e o bot enviará uma mensagem informando o caminho local, em vez de enviar o binário completo.
- **Tipos de Arquivos Permitidos para Download:** Imagens enviadas pelo operador humano no chat (para inserção na arte) só serão aceitas se forem dos formatos `.png`, `.jpg`, `.jpeg` ou `.svg`, e tiverem menos de 20 MB. Arquivos executáveis, scripts ou formatos desconhecidos serão sumariamente rejeitados.

---

## 5. Confirmação para Ações Destrutivas

Comandos que resultem em fechamento de documentos sem salvar, cancelamento de tarefas em lote ou exclusão de prévias exigem confirmação em duas etapas (envio do botão: *"Você tem certeza que deseja cancelar? [SIM] [NÃO]"*).
