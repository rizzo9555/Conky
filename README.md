# Rizzo Matrix Conky

Um tema para [Conky](https://github.com/brndnmtthws/conky) inspirado no visual do filme **Matrix**: fundo transparente, verde fosforescente, fonte monoespaçada e um relógio digital em destaque. Construído em cima de um `.conkyrc` já otimizado para monitorar CPU, memória, temperaturas, discos, rede e GPU NVIDIA.

![status](https://img.shields.io/badge/conky-1.22.2-00FF41?style=flat-square&labelColor=000000)
![license](https://img.shields.io/badge/license-GPLv2-00FF41?style=flat-square&labelColor=000000)

## Preview

> Screenshot:
>
> `![preview](docs/preview.png)`

## Recursos

- 🟢 Paleta de cores estilo Matrix (`#00FF41` / `#39FF14` sobre fundo preto transparente)
- ⏰ Relógio grande no topo, estilo contador digital, com data e segundos
- 🖥️ Uso geral de CPU (não por núcleo) + frequência + gráfico
- 🧠 RAM e Swap em linhas separadas, com barra de uso
- 🌡️ Seção dedicada de temperaturas (CPU, NVMe/SSD e GPU)
- 📊 Top 4 processos por uso de CPU, com memória exibida em MB/GB (alinhada à direita)
- 💾 Uso de disco e I/O de leitura/escrita (NVMe)
- 🌐 Status de rede para Ethernet (`enp5s0`) e Wi-Fi (`wlp6s0`)
- 🎮 Métricas completas de GPU via `nvidia-smi` (temperatura, clocks, VRAM, consumo, uso)
- ▓▒░ Separadores e prefixos que remetem a um terminal

## Requisitos

| Componente | Observação |
|---|---|
| [Conky](https://github.com/brndnmtthws/conky) | testado na versão 1.22.2 |
| Fonte **Fira Code** | usada em todo o tema |
| `lm-sensors` | para as temperaturas de CPU/NVMe (comando `sensors`) |
| Driver NVIDIA + `nvidia-smi` | para a seção de GPU |
| `x11-xserver-utils` | opcional, usado no autostart via systemd (comando `xset`) |

## Instalação

### 1. Instalar a fonte Fira Code

```bash
sudo apt update
sudo apt install fonts-firacode
fc-cache -fv
```

Confirme que foi reconhecida:

```bash
fc-list | grep -i "fira code"
```

### 2. Instalar dependências

```bash
sudo apt install conky-all lm-sensors x11-xserver-utils
sudo sensors-detect   # responda "yes" às perguntas padrão
```

### 3. Copiar o arquivo de configuração

```bash
mkdir -p ~/.config/conky
cp Rizzo.conkyrc ~/.config/conky/Rizzo.conkyrc
```

### 4. Testar

```bash
conky -c ~/.config/conky/Rizzo.conkyrc
```

## Autostart (systemd user service)

O Ubuntu 26.04 não traz mais a GUI de "Startup Applications", então o autostart é feito via **serviço de usuário do systemd**. Pontos importantes descobertos durante os testes:

- `background = false` no `.conkyrc` — necessário para o systemd rastrear o processo corretamente (`background = true` faz o Conky virar daemon e "some" do systemd)
- Verificação de tela pronta com `xset q`, seguida de um `sleep 10` para dar tempo do XWayland, drivers e compositor estabilizarem antes do Conky desenhar

Exemplo de unit (`~/.config/systemd/user/conky.service`):

```ini
[Unit]
Description=Conky Matrix Theme
After=graphical-session.target

[Service]
Type=simple
ExecStartPre=/bin/sh -c 'until xset q; do sleep 1; done; sleep 10'
ExecStart=/usr/bin/conky -c %h/.config/conky/Rizzo.conkyrc
Restart=on-failure

[Install]
WantedBy=graphical-session.target
```

Ativar:

```bash
systemctl --user daemon-reload
systemctl --user enable --now conky.service
```

## Personalização

### Cores (Matrix theme)

| Variável | Cor | Uso |
|---|---|---|
| `default_color` / `color2` / `color5` | `#00FF41` | texto padrão, labels, cabeçalhos |
| `color1` | `#008F11` | contorno/preenchimento de gráficos |
| `color3` | `#003B00` | fundo escuro, separadores |
| `color4` | `#39FF14` | destaques (data) |
| `color6` | `#E0FFE0` | relógio, nomes de processos |
| `color7` / `color8` / `color9` | verde / laranja / vermelho | sinalização (ok / aviso / alerta) |

### Ajustes comuns

- **Temperatura da NVMe**: a linha `NVMe Temp` depende do rótulo retornado pelo `sensors` no seu sistema. Rode `sensors` no terminal e ajuste o `grep` (ex.: `nvme-pci-0400`) se a linha não aparecer.
- **Interfaces de rede**: troque `enp5s0` e `wlp6s0` pelos nomes das suas interfaces (`ip a` para conferir).
- **Discos monitorados**: existem blocos comentados no arquivo para adicionar discos extras — basta descomentar e ajustar o ponto de montagem.
- **Tamanho/posição da janela**: `minimum_width`, `maximum_width`, `gap_x`, `gap_y` e `alignment` no bloco `conky.config`.

## Créditos

Baseado no tema original do ArcoLinux (Erik Dubois), com as modificações "Titus" e as customizações "Rizzo" (Matrix theme) descritas no cabeçalho do próprio arquivo `Rizzo.conkyrc`.

## Licença

Distribuído sob os termos da GNU GPL v2 ou posterior, conforme o arquivo original.
