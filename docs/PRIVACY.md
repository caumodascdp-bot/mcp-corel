# Política de Privacidade e Tratamento de Dados — MCP Corel

Este documento define como dados pessoais, conteúdos gráficos e metadados são tratados pelo sistema **MCP Corel**, garantindo confidencialidade e respeito à privacidade dos usuários e clientes finais.

---

## 1. Princípio da Localidade e Soberania dos Dados

O princípio fundamental do **MCP Corel** é a **execução local**:
- Toda a renderização vetorial, geração de prévias e manipulação de arquivos ocorre **exclusivamente na máquina Windows 10 local**.
- O sistema **não envia** artes, documentos CDR, dados de clientes ou imagens para servidores em nuvem de terceiros, exceto quando explicitamente configurado pelo usuário para entrega via Telegram.

---

## 2. Tratamento de Dados Pessoais (PII) em Artes Gráficas

Muitas artes processadas (cartões de visita, crachás, folders publicitários, recibos) contêm dados pessoais como nomes, números de telefone, endereços de e-mail e fotografias.

### Diretrizes de Proteção:
1. **Minimização em Logs:** Nomes, CPFs, e-mails ou números de telefone contidos nos textos dos briefs artísticos **não** devem ser gravados nos logs de depuração do sistema. O log deve registrar apenas a contagem de nós e identificadores de objetos (`shape_id`).
2. **Isolamento de Arquivos:** Todos os arquivos de trabalho são armazenados localmente no diretório seguro (`data/artifacts/`).
3. **Não Compartilhamento com Treinamento de Modelos:** Os arquivos locais não são expostos a nenhum serviço de treinamento ou telemetria externa.

---

## 3. Privacidade na Integração com o Telegram

Quando o módulo de Telegram estiver ativo:
- **Metadados Transmitidos:** Apenas a imagem da prévia (PNG) e o documento final aprovado (PDF/CDR) são trafegados via API oficial do Telegram para a conversa privada do operador autorizado.
- **Armazenamento de Mensagens:** O bot não mantém banco de dados com histórico de conversas irrelevantes; apenas registra os comandos operacionais atrelados a um `job_id`.
- **Exclusão de Prévias Temporárias:** Arquivos gerados para visualização rápida no Telegram que estejam na pasta `data/previews/` podem ser expurgados periodicamente sem prejuízo ao arquivo original.

---

## 4. Retenção e Descarte de Dados

| Categoria de Dado | Local de Armazenamento | Política de Retenção | Método de Descarte |
| :--- | :--- | :--- | :--- |
| **Prévias Visuais (PNG/JPG)** | `data/previews/` | 7 dias | Exclusão automatizada de arquivos temporários |
| **Arquivos Mestres (.CDR)** | `data/artifacts/` | Permanente (controlado pelo operador) | Exclusão manual pelo usuário |
| **Arquivos de Entrega (PDF/EPS)** | `data/artifacts/` | Permanente | Exclusão manual pelo usuário |
| **Histórico de Tarefas (SQLite)** | `data/jobs/jobs.sqlite3` | 90 dias | Limpeza de registros antigos via rotina de manutenção |
| **Logs de Auditoria** | `logs/*.log` | 30 dias com rotação de 10 MB | Rotação automática de arquivos de log |
