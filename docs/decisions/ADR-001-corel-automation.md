# ADR-001: Mecanismo de Automação do CorelDRAW 2023

- **Status:** Proposto (*proposed*)
- **Data:** 2026-10-16
- **Autores:** Product Architect, Corel Automation Engineer

---

## 1. Contexto
O objetivo central do sistema é permitir que agentes de IA manipulem documentos vetoriais no CorelDRAW 2023 em ambiente Windows 10. O CorelDRAW tradicionalmente expõe interfaces de automação via Component Object Model (COM), além de suportar macros em VBA (Visual Basic for Applications) e VSTA. No entanto, o estado da instalação na máquina alvo ainda não foi verificado e não sabemos se todos os componentes de automação estão habilitados na licença instalada.

---

## 2. Decisão Proposta
Propõe-se adotar como mecanismo primário de automação a **interface COM nativa do Windows via biblioteca Python `pywin32` (`win32com.client`)**, encapsulada em um adaptador de alto nível com monitoramento de timeout (watchdog).

Caso o diagnóstico da Fase 1 comprove que o servidor COM não está disponível ou está restrito na edição instalada, a decisão será revista para utilizar o **Fallback A (Geração de arquivos vetoriais abertos como SVG/PDF combinada com importação e manipulação via script)**.

---

## 3. Alternativas Avaliadas

### Alternativa 1: Automação COM via `pywin32` (Opção Escolhida)
- **Vantagens:** Comunicação direta em memória, controle detalhado de propriedades de formas, textos, camadas, paletas de cores e filtros de exportação sem necessidade de macros pré-instaladas.
- **Desvantagens:** Específico para Windows, sensível a travamentos por diálogos modais não tratados.

### Alternativa 2: Execução de Macros VBA Externas (.gms / .bas)
- **Vantagens:** Executa muito rápido dentro do processo interno do CorelDRAW.
- **Desvantagens:** Requer instalação manual de arquivos `.gms` na pasta do CorelDRAW pelo usuário; maior risco de segurança se macros forem modificadas dinamicamente.

### Alternativa 3: Automação Baseada em Interface Gráfica (pywinauto / pyautogui)
- **Vantagens:** Funciona em qualquer versão do CorelDRAW sem depender de COM.
- **Desvantagens:** Extremamente instável; qualquer mudança de resolução de tela, posição de janelas ou idioma da interface quebra a automação. Descartado para produção.

---

## 4. Consequências

### Consequências Positivas:
- Controle paramétrico milimétrico das artes.
- Possibilidade de suprimir diálogos e capturar erros estruturados via HRESULT do Windows.
- Não exige compilação de código binário C++ complexo.

### Consequências Negativas / Riscos:
- Exige que o Python e o CorelDRAW rodem na mesma arquitetura de bits (recomendado 64-bit).
- Dependência estrita do diagnóstico prático na máquina alvo na Fase 1.
