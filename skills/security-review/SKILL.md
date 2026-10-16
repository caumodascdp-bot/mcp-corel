---
name: security-review
description: Roteiro de auditoria de segurança de código, permissões de arquivos, sanitização de caminhos e prevenção de vulnerabilidades.
---

# Procedimento Operacional: Auditoria de Segurança (Security Review)

## 1. Objetivo
Estabelecer um roteiro sistemático de revisão de segurança para qualquer código, ferramenta MCP ou alteração de infraestrutura no ecossistema **MCP Corel**, prevenindo riscos de execução arbitrária, path traversal ou vazamento de segredos.

---

## 2. Quando Usar
- Antes de mesclar qualquer PR de código novo.
- Ao adicionar novas ferramentas MCP que manipulem caminhos em disco ou executem chamadas no Windows.
- Periodicamente antes de marcos e releases de versão.

---

## 3. Entradas Necessárias
- Diff ou arquivos de código-fonte modificados.
- Especificação da ferramenta ou funcionalidade proposta.
- Modelo de Ameaças (`docs/THREAT_MODEL.md`).

---

## 4. Procedimento Passo a Passo
1. **Auditoria de Manipulação de Arquivos e Caminhos:**
   - Inspecionar todas as chamadas de abertura, leitura ou escrita em disco (`open`, `os.path`, `shutil`).
   - Conferir se todas as entradas de caminhos passam obrigatoriamente por `PathSanitizer.resolve_safe_path()`.
   - Garantir que caminhos com `..`, barras invertidas duplas ou caminhos absolutos do sistema (ex: `C:\Windows`) sejam bloqueados.
2. **Auditoria de Chamadas de Shell e Subprocessos:**
   - Verificar se há chamadas do tipo `subprocess.Popen(..., shell=True)` ou `os.system`.
   - **Regra:** `shell=True` é terminantemente proibido em todo o repositório.
3. **Auditoria de Injeção de Macros:**
   - Conferir se nenhum método aceita código VBA/VSTA em string para compilação dinâmica em tempo de execução.
4. **Varredura de Segredos e Credenciais:**
   - Procurar por tokens, senhas ou chaves de API hardcoded no código ou em mensagens de log.
   - Conferir se `.env` permanece estritamente listado no `.gitignore`.
5. **Auditoria de Autenticação Remota:**
   - Verificar se o worker do Telegram valida o ID de usuário antes de qualquer processamento de comando.
6. **Emissão do Relatório de Auditoria:**
   - Emitir parecer conclusivo: `SECURITY_PASS` (autorizado) ou `SECURITY_BLOCK` (bloqueado com lista de correções).

---

## 5. Ferramentas Permitidas
- Linters de segurança estática (ex: Bandit, Ruff).
- Suíte automatizada de testes de penetração `tests/security/`.

---

## 6. Saídas Esperadas
- Parecer formal de segurança registrado na issue ou pull request.

---

## 7. Validações
- 100% dos testes da suíte `tests/security/` passam sem falhas.
- 0 violações de segurança apontadas pelos linters estáticos.

---

## 8. Falhas Comuns e Ações Corretivas
- **Falha:** Uso de `os.path.join(base_dir, user_input)` sem validação de caminho absoluto (se `user_input` for `C:\arquivo.txt`, o Windows ignora `base_dir`).
  - *Ação:* Substituir por função segura que resolve o caminho canônico e confere se começa com `SAFE_STORAGE_ROOT`.
- **Falha:** Token do bot impresso em log de inicialização.
  - *Ação:* Mascarar a string exibindo apenas os primeiros e últimos 4 caracteres (ex: `1234****abcd`).

---

## 9. Exemplo Prático de Validação de Caminho
```python
# Padrão obrigatório de verificação de caminho seguro:
def resolve_safe_path(user_path: str, safe_root: Path) -> Path:
    target = (safe_root / user_path).resolve()
    if not target.is_relative_to(safe_root.resolve()):
        raise SecurityException("ERR_SECURITY_PATH_OUT_OF_BOUNDS: Tentativa de escape detectada.")
    return target
```

---

## 10. Critérios de Sucesso
- Código aprovado sem nenhum apontamento crítico de segurança aberto.
