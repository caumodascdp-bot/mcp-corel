# Testes de Segurança: `tests/security/`

## Escopo
Suíte de testes de penetração e auditoria automatizada que submete o sistema a cenários adversos:
- Tentativas de evasão de diretório (*Path Traversal*) com sequências como `../../Windows/System32`.
- Mensagens do Telegram simuladas vindas de IDs de usuário não autorizados.
- Injeção de caracteres de controle e strings maliciosas em parâmetros de texto.
- Verificação de mascaramento de segredos em logs e saídas de erro.
