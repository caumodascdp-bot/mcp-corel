---
name: print-preflight
description: Inspeção técnica de pré-impressão gráfica validando sangrias, conversão em curvas, espaço de cor CMYK e resolução.
---

# Procedimento Operacional: Pré-Voo e Pré-Impressão (Preflight)

## 1. Objetivo
Garantir que os arquivos destinados à produção em gráficas comerciais ou gráficas rápidas estejam em estrita conformidade com as exigências técnicas da indústria de impressão, evitando retrabalho e desperdício de papel.

---

## 2. Quando Usar
- Antes de exportar o PDF definitivo para envio à gráfica.
- Ao executar a ferramenta MCP `corel.validate_artwork`.

---

## 3. Entradas Necessárias
- Documento CorelDRAW ativo com arte finalizada.
- Perfil de cor alvo (ex: Coated FOGRA39, ISO Coated v2 ou SWOP CMYK).
- Requisitos da gráfica (sangria padrão de 3mm a 5mm).

---

## 4. Procedimento Passo a Passo
1. **Verificação de Dimensões e Sangria (Bleed):**
   - Confirmar se o documento possui sangria ativada nas propriedades de página do CorelDRAW.
   - Conferir se elementos que encostam na borda avançam até o limite externo da sangria.
2. **Inspeção de Paleta de Cores:**
   - Detectar se há objetos vetoriais com preenchimento ou contorno em RGB.
   - Converter todas as cores do documento para CMYK.
   - Garantir que textos pequenos em preto utilizem Preto Puro (C:0 M:0 Y:0 K:100) para evitar registro duplo.
3. **Conversão de Fontes em Curvas:**
   - Selecionar todos os objetos de texto e executar o comando de conversão em curvas (`cdrConvertToCurves`).
   - Confirmar que a contagem de fontes ativas no documento seja igual a zero.
4. **Verificação de Resolução de Imagens:**
   - Percorrer todos os bitmaps importados e checar se o DPI efetivo é de no mínimo 300 DPI.
5. **Configuração de Exportação PDF/X:**
   - Configurar o filtro de publicação PDF para o padrão PDF/X-1a:2001 ou PDF/X-3 com marcas de corte ativadas.

---

## 5. Ferramentas Permitidas
- Ferramenta MCP `corel.validate_artwork`.
- Métodos COM de inspeção de propriedades de preenchimento e fontes.

---

## 6. Saídas Esperadas
- Relatório de conformidade de pré-impressão (`preflight_report.json`).
- Documento pronto para exportação com status `PREPRESS_VERIFIED`.

---

## 7. Validações
- 0 fontes pendentes no documento (todas convertidas em curvas).
- 0 objetos em RGB no documento de saída gráfica.
- Sangria de no mínimo 3mm configurada.

---

## 8. Falhas Comuns e Ações Corretivas
- **Falha:** Fonte tipográfica não encontrada na máquina do CorelDRAW.
  - *Ação:* Bloquear fechamento e alertar que a fonte deve ser instalada ou substituída antes da conversão.
- **Falha:** Bitmap com 150 DPI de resolução.
  - *Ação:* Alerta `ERR_PREPRESS_RESOLUTION_LOW` com solicitação de imagem em alta definição.

---

## 9. Exemplo Prático
```json
{
  "preflight_status": "APROVADO_COM_RESSALVAS",
  "detalhes": {
    "dimensoes_pagina_mm": "100 x 150",
    "sangria_mm": 3.0,
    "fontes_em_curvas": true,
    "espaco_cor": "CMYK",
    "menor_resolucao_bitmap_dpi": 280
  },
  "parecer": "Arte liberada para impressão comercial em offset ou laser digital."
}
```

---

## 10. Critérios de Sucesso
- Arquivo PDF gerado contendo marcas de corte precisas e 100% dos textos em vetores puros.
