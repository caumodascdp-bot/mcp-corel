# Espaço de Trabalho: Qualidade e Segurança

Este documento orienta os procedimentos de garantia de qualidade (QA), testes automatizados, conformidade gráfica de pré-impressão e auditoria de segurança da informação.

---

## 1. Missão da Área
Garantir que nenhuma vulnerabilidade de segurança atinja o ambiente de execução e que nenhuma arte com defeitos gráficos, problemas de corte ou erros de cor seja entregue para produção.

---

## 2. Agentes Vinculados
- **Security Reviewer** (Auditoria de vulnerabilidades)
- **Test Engineer** (Testes automatizados e fixtures)
- **Visual QA** (Inspeção visual de prévias)
- **Prepress** (Conformidade gráfica para impressão)

---

## 3. Arquivos e Recursos Centrais
- [Política de Segurança](file:///home/kali/mcp-corel/SECURITY.md)
- [Modelo de Ameaças (Threat Model)](file:///home/kali/mcp-corel/docs/THREAT_MODEL.md)
- [Plano de Testes](file:///home/kali/mcp-corel/docs/TEST_PLAN.md)
- [Critérios de Aceite](file:///home/kali/mcp-corel/docs/ACCEPTANCE_CRITERIA.md)
- [Suíte de Testes Automatizados](file:///home/kali/mcp-corel/tests/)

---

## 4. Regras e Restrições Vigentes
1. Todo caminho de arquivo recebido deve ser validado contra escape de diretório (`Path Traversal`).
2. Proibido permitir abertura de portas públicas de entrada no Windows.
3. Proibido aprovar artes para gráfica que contenham imagens com menos de 300 DPI ou textos em RGB.
4. O Security Reviewer possui poder de veto irrecorrível sobre qualquer alteração arriscada.
