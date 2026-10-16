---
name: visual-qa
description: Roteiro rigoroso de conferência visual e identificação de defeitos em imagens de prévia renderizadas pelo CorelDRAW.
---

# Procedimento Operacional: Garantia da Qualidade Visual (Visual QA)

## 1. Objetivo
Auditar sistematicamente as imagens de prévia exportadas pelo CorelDRAW para identificar defeitos visuais objetivos antes que a arte seja submetida à aprovação do cliente ou enviada para impressão.

---

## 2. Quando Usar
- Imediatamente após a geração de uma prévia de imagem pela ferramenta `corel.preview`.
- Antes de disparar mensagens com fotos para o Telegram ou avançar para `needs_approval`.

---

## 3. Entradas Necessárias
- Arquivo de imagem da prévia (`data/previews/preview_<job_id>.png`).
- Briefing original do cliente (`artwork-brief.schema.json`).

---

## 4. Procedimento Passo a Passo
1. **Inspeção de Integridade da Imagem:**
   - Conferir se a imagem foi renderizada por completo (sem áreas cinzas de renderização corrompida).
2. **Conferência de Textos Literais:**
   - Ler os textos renderizados na imagem e comparar caractere a caractere com o briefing (telefones, nomes próprios, preços).
3. **Detecção de Colisões e Sobreposições:**
   - Verificar se há textos colidindo com logotipos, caixas vetoriais ou bordas da imagem.
4. **Verificação de Margem de Segurança:**
   - Confirmar visualmente se nenhum elemento crítico está excessivamente colado à linha de corte da página.
5. **Auditoria de Contraste e Legibilidade:**
   - Avaliar se a cor do texto contrasta suficientemente com a cor de fundo (ex: texto amarelo em fundo branco é inaceitável).
6. **Emissão do Parecer Técnico:**
   - Se 100% aprovado: Emitir parecer `VISUAL_APPROVED`.
   - Se houver defeitos: Listar cada defeito com coordenadas aproximadas e gravidade.

---

## 5. Ferramentas Permitidas
- Inspeção de pixels e canais de cor via Pillow.
- Leitura comparativa de strings contra o briefing original.

---

## 6. Saídas Esperadas
- Relatório de auditoria visual (`data/jobs/qa_report_<job_id>.json`).

---

## 7. Validações
- Nenhuma discrepância entre o número de contato do briefing e o impresso na arte.
- Taxa de contraste estimada superior a 4.5:1 para textos informativos.

---

## 8. Falhas Comuns e Ações Corretivas
- **Falha:** Dígito do telefone cortado pela borda direita.
  - *Ação:* Reprovar e instruir o Layout Engineer a deslocar a caixa de texto 10mm para a esquerda.
- **Falha:** Texto escuro sobreposto a foto escura de fundo.
  - *Ação:* Reprovar e solicitar inclusão de caixa semi-opaca de contraste ou inversão para texto branco.

---

## 9. Exemplo de Relatório de QA
```json
{
  "job_id": 104,
  "status": "REPROVADO",
  "issues": [
    {
      "severidade": "alta",
      "tipo": "colisao_borda",
      "descricao": "Texto de contato a apenas 1mm da borda inferior (margem de segurança violada)"
    }
  ],
  "recomendacao": "Recalcular posição vertical do bloco de contato para Y = 12mm"
}
```

---

## 10. Critérios de Sucesso
- Arte inspecionada e aprovada com conformidade visual total atestada pelo relatório.
