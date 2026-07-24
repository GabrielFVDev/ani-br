<h1 align="center">
<img alt="Ani BR" src="https://img.shields.io/badge/%F0%9F%93%BA%20Ani%20BR-FF6B35?style=for-the-badge" height="50">
</h1>

<p align="center">
Assista anime <b>dublado e legendado em pt-br</b> direto do terminal, sem anúncio.
</p>

<p align="center">
Fork brasileiro do <a href="https://github.com/pystardust/ani-cli">ani-cli</a>, adaptado para raspar fontes nacionais.
</p>

<p align="center">
<img alt="Shell: POSIX sh" src="https://img.shields.io/badge/shell-POSIX%20sh-4EAA25?logo=gnubash&logoColor=white">
<img alt="Plataformas: Linux, macOS, Windows" src="https://img.shields.io/badge/plataformas-Linux%20%7C%20macOS%20%7C%20Windows-blue">
<img alt="Licença: GPL-3.0" src="https://img.shields.io/badge/license-GPL--3.0-lightgrey">
</p>

---

## Instalação

```sh
git clone https://github.com/GabrielFVDev/ani-br.git
cp ani-br/ani-br ~/.local/bin/     # ou: sudo cp ani-br/ani-br /usr/local/bin/
rm -rf ani-br
chmod +x ~/.local/bin/ani-br
```

**Dependências:** `curl`, `sed`, `grep`, `jq`, `fzf`, e um player (`mpv` recomendado).
**Opcionais:** `aria2c` (download mp4), `yt-dlp`/`ffmpeg` (download HLS), [`ani-skip`](https://github.com/synacktraa/ani-skip) (pular abertura, só mpv).

### Linux

```sh
sudo apt install curl sed grep jq fzf mpv     # Debian/Ubuntu
sudo pacman -S curl sed grep jq fzf mpv       # Arch
sudo dnf install curl sed grep jq fzf mpv     # Fedora
```

### macOS

```sh
brew install curl jq fzf ffmpeg aria2
brew install --cask iina   # ou mpv, vlc
```

O script detecta macOS via `uname` e usa o IINA automaticamente se instalado (`ANI_CLI_PLAYER` sobrescreve).

### Windows

Sem porta nativa — roda dentro de um ambiente POSIX:

- **WSL2** (recomendado): `wsl --install`, depois siga os comandos do Linux acima.
- **Git Bash/MSYS2**: instale o [MSYS2](https://www.msys2.org/), depois `pacman -S curl jq fzf sed grep mingw-w64-x86_64-mpv`.

Em ambos, a instalação do `ani-br` acima funciona sem alteração. O player precisa estar no `PATH` e o terminal precisa ter TTY (Windows Terminal e mintty funcionam; `cmd.exe` puro não).

## Uso

```sh
ani-br                      # busca interativa
ani-br naruto               # busca por "naruto"
ani-br --dub one piece      # só resultados dublados
ani-br -c                   # continuar do histórico
ani-br -q 1080 naruto       # qualidade preferida (best/worst/360/720/1080)
ani-br -d -e 1-12 naruto    # baixar episódios 1 a 12
ani-br --no-detach naruto   # roda o player em primeiro plano (erros visíveis)
ani-br -h                   # todas as opções
```

Durante a reprodução, o menu permite `next` / `previous` / `replay` / `select` / `change_quality` / `quit`.

## Fontes suportadas

Consultadas em ordem de prioridade, com fallback automático por anime:

| Prioridade | Fonte | Domínio | Vídeo | Dublado |
|---|---|---|---|---|
| 1ª | **AnimesDigital** | `animesdigital.org` | HLS (`.m3u8`) | sim |
| 2ª | **AnimesROLL** | `www.anroll.info` | HLS (`.m3u8`) | sim (`-dublado`) |
| 3ª | **AnimeFire** | `animefire.io` | MP4 direto | sim (`-dublado`) |
| 4ª | **TopAnimes** | `topanimes.net` | HLS (`.m3u8`) | sim |

Reordene ou restrinja com `ANI_CLI_SOURCES` (veja [Configuração](#configuração)).

## Configuração

Opções fixáveis por variável de ambiente (prefixo `ANI_CLI_`):

| Variável | Para quê |
|---|---|
| `ANI_CLI_SOURCES` | Ordem/seleção de fontes. Ex.: `ANI_CLI_SOURCES="anroll" ani-br ...` |
| `ANI_CLI_PLAYER` | Player a usar. Ex.: `export ANI_CLI_PLAYER=mpv` |
| `ANI_CLI_QUALITY` | Qualidade padrão (`best`/`worst`/`720`...) |
| `ANI_CLI_MODE` | `sub` (padrão) ou `dub` |
| `ANI_CLI_DOWNLOAD_DIR` | Pasta de download |

## Solução de problemas

- **Nenhuma janela abre.** O `mpv` provavelmente é o do flatpak (sandbox). Force o nativo: `export ANI_CLI_PLAYER=mpv`. Para ver o erro real: `ani-br --no-detach`.
- **`permissão negada` ao rodar.** `chmod +x ani-br`.
- **`No results found` numa fonte.** O fallback tenta a próxima fonte sozinho; se nenhuma tiver o título, ele não está nos catálogos.
- **HTTP 401 em vídeo do AnimeFire.** Se você fixou `ANI_CLI_ANIMEFIRE_BASE` para `.plus`, troque para `animefire.io`.
- **Não rode `ani-br -U`.** O auto-update aponta para o upstream e sobrescreveria este fork.

## Contribuindo: adicionar uma fonte

Cada fonte é um trio de funções:

```
<fonte>_search "<consulta>"        -> linhas: id_nativo<TAB>título
<fonte>_episodes "<id_nativo>"     -> um número de episódio por linha
<fonte>_episode_url "<id>" "<ep>"  -> linhas: <qualidade> ><url>
```

Defina `<fonte>_base="https://..."` e adicione o nome em `sources`. Veja `animefire_*` e `anroll_*` como referência.

## Crédito e licença

Fork de [pystardust/ani-cli](https://github.com/pystardust/ani-cli). GNU GPL v3.0 — veja [LICENSE](./LICENSE) e o [disclaimer](./disclaimer.md).
