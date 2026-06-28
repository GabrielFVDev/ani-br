<h1 align="center">ani-br</h1>

<p align="center">
Assista anime <b>dublado e legendado em pt-br</b> direto do terminal, sem anúncio.
</p>

<p align="center">
Fork brasileiro do <a href="https://github.com/pystardust/ani-cli">ani-cli</a>, adaptado para raspar fontes nacionais.
</p>

---

## Índice

- [O que é](#o-que-é)
- [Fontes](#fontes)
- [Dependências](#dependências)
- [Instalação](#instalação)
- [Uso](#uso)
- [Variáveis de ambiente](#variáveis-de-ambiente)
- [Adicionar uma fonte](#adicionar-uma-fonte)
- [Solução de problemas](#solução-de-problemas)
- [Crédito e licença](#crédito-e-licença)

## O que é

`ani-br` é um shell script único (POSIX) para buscar e assistir anime pelo terminal, abrindo o vídeo num player externo (mpv por padrão). É um fork do [ani-cli](https://github.com/pystardust/ani-cli) em que toda a camada de scraping foi trocada do site original (em inglês) por **fontes pt-br**.

## Fontes

As fontes são **plugáveis** e consultadas por ordem de prioridade. O fallback é **por anime**: se a primeira fonte não tiver o título, a próxima é tentada.

| Prioridade | Fonte | Domínio | Vídeo | Dublado |
|---|---|---|---|---|
| 1ª | **AnimesDigital** | `animesdigital.org` | HLS (`.m3u8`) | sim |
| 2ª | **AnimesROLL** | `www.anroll.info` | HLS (`.m3u8`) | sim (`-dublado`) |
| 3ª | **AnimeFire** | `animefire.io` | MP4 direto | sim (`-dublado`) |
| 4ª | **TopAnimes** | `topanimes.net` | HLS (`.m3u8`) | sim |

Por padrão `sources="animesdigital anroll animefire topanimes"`. O fallback é por anime: a primeira fonte que tiver o título serve; as demais entram para títulos que faltam. Dá pra reordenar ou restringir com a env `ANI_CLI_SOURCES` (veja abaixo).

> A base default do AnimeFire é `animefire.io`. O antigo `animefire.plus` redireciona para `.io`, mas o CDN do vídeo valida o *Referer* pela string exata — usar `.plus` causava **HTTP 401** no vídeo. Se você sobrescrever `ANI_CLI_ANIMEFIRE_BASE`, use o domínio para o qual o site resolve de fato.

## Dependências

**Obrigatórias:** `curl`, `sed`, `grep`, `jq`, `fzf`, e um player (`mpv` recomendado).

**Opcionais (download `-d`):** `aria2c` (mp4), `yt-dlp` ou `ffmpeg` (HLS/m3u8).

**Opcional (pular abertura):** [`ani-skip`](https://github.com/synacktraa/ani-skip) (só com mpv).

Exemplos de instalação das dependências:

```sh
# Debian/Ubuntu
sudo apt install curl sed grep jq fzf mpv

# Arch
sudo pacman -S curl sed grep jq fzf mpv

# Fedora
sudo dnf install curl sed grep jq fzf mpv
```

## Instalação

A partir do código-fonte:

```sh
git clone https://github.com/GabrielFV/ani-br.git
sudo cp ani-br/ani-br /usr/local/bin/
rm -rf ani-br
```

Ou, sem `sudo`, em `~/.local/bin` (garanta que está no `PATH`):

```sh
git clone https://github.com/GabrielFV/ani-br.git
cp ani-br/ani-br ~/.local/bin/
rm -rf ani-br
```

Confirme que o arquivo está executável (`chmod +x ani-br`) e rode `ani-br`.

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

## Variáveis de ambiente

As variáveis mantêm o prefixo `ANI_CLI_` (compatibilidade com o upstream). As mais úteis:

| Variável | Para quê |
|---|---|
| `ANI_CLI_SOURCES` | Ordem/seleção de fontes. Ex.: `ANI_CLI_SOURCES="anroll" ani-br ...` força só o AnimesROLL |
| `ANI_CLI_PLAYER` | Player a usar. Ex.: `export ANI_CLI_PLAYER=mpv` força o mpv nativo |
| `ANI_CLI_QUALITY` | Qualidade padrão (`best`/`worst`/`720`...) |
| `ANI_CLI_MODE` | `sub` (padrão) ou `dub` |
| `ANI_CLI_DOWNLOAD_DIR` | Pasta de download |

## Adicionar uma fonte

A arquitetura é plugável. Cada fonte é um trio de funções com o mesmo contrato:

```
<fonte>_search "<consulta>"        -> linhas: id_nativo<TAB>título
<fonte>_episodes "<id_nativo>"     -> um número de episódio por linha
<fonte>_episode_url "<id>" "<ep>"  -> linhas: <qualidade> ><url>
```

Defina também `<fonte>_base="https://..."` (usado como referer) e acrescente o nome em `sources`. Os dispatchers (`search_anime`/`episodes_list`/`get_episode_url`) cuidam do resto, carimbando o id com `<fonte>:` para o histórico e o fallback funcionarem. Veja `animefire_*` e `anroll_*` como referência.

## Solução de problemas

- **Roda no terminal mas nenhuma janela abre.** Geralmente o `mpv` escolhido é o do flatpak (sandbox não abre janela). Force o nativo: `export ANI_CLI_PLAYER=mpv`. Para ver o erro real, use `ani-br --no-detach`.
- **`permissão negada` ao rodar.** Falta o bit de execução: `chmod +x ani-br`.
- **`No results found` numa fonte.** O fallback por-anime tenta a próxima fonte automaticamente; se nenhuma tiver o título, ele não existe nos catálogos.
- **Não rode `ani-br -U`.** O auto-update aponta para o repositório do upstream e **sobrescreveria este fork**.

## Crédito e licença

Fork de [pystardust/ani-cli](https://github.com/pystardust/ani-cli). Todo o crédito da base (UI, playback, histórico, multiplataforma) é da equipe original.

Licenciado sob a **GNU GPL v3.0** — veja [LICENSE](./LICENSE). Uso por sua conta e risco; veja o [disclaimer](./disclaimer.md).
