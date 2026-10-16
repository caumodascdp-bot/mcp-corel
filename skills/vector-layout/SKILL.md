---
name: vector-layout
description: Converte um briefing estruturado em um plano geométrico de coordenadas, caixas delimitadoras e ordenação de camadas.
---

# Procedimento Operacional: Engenharia de Leiaute Vetorial

## 1. Objetivo
Mapear matematicamente cada elemento visual do briefing para o plano cartesiano bidimensional do CorelDRAW, calculando posições (x, y), larguras, alturas, alinhamentos e camadas.

---

## 2. Quando Usar
- Após a validação do briefing estruturado pelo Art Director.
- Sempre que houver necessidade de reorganizar a distribuição dos elementos na página.

---

## 3. Entradas Necessárias
- Briefing estruturado validado (`artwork-brief.schema.json`).
- Dimensões do documento e margens de corte (sangria e margem de segurança).

---

## 4. Procedimento Passo a Passo
1. **Configurar a Grade Modular (Grid):** Dividir a página útil em colunas e linhas mestras para distribuição harmoniosa.
2. **Definir a Camada de Fundo (Background):**
   - Criar retângulo cobrindo a área da página acrescida da sangria (ex: largura + 2*sangria, altura + 2*sangria).
3. **Calcular Caixas Delimitadoras de Textos:**
   - Posicionar a caixa do título principal no terço superior da área de segurança.
   - Posicionar o corpo de texto ou ofertas no terço médio.
   - Posicionar os dados de contato e chamadas no terço inferior.
4. **Calcular o Posicionamento de Logotipos / Imagens:**
   - Garantir preservação da proporção original da imagem (aspect ratio).
   - Centralizar ou alinhar à margem esquerda de segurança.
5. **Definir a Sequência de Camadas (Z-Index):**
   - Camada 0: Fundo e texturas decorativas.
   - Camada 1: Formas vetoriais secundárias e fotos.
   - Camada 2: Textos informativos e títulos.
   - Camada 3: Linha de corte e guias técnicas de faca (se aplicável).

---

## 5. Ferramentas Permitidas
- Fórmulas de cálculo de coordenadas cartesianas no padrão milimétrico do CorelDRAW.
- Validação de sobreposição de retângulos (*collision detection*).

---

## 6. Saídas Esperadas
- Plano de execução vetorial detalhado com parâmetros prontos para despacho ao Corel Automation.

---

## 7. Validações
- Nenhum texto essencial possui coordenadas dentro da faixa da margem de segurança (mínimo 5mm da borda final).
- O fundo se estende perfeitamente até o limite da sangria externa.

---

## 8. Falhas Comuns e Ações Corretivas
- **Falha:** Texto calculado fora dos limites visíveis da página.
  - *Ação:* Reduzir tamanho da fonte em pontos ou recalcular quebra de linha automática.
- **Falha:** Sobreposição entre o logotipo e o título principal.
  - *Ação:* Aumentar o espaçamento vertical (espaço entre caixas delimitadoras) em 10mm.

---

## 9. Exemplo Prático
Para página A6 (105 x 148 mm) com sangria de 3mm:
- Dimensão do Retângulo de Fundo: 111 x 154 mm, posicionado em (-3mm, -3mm).
- Caixa do Título: Largura 95mm, Altura 25mm, posicionada em (5mm, 115mm).
- Caixa do Contato: Largura 95mm, Altura 15mm, posicionada em (5mm, 10mm).

---

## 10. Critérios de Sucesso
- Plano vetorial 100% livre de colisões espúrias e com todas as coordenadas dentro dos limites de produção.
