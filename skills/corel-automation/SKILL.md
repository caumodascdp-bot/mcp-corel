---
name: corel-automation
description: Procedimento controlado de automação do CorelDRAW 2023 via COM, com tratamento de timeout, supressão de modais e gravação atômica.
---

# Procedimento Operacional: Automação Controlada do CorelDRAW

## 1. Objetivo
Executar comandos de manipulação gráfica no CorelDRAW 2023 instalado localmente através da interface COM, mantendo isolamento de processos, tratamento de travamentos por timeout e supressão de janelas modais.

---

## 2. Quando Usar
- Durante a execução de tarefas que criem, editem, salvem ou exportem documentos no CorelDRAW.
- Durante a rotina de diagnóstico de saúde da aplicação.

---

## 3. Entradas Necessárias
- Parâmetros da chamada COM (ex: dimensões do documento, tipo de forma, texto a inserir, caminho do arquivo).
- Tempo limite configurado (padrão: 60 segundos).

---

## 4. Procedimento Passo a Passo
1. **Verificar Estado da Instância:**
   - Conferir se o processo `CorelDRW.exe` está ativo.
   - Conectar ao objeto COM via `win32com.client.Dispatch("CorelDRAW.Application")`.
2. **Ativar Supressão de Mensagens:**
   - Configurar a propriedade de otimização da aplicação (`Application.Optimization = True`) para evitar diálogos gráficos bloqueantes e aceleração de tela.
3. **Iniciar o Watchdog de Timeout:**
   - Disparar cronômetro assíncrono de 60 segundos para abortar a chamada caso o Corel trave.
4. **Executar a Operação Gráfica:**
   - Criar documento, desenhar objetos ou inserir textos conforme o plano vetorial.
5. **Finalizar Otimização e Forçar Redesenho:**
   - Restaurar `Application.Optimization = False` e atualizar a visualização.
6. **Gerar Prévia Imediata ou Salvar:**
   - Exportar a imagem de prévia (PNG a 150 DPI) na pasta `data/previews/` ou salvar o arquivo `.cdr` em `data/artifacts/`.
7. **Tratar Exceções COM:**
   - Capturar erros HRESULT e mapear para códigos padronizados em `contracts/error-codes.md`.

---

## 5. Ferramentas Permitidas
- Adaptador COM oficial em `src/mcp_corel/corel/adapter.py`.
- Temporizador watchdog em `src/mcp_corel/corel/watchdog.py`.

---

## 6. Saídas Esperadas
- Documento alterado com sucesso no CorelDRAW.
- Arquivo de prévia renderizado no disco.
- Registro de sucesso com duração em milissegundos.

---

## 7. Validações
- O arquivo gerado existe no disco e possui tamanho maior que zero bytes.
- O tempo de execução não excedeu o limite de segurança configurado.

---

## 8. Falhas Comuns e Ações Corretivas
- **Falha:** Erro `0x8001010A` (Servidor de chamada ocupado).
  - *Ação:* Tentar novamente após 2 segundos (máximo 2 tentativas); se persistir, cancelar a tarefa e alertar o operador.
- **Falha:** Timeout de 60s atingido sem resposta.
  - *Ação:* Registrar `ERR_COREL_COM_TIMEOUT`, tentar cancelar o diálogo ativo e liberar a fila para o próximo job.

---

## 9. Exemplo Prático
```python
# Pseudocódigo ilustrativo do fluxo de automação segura
with COMWatchdog(timeout_seconds=60):
    app = get_corel_application()
    app.Optimization = True
    try:
        doc = app.CreateDocument()
        doc.Unit = cdrMillimeter
        shape = doc.ActiveLayer.CreateRectangle(0, 0, 100, 150)
        shape.Fill.UniformColor.CMYKAssign(0, 100, 100, 0)
    finally:
        app.Optimization = False
```

---

## 10. Critérios de Sucesso
- Ação executada no CorelDRAW e prévia gerada sem erros de COM ou travamento do sistema.
