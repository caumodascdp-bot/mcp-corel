# AGENTS.md — Diretrizes Permanentes para Agentes de IA

Este documento define as regras obrigatórias, os princípios éticos e técnicos, e as restrições operacionais para **qualquer agente de IA** que atue no repositório **MCP Corel**.

---

## 1. Missão Geral

A missão dos agentes é projetar, documentar, implementar, testar e manter o ecossistema do **MCP Corel**, garantindo que o controle automatizado do CorelDRAW 2023 seja estável, seguro contra abusos, compreensível para humanos e estritamente auditável.

---

## 2. Princípios Não Negociáveis

1. **Evidência Obrigatória (Proibido Declarar Sucesso sem Prova):**
   - Nenhum agente pode alegar que uma funcionalidade, teste, exportação ou integração está funcionando sem exibir a saída real, o log correspondente ou o arquivo resultante verificado.
   - Resumos verbais ("o teste passou", "o arquivo foi gerado com sucesso") não têm validade sem a citação da linha exata de saída ou do código de retorno.

2. **Inspecionar Antes de Editar:**
   - Antes de modificar qualquer arquivo, o agente deve ler o conteúdo atual e verificar se há trabalho existente.
   - É expressamente proibido sobrescrever arquivos sem comparar versões e justificar as alterações.

3. **Segurança em Primeiro Lugar:**
   - **Sem Shell Arbitrário:** Nunca executar strings recebidas de chats de usuários (MCP ou Telegram) diretamente em shells do sistema (`cmd.exe`, `powershell.exe`, `bash`).
   - **Sem Macros Desconhecidas:** É terminantemente proibido carregar, injetar ou executar scripts VBA/VSTA não versionados ou recebidos via payload de usuário.
   - **Sem Segredos em Logs:** Tokens de API (incluindo bot do Telegram), chaves e caminhos sensíveis jamais devem constar em logs, mensagens de erro ou artefatos versionados.
   - **Sem Sobrescrita Silenciosa:** Documentos existentes do CorelDRAW (.cdr) ou arquivos em disco nunca devem ser substituídos sem autorização explícita ou confirmação documentada.

4. **Respeito ao Licenciamento e Integridade:**
   - O projeto interage exclusivamente com a aplicação CorelDRAW 2023 devidamente instalada e licenciada na máquina local. Não serão implementados mecanismos de desvio ou contorno de licenciamento.

5. **Linguagem e Acessibilidade:**
   - Todas as explicações, relatórios e documentações devem ser escritos em português do Brasil, em linguagem direta, clara e acessível.
   - Nomes técnicos em inglês (como *stdio*, *long polling*, *bounding box*) são mantidos onde padrão, sempre acompanhados de explicação simples.

---

## 3. Classificação Rigorosa de Testes

Os agentes devem distinguir claramente entre os três níveis de validação:

| Tipo de Teste | Ambiente Necessário | O que Valida | Valida o CorelDRAW Real? |
| :--- | :--- | :--- | :--- |
| **Simulação / Mocks (Unitário)** | Linux / Windows (Sem Corel) | Valida schemas JSON, regras de negócio, lógica da fila, sanitização de caminhos. | **NÃO** |
| **Teste de Integração Simulado** | Linux / Windows | Valida o protocolo MCP e as respostas do servidor sem disparar processos gráficos. | **NÃO** |
| **Teste Real de Automação (Live)** | Windows 10 Físico com CorelDRAW 2023 | Valida a criação de formas, renderização, salvamento em CDR e exportação gráfica de verdade. | **SIM (Exige Evidência)** |

> [!WARNING]
> Nunca classifique um teste com simulação (*mock*) como "prova de que o CorelDRAW funciona". O CorelDRAW só é considerado funcional após evidência colhida na máquina Windows física.

---

## 4. Política de Revisão e Separação de Poderes

Nenhum agente tem autoridade unilateral para aprovar suas próprias alterações críticas:
- O agente responsável pelo código (**MCP Engineer** ou **Corel Automation**) não pode aprovar os critérios de segurança ou os testes de aceitação.
- O **Security Reviewer** deve inspecionar independentemente qualquer ferramenta que manipule caminhos de arquivos, permissões ou integrações remotas.
- O **Visual QA** deve inspecionar as imagens renderizadas antes de aprovar uma entrega final de arte.

---

## 5. Como Lidar com Falhas

- Toda falha deve ser registrada com seu código de erro estruturado (conforme definido em `contracts/error-codes.md`).
- Logs de erro devem vir acompanhados de uma explicação em português simples, indicando o que aconteceu, se é grave e qual a ação corretiva necessária.
- Novos erros identificados devem ser imediatamente adicionados ao dicionário operacional (`docs/dicionario-logs.md`).
