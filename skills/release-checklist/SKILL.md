---
name: release-checklist
description: Roteiro obrigatório de verificação antes de concluir fases e liberar novas versões do produto MCP Corel.
---

# Procedimento Operacional: Checklist de Liberação (Release Checklist)

## 1. Objetivo
Garantir que todas as fases do projeto e novas versões atendam aos critérios formais de qualidade, documentação, testes e segurança antes de serem consideradas concluídas.

---

## 2. Quando Usar
- No encerramento de cada fase do cronograma (`docs/ROADMAP.md`).
- Antes de criar tags de versão Git (ex: `v0.1.0-alpha`, `v1.0.0`).

---

## 3. Entradas Necessárias
- Versão a ser liberada e lista de alterações no `CHANGELOG.md`.
- Resultados das suítes de testes automatizados e logs de evidência física.
- Relatório de fechamento da fase anterior.

---

## 4. Procedimento Passo a Passo
1. **Verificação de Documentação e Links:**
   - Conferir se todos os links relativos entre documentos Markdown estão íntegros e apontam para arquivos existentes.
   - Garantir que `README.md`, `SPEC.md` e `ROADMAP.md` reflitam exatamente o estado atual.
2. **Conferência de Schemas JSON:**
   - Validar sintaxe e integridade de todos os arquivos `.json` e `.schema.json`.
3. **Execução da Suíte Completa de Testes:**
   - Executar `pytest tests/unit/ tests/security/` (100% de sucesso exigido).
   - Para fases com CorelDRAW real: anexar o log de teste físico no Windows.
4. **Inspeção de Segurança e Segredos:**
   - Rodar busca no repositório por strings de tokens, senhas ou caminhos locais reais de usuários.
   - Confirmar que arquivos `.env` e `.cdr` temporários não estão rastreados no Git.
5. **Atualização do CHANGELOG:**
   - Registrar as novas funcionalidades, melhorias e correções na versão correspondente.
6. **Emissão de Relatório de Conclusão de Fase:**
   - Gerar o documento de relatório (ex: `docs/PHASE-X-REPORT.md`) resumindo as evidências e pendências.
7. **Solicitação de Aprovação Humana:**
   - Parar a execução e submeter formalmente o relatório para a aprovação explícita do operador humano.

---

## 5. Ferramentas Permitidas
- `git status`, `git diff`.
- `pytest`, linters de código.
- Validador JSON.

---

## 6. Saídas Esperadas
- Relatório de encerramento de fase publicado e checklist 100% assinado.

---

## 7. Validações
- 0 testes falhando.
- 0 segredos versionados no repositório.
- Nenhuma funcionalidade não testada declarada como funcional.

---

## 8. Falhas Comuns e Ações Corretivas
- **Falha:** Arquivo de log com caminho local de usuário commitado por engano.
  - *Ação:* Remover o arquivo do Git antes de gerar a release e adicionar o padrão ao `.gitignore`.
- **Falha:** Tentar avançar de fase sem aprovação humana registrada.
  - *Ação:* Bloquear a transição e solicitar a autorização expressa no chat.

---

## 9. Exemplo de Checklist de Liberação
- [x] Estrutura de pastas íntegra
- [x] Schemas JSON validados
- [x] Testes unitários passando
- [x] Threat model atualizado
- [x] Sem código funcional prematuro na Fase 0
- [ ] Autorização explícita do operador concedida

---

## 10. Critérios de Sucesso
- Liberação assinada por todos os agentes competentes e autorizada pelo operador humano.
