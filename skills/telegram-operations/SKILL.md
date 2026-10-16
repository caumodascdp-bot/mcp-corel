---
name: telegram-operations
description: Procedimento operacional para comunicação, aprovação remota e entrega de arquivos via bot do Telegram com long polling.
---

# Procedimento Operacional: Operações Remotas via Telegram

## 1. Objetivo
Padronizar a gestão das mensagens de comando, envio de imagens de prévia, coleta de votos de aprovação e entrega de arquivos gráficos através do bot Telegram oficial do sistema.

---

## 2. Quando Usar
- Durante a execução contínua do worker do Telegram.
- Ao notificar operadores sobre tarefas prontas para aprovação.
- Ao entregar arquivos PDF/CDR solicitados remotamente.

---

## 3. Entradas Necessárias
- Token válido do bot (`TELEGRAM_BOT_TOKEN`) e lista de IDs autorizados (`TELEGRAM_ALLOWED_USER_IDS`).
- Dados da tarefa (Job ID, tipo de arte, status atual, caminho da imagem de prévia).

---

## 4. Procedimento Passo a Passo
1. **Inicialização do Poller:**
   - Iniciar o loop de escuta `getUpdates` com timeout de 30 segundos (conexão HTTPS de saída).
2. **Filtragem de Acesso:**
   - Para cada atualização recebida, inspecionar o campo `message.from.id`.
   - Se o ID não constar na allowlist, descartar o pacote sem resposta e registrar no log de segurança.
3. **Envio de Notificação de Prévia:**
   - Quando um job atinge o estado `needs_approval`, enviar mensagem com a foto renderizada (`sendPhoto`).
   - Anexar teclado inline contendo dois botões de callback:
     - `Aprovar [job_id]` (dispara fechamento).
     - `Rejeitar [job_id]` (cancela fechamento).
4. **Processamento do Callback de Aprovação:**
   - Ao receber o clique em "Aprovar", editar a mensagem original confirmando o aceite e mudar o status do job para `running`.
5. **Entrega do Arquivo Final:**
   - Quando o job for concluído com sucesso (`succeeded`), enviar o arquivo `.pdf` gerado via método `sendDocument`.
   - Se o arquivo ultrapassar 50 MB, enviar mensagem informando que o arquivo está salvo na estação de trabalho local.

---

## 5. Ferramentas Permitidas
- Biblioteca `python-telegram-bot` rodando em modo assíncrono.
- Acesso de leitura ao banco de jobs SQLite.

---

## 6. Saídas Esperadas
- Mensagens entregues com sucesso na conversa do operador.
- Transições de estado do job registradas com o ID do aprovador.

---

## 7. Validações
- 0 mensagens respondidas para IDs não autorizados.
- Nenhum token exposto nas mensagens de log do console.

---

## 8. Falhas Comuns e Ações Corretivas
- **Falha:** Timeout na conexão com os servidores do Telegram.
  - *Ação:* O loop de polling deve capturar `NetworkError` e tentar reconexão com espera exponencial (backoff).
- **Falha:** Arquivo PDF maior que 50 MB rejeitado pelo Telegram.
  - *Ação:* Capturar o erro e enviar mensagem explicativa: *"Arquivo gerado com sucesso, porém excede o limite de envio do Telegram (50 MB). Disponível localmente na pasta de artefatos."*

---

## 9. Exemplo Prático de Mensagem
```
🎨 Prévia da Arte Concluída!
ID da Tarefa: #108
Material: Cartão de Visita (90x50mm)
Preflight: Aprovado (CMYK, 300 DPI, Curvas)

Deseja aprovar a impressão?
[ ✅ Sim, Gerar PDF ]    [ ❌ Não, Ajustar ]
```

---

## 10. Critérios de Sucesso
- Notificação de prévia entregue em menos de 5 segundos e arquivo final recebido pelo operador sem falhas de rede.
