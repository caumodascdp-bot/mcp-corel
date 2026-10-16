# Módulo: `mcp_corel.storage`

## Finalidade
Gerencia o armazenamento local de arquivos, organização de pastas de artefatos (`data/artifacts/`), prévias (`data/previews/`) e geração do manifesto criptográfico de entrega (`artifact-manifest.schema.json`).

## Componentes Planejados (Para Fase 2 em diante)
- `manifest.py`: Construtor e leitor de manifestos de entrega com cálculo de hash SHA-256.
- `retention.py`: Rotina de expurgo de imagens temporárias de prévia com mais de 7 dias.
