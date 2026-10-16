# Testes de Integração: `tests/integration/`

> [!IMPORTANT]
> Esta suíte é marcada com o decorator `@pytest.mark.live_corel` e só pode ser executada em uma máquina física Windows 10 com o CorelDRAW 2023 instalado e ativado.

## Escopo
Valida a comunicação COM real (`win32com.client`), abertura e fechamento de instâncias do `CorelDRW.exe`, criação física de objetos na página, tempo de resposta do watchdog e geração de arquivos no disco.
