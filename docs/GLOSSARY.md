# Glossário de Termos Técnicos — MCP Corel

Este documento define os termos técnicos, siglas e conceitos utilizados no projeto **MCP Corel**, facilitando a compreensão entre desenvolvedores, operadores gráficos e agentes de IA.

---

## 1. Termos de Inteligência Artificial e Protocolos

- **MCP (Model Context Protocol):** Padrão aberto de comunicação desenvolvido pela Anthropic que permite que modelos de linguagem (LLMs) descubram e utilizem ferramentas (*tools*), leiam recursos e acessem contextos externos de forma padronizada.
- **stdio (Standard Input / Output):** Mecanismo de comunicação interprocessos padrão no qual o cliente de IA envia comandos pela entrada padrão (`stdin`) do servidor e lê as respostas pela saída padrão (`stdout`).
- **Prompt:** Instrução textual fornecida pelo usuário à IA descrevendo a arte desejada ou a ação a ser executada.
- **Briefing Estruturado:** Documento JSON padronizado que traduz a ideia do usuário em especificações exatas (dimensões, cores, fontes, textos literais).

---

## 2. Termos do Windows e Automação Desktop

- **COM (Component Object Model):** Tecnologia da Microsoft que permite a programas externos (como scripts Python) interagirem e comandarem aplicações instaladas no Windows (como o CorelDRAW).
- **ProgID (Programmatic Identifier):** Identificador legível registrado no Windows Registry que referencia um servidor COM. Exemplo: `CorelDRAW.Application.24`.
- **pywin32:** Biblioteca Python oficial que provê acesso às APIs do Windows e à interface COM (`win32com.client`).
- **Watchdog:** Mecanismo temporizador em segundo plano encarregado de interromper chamadas de automação que excedam o tempo limite de segurança (timeout).
- **Janela Modal (Modal Dialog):** Caixa de diálogo aberta por um programa que bloqueia qualquer outra interação até que o usuário clique em um botão (ex: aviso de fonte ausente).

---

## 3. Termos de Design Gráfico e Pré-Impressão

- **CDR:** Formato de arquivo proprietário nativo do CorelDRAW que preserva todas as propriedades vetoriais, camadas, textos editáveis e efeitos.
- **Sangria (Bleed):** Margem externa de segurança (comumente 3mm a 5mm além da linha de corte) onde o fundo da arte deve ser estendido para evitar bordas brancas indesejadas após o refile do papel na gráfica.
- **Margem de Segurança:** Espaço interno entre os elementos importantes (textos, logotipos) e a linha de corte da página para garantir que nada seja cortado por erro de alinhamento na guilhotina.
- **Preflight (Pré-impressão):** Processo automatizado de inspeção técnica de um documento gráfico antes do envio para a gráfica (checagem de resolução de imagens, formato de cor e fontes).
- **Converter em Curvas:** Processo de transformar caracteres de texto tipográfico em formas vetoriais estáticas, eliminando a dependência de fontes instaladas na gráfica.
- **CMYK (Ciano, Magenta, Amarelo e Preto):** Modelo de cor subtrativo utilizado em processos de impressão gráfica.
- **RGB (Vermelho, Verde e Azul):** Modelo de cor aditivo utilizado para visualização em telas digitais e na web.
- **DPI (Dots Per Inch / Pontos por Polegada):** Medida de densidade de resolução de imagens impressas. Padrão para impressão gráfica profissional é de no mínimo 300 DPI.

---

## 4. Termos de Rede e Segurança

- **Long Polling:** Técnica de comunicação web na qual o cliente faz uma requisição HTTP de saída para o servidor e aguarda que uma nova mensagem fique disponível antes de responder, dispensando a abertura de portas de entrada no roteador local.
- **Allowlist (Lista de Permissões):** Lista explícita de identificadores autorizados (como IDs numéricos do Telegram) com acesso concedido ao sistema; qualquer identificador fora da lista é bloqueado.
- **Path Traversal:** Tipo de vulnerabilidade de segurança onde um usuário tenta acessar arquivos fora do diretório autorizado usando sequências como `../` ou caminhos absolutos do sistema operacional.
- **SQLite WAL (Write-Ahead Logging):** Modo de operação de banco de dados no qual as alterações são registradas em um arquivo de log sequencial, permitindo que leituras e escritas ocorram simultaneamente sem travamento.
