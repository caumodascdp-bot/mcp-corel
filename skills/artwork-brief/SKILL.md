---
name: artwork-brief
description: Transforma pedidos informais de clientes em briefings estruturados em JSON validados contra o schema oficial.
---

# Procedimento Operacional: Elaboração de Briefing de Arte

## 1. Objetivo
Estruturar pedidos em linguagem natural enviados por usuários em documentos JSON canônicos e livres de ambiguidades, definindo dimensões físicas, paleta de cores, tipografia e textos literais.

---

## 2. Quando Usar
- Ao receber uma nova solicitação de criação de arte gráfica via cliente MCP ou Telegram.
- Ao iniciar a concepção de um novo documento no CorelDRAW.

---

## 3. Entradas Necessárias
- Texto do pedido enviado pelo usuário (ex: tipo de arte, tema, informações de contato).
- Tipo de saída pretendida: Impressão física (ex: flyer, crachá, cartão) ou Mídia Digital (ex: feed, story).
- Elementos obrigatórios (logotipo, texto de título, telefone, endereço).

---

## 4. Procedimento Passo a Passo
1. **Identificar a Finalidade da Arte:** Determinar se a saída será física (requer espaço de cor CMYK e sangria) ou digital (requer RGB e dimensões em pixels).
2. **Definir as Dimensões Canônicas:** Estabelecer largura e altura em milímetros (ex: 100 x 150 mm para folheto).
3. **Extrair Textos Literais:** Separar o título principal, subtítulo, corpo de texto e dados de contato exatamente como o cliente solicitou.
4. **Definir Paleta de Cores e Estilo:** Escolher cores primária, secundária e de fundo com bom contraste visual.
5. **Preencher o JSON de Briefing:** Gerar o documento de acordo com a especificação em `contracts/artwork-brief.schema.json`.
6. **Validar contra o Schema:** Executar validação sintática do JSON gerado.

---

## 5. Ferramentas Permitidas
- Leitor/validador JSON contra `contracts/artwork-brief.schema.json`.
- Modelos de referência em `templates/`.

---

## 6. Saídas Esperadas
- Arquivo `data/jobs/briefing_<job_id>.json` contendo o briefing estruturado e validado.

---

## 7. Validações
- O JSON não contém campos obrigatórios vazios ou nulos.
- O formato de cor é estritamente `"CMYK"` ou `"RGB"`.
- As dimensões são números positivos maiores que zero.

---

## 8. Falhas Comuns e Ações Corretivas
- **Falha:** Cliente não informou as medidas do panfleto.
  - *Ação:* Adotar o padrão de mercado A6 (105 x 148 mm) ou consultar o operador antes de prosseguir.
- **Falha:** Texto solicitado muito longo para a área da arte.
  - *Ação:* Alerta de excesso de conteúdo ao Art Director para resumir os tópicos.

---

## 9. Exemplo Prático
```json
{
  "$schema": "../../contracts/artwork-brief.schema.json",
  "title": "Flyer Promocional de Pizzaria",
  "target_medium": "print",
  "dimensions": {
    "width_mm": 100.0,
    "height_mm": 150.0,
    "bleed_mm": 3.0,
    "safety_margin_mm": 5.0
  },
  "color_profile": "CMYK",
  "content": {
    "headline": "Grande Inauguração!",
    "subheadline": "20% de Desconto em Todas as Pizzas",
    "call_to_action": "Peça pelo WhatsApp: (11) 99999-9999"
  }
}
```

---

## 10. Critérios de Sucesso
- Briefing JSON salvo e validado com 0 erros de validação de schema.
