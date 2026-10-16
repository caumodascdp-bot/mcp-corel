# Briefing do Produto — MCP Corel

## 1. Visão do Produto
Permitir que operadores humanos e agentes autônomos de Inteligência Artificial comandem de forma natural, robusta e segura uma instância local do **CorelDRAW 2023** em uma máquina Windows 10, automatizando desde a concepção gráfica vetorial inicial até o fechamento de arquivos para impressão e mídias sociais.

---

## 2. O Problema
Atualmente, a criação e edição gráfica vetorial de precisão no CorelDRAW exige operação manual constante de designers em tarefas repetitivas (redimensionamento de cartazes, ajustes de textos em dezenas de crachás, aplicação de marcas d'água, exportação de provas para clientes, fechamento de sangrias e pré-impressão). Além disso, a maioria dos modelos de linguagem opera em nuvem e não tem acesso seguro ao ecossistema gráfico desktop local.

---

## 3. A Solução
O **MCP Corel** atua como uma ponte padronizada (Model Context Protocol) que expõe ferramentas controladas para os agentes de IA:
- Criação e configuração de documentos gráficos (.CDR) com dimensões milimétricas precisas.
- Inserção e formatação de formas geométricas, textos artísticos e de parágrafo.
- Importação de logotipos e imagens externas mantendo proporção e posicionamento.
- Alinhamento, distribuição e agrupamento paramétrico de objetos.
- Geração instantânea de prévias visuais em imagem (PNG) para que a IA e o usuário possam inspecionar a arte.
- Exportação automatizada para padrões de mercado (PDF pré-impressão com marcas de corte, EPS, SVG e PNG).
- Canal remoto seguro via bot do Telegram com autenticação por ID numérico e aprovação de etapas.

---

## 4. Personas e Cenários de Uso

### Persona A: O Agente de IA (AntiGravity, Claude Code, Codex)
- **Necessidade:** Executar ações gráficas com garantia de tipos, parâmetros claros em milímetros/pontos, retorno imediato de erros e links para imagens de prévia.

### Persona B: O Operador / Designer Humano Remoto
- **Necessidade:** Solicitar alterações ou acompanhar o andamento pelo celular (via Telegram) enquanto o computador de trabalho no escritório processa as artes pesadas no CorelDRAW.

### Persona C: O Responsável por Pré-Impressão / Gráfica Rápida
- **Necessidade:** Garantir que o PDF final atenda estritamente aos requisitos técnicos: sangria correta, fontes convertidas em curvas ou incorporadas, resolução de imagens acima de 300 DPI e cores em CMYK.

---

## 5. Escopo e Fora de Escopo

### No Escopo (Fases 0 a 4)
- Controle de 1 instância local do CorelDRAW 2023 no Windows 10.
- Servidor MCP local rodando via `stdio`.
- Fila de tarefas serializada com persistência em SQLite.
- Bot Telegram complementar com long polling de saída.
- Validação de regras de pré-impressão e geração de prévias PNG.

### Fora de Escopo (Explicitamente Excluído)
- CorelDRAW em servidores Linux ou em contêineres Docker (o CorelDRAW exige Windows nativo com GUI).
- Servidor web com portas públicas abertas na internet no computador local.
- Edição de efeitos 3D complexos ou malhas de preenchimento vetoriais ultra-avançadas no MVP.
- Burlar proteções ou executar instâncias piratas do CorelDRAW.
- Edição simultânea concorrente no mesmo arquivo CDR.

---

## 6. Métricas de Sucesso do Produto
1. **Taxa de Sucesso de Execução:** Mais de 95% das instruções vetoriais válidas devem ser renderizadas sem erro de COM.
2. **Tempo de Geração de Prévia:** Uma prévia PNG de baixa resolução deve ser gerada e disponibilizada em menos de 5 segundos após a finalização do desenho.
3. **Segurança Absoluta de Arquivos:** 0 incidentes de sobrescrita acidental de arquivos não autorizados ou travamentos silenciosos da aplicação.
