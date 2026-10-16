# Módulo: `mcp_corel.artwork`

## Finalidade
Modelos de dados canônicos e representação matemática de formas vetoriais, caixas delimitadoras (*bounding boxes*), cores (CMYK e RGB), fontes tipográficas e regras de transformação geométrica.

## Componentes Planejados (Para Fase 3 em diante)
- `models.py`: Classes Pydantic para representação de retângulos, elipses, caminhos e caixas de texto.
- `geometry.py`: Funções para cálculo de interseções, centralização e conversão de unidades (mm para pt).
- `color.py`: Utilitários de manipulação de espaços de cores (RGB, CMYK, Pantone/Spot).
