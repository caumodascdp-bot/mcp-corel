# ADR-004: Fila de Tarefas e Persistência de Estado com SQLite

- **Status:** Proposto (*proposed*)
- **Data:** 2026-10-16
- **Autores:** Product Architect, Orchestrator, Corel Automation

---

## 1. Contexto
A automação gráfica no CorelDRAW 2023 através da interface COM não suporta chamadas concorrentes assíncronas na mesma instância da aplicação (duas threads tentando manipular documentos ao mesmo tempo causam corrupção de memória ou exceção RPC). Além disso, as tarefas precisam ter seus estados preservados caso o sistema reinicie, permitindo auditoria, histórico de prévias e retomada ordenada.

---

## 2. Decisão Proposta
Propõe-se adotar uma **fila serializada de tarefas persistida em banco de dados local SQLite** configurado em modo **WAL (Write-Ahead Logging)**, utilizando a biblioteca assíncrona `aiosqlite`.

Toda operação de modificação vetorial no CorelDRAW passará por um despachante de fila única (worker serial), garantindo que apenas 1 comando gráfico seja executado por vez. A máquina de estados suportará os estados: `queued`, `running`, `needs_approval`, `succeeded`, `failed`, `cancelled`.

---

## 3. Alternativas Avaliadas

### Alternativa 1: SQLite em modo WAL (Opção Escolhida)
- **Vantagens:** Embutido no Python (zero serviços externos para instalar no Windows); persistência transacional sólida; modo WAL permite leituras simultâneas enquanto gravações ocorrem sem bloqueio de banco.
- **Desvantagens:** Não é distribuído (mas o projeto roda em uma única máquina física, tornando isso irrelevante).

### Alternativa 2: Fila em Memória (Python `asyncio.Queue`)
- **Vantagens:** Altíssima velocidade; código simples.
- **Desvantagens:** Se o computador reiniciar ou o processo cair, todas as tarefas pendentes e o histórico de aprovação são perdidos instantaneamente. Rejeitado.

### Alternativa 3: Redis / RabbitMQ
- **Vantagens:** Recursos avançados de filas distribuídas e mensageria.
- **Desvantagens:** Requer instalação e manutenção de serviços externos adicionais no Windows, elevando desnecessariamente a complexidade de instalação para o usuário final. Rejeitado.

---

## 4. Consequências

### Consequências Positivas:
- Histórico completo de criação de artes auditável a qualquer momento.
- Recuperação automática de tarefas interrompidas na inicialização do sistema.
- Zero dependências de serviços externos instalados no sistema operacional.

### Consequências Negativas:
- Pequeno overhead de I/O em disco a cada transição de estado da tarefa.
