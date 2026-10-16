# Suíte de Testes Automatizados — MCP Corel

Este diretório contém toda a infraestrutura de testes automatizados do projeto **MCP Corel**, organizada por camadas de validação.

---

## 1. Organização das Pastas de Teste

| Pasta | Nível de Teste | Ambiente de Execução | Precisa de CorelDRAW Físico? |
| :--- | :--- | :--- | :--- |
| **`tests/unit/`** | Testes Unitários | Linux / Windows | **NÃO** (Usa simulações/mocks) |
| **`tests/security/`**| Testes de Segurança | Linux / Windows | **NÃO** |
| **`tests/integration/`**| Testes de Integração COM | Windows 10 | **SIM** (CorelDRAW 2023 real) |
| **`tests/acceptance/`** | Testes de Aceitação E2E | Windows 10 | **SIM** |
| **`tests/fixtures/`** | Arquivos de Amostra e Dados | Linux / Windows | N/A |

---

## 2. Como Rodar os Testes

```bash
# Rodar testes que não exigem Windows nem CorelDRAW:
pytest tests/unit/ tests/security/ -v

# Rodar testes reais no Windows 10 com CorelDRAW aberto:
pytest tests/integration/ -m live_corel -v
```
