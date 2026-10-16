# ADR-005: Armazenamento e Isolamento de Artefatos Gráficos

- **Status:** Proposto (*proposed*)
- **Data:** 2026-10-16
- **Autores:** Product Architect, Security Reviewer, Layout Engineer

---

## 1. Contexto
A operação gráfica gera arquivos volumosos e de naturezas distintas:
1. Documentos mestres vetoriais (`.cdr`).
2. Imagens temporárias de prévia para o chat e IA (`.png`, `.jpg`).
3. Arquivos fechados finais para impressão (`.pdf`, `.eps`).
4. Imagens importadas (logos, fotos) fornecidas pelo cliente.
Sem uma padronização rígida, há risco de dispersão de arquivos pelo computador, invasão de diretórios de sistema operacional e perda de arquivos originais de clientes.

---

## 2. Decisão Proposta
Propõe-se estruturar o armazenamento local sob uma **Raiz Segura Dedicada (`SAFE_STORAGE_ROOT`)**, subdividida em três pastas com políticas de retenção distintas:
- `data/artifacts/`: Destinada aos documentos mestres `.cdr` e PDFs fechados de entrega. **Nunca expurgada automaticamente.** Sobrescrita de arquivos existentes requer autorização explícita.
- `data/previews/`: Destinada exclusivamente a renderizações temporárias de inspeção visual. Elegível para limpeza automática após 7 dias.
- `data/jobs/`: Destinada ao banco de dados SQLite e manifestos JSON de tarefas.

Todo acesso a arquivos passa obrigatoriamente por um verificador de caminhos (*Path Sanitizer*) que resolve links simbólicos, valida o caminho canônico e bloqueia qualquer tentativa de escrita fora da raiz segura.

---

## 3. Alternativas Avaliadas

### Alternativa 1: Estrutura Padronizada com Raiz Segura (Opção Escolhida)
- **Vantagens:** Organização clara; prevenção absoluta de vulnerabilidades de *Path Traversal*; facilidade de backup para o operador humano (basta copiar a pasta `data/`).
- **Desvantagens:** Requer configuração de caminho absoluto na variável de ambiente do Windows.

### Alternativa 2: Permitir Qualquer Caminho Escolhido pelo Usuário na Estação
- **Vantagens:** Flexibilidade para salvar arquivos em qualquer pasta do computador (ex: Desktop, Meus Documentos).
- **Desvantagens:** Risco severo de segurança; o agente de IA ou um invasor no Telegram poderia sobrescrever arquivos do sistema operacional ou documentos pessoais confidenciais. Rejeitado.

---

## 4. Consequências

### Consequências Positivas:
- Segurança blindada contra acesso indevido ao sistema de arquivos do Windows.
- Separação clara entre arquivos descartáveis de teste e arquivos de produção dos clientes.

### Consequências Negativas:
- Arquivos de imagem que o usuário desejar importar para a arte precisam ser copiados previamente para o diretório autorizado.
