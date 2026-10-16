# Módulo: `mcp_corel.corel`

## Finalidade
Responsável por toda a camada de comunicação de baixo nível com o software CorelDRAW 2023 no Windows 10 através do mecanismo COM (`win32com.client`).

## Componentes Planejados (Para Fase 1 em diante)
- `diagnostics.py`: Utilitário executável para inspecionar registros COM, caminhos de instalação e versões instaladas.
- `adapter.py`: Classe adaptadora de alto nível que expõe métodos tipados para criação de documentos, formas e exportações.
- `watchdog.py`: Monitor assíncrono que impõe timeout de 60 segundos em chamadas bloqueantes de COM.
- `exceptions.py`: Mapeamento de exceções HRESULT do Windows para códigos de erro da aplicação.
