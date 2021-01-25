---
title: "Como usar o Youtube-DL"
permalink: youtube-dl-basico
date: "2021-01-25 00:37"
key: 202101250037
layout: article
aside:
  toc: false
articles:
  data_source: site.sample_page
  show_excerpt: true
  show_readmore: true
tags: linux windows youtube
---
# Introdução
Em mais um post do D.T.I, desta vez estarei ensinando como utilizar o básico do Youtube-DL, que é uma ferramenta escrita em Python que funciona apenas por linha de comando mas que é muito poderosa, e lhe garanto que se você aprender a utiliza-la corretamente nunca mais dependerá de "sites pra baixar vídeos do Youtube" e muitos outros sites, que são suportados pelo Youtube-dl, que de volta e meia são derrubados.
Saiba você que o Youtube-dl é utilizado como base pra construir a maioria desses sites, então porque não beber direto da fonte e aprender como se faz isso totalmente de graça?

AVISO: Este artigo não visa a descrição completa da ferramenta, mas ensinar o básico para que o individuo consiga desenvolver sua própria solução através da mesma.
{:.warning}
# Instalação
Diferente do que muitos pensam, Python não se limita só ao Linux e por isso estarei mostrando como se instala nos dois, Linux e Windows respectivamente.
## Windows
A maneira correta de se instalar os módulos Python é via "PIP", que basicamente é um gerenciador de pacotes python a grosso modo.

Instalar no Windows é muito fácil, só precisa cuidar de adicionar adicionar no PATH do sistema, assim você garante que o sistema "enxergue" o módulo Python em basicamente qualquer lugar no sistema.
### 1º Passo
Baixar o Python for Windows:
Acesse o site [python.org/downloads](https://www.python.org/downloads/) e baixe a versão mais recente do Python para Windows. <br>
![Download Python for Windows](/assets/images/como-usar-Youtube-DL/Python_1.png)
### 2º Passo

## Linux

No Linux, dependendo da sua distribuição, isso é mais fácil ainda que no Windows. Só irá precisar adicionar o caminho aonde o Linux instala os módulos python pro usuário atual e só começar a brincar.

Se você utiliza BASH como Shell padrão não deverá enfrentar problemas com os comandos a seguir, assim sendo, tirando a parte da instalação do PIP na distribuição o resto do artigo se aplica a qualquer distribuição.
{:.warning}

### 1º Passo
Instale o PIP usando o gerenciador de pacotes da sua distribuição
#### Debian, Ubuntu e baseados:
```sh
sudo apt install python3-pip ffmpeg
```
Testado no Debian 10, Ubuntu 20.04 e sistemas baseados mais recentes
{:.info}

### 2º Passo
Adicione o PATH ao sistema executando os comandos a seguir como usuário comum:
```sh
echo "export PATH="$HOME/.local/bin:$PATH"" >> ~/.bashrc
source ~/.bashrc
```
## Instalação Youtube-dl
Com o pip instalado e o PATH configurado, fica fácil instalar o Youtube-dl em qualquer sistema.
Basta executar o seguinte comando:
```sh
pip3 install -U youtube_dl
```
Se tudo ocorreu sem erros, o programa estará pronto para uso.
## Uso
Usar o Youtube-DL é tão fácil que quase nem precisa de tutorial.
Usaremos de exemplo para download este vídeo: [https://www.youtube.com/watch?v=3nQNiWdeH2Q](https://www.youtube.com/watch?v=3nQNiWdeH2Q)

Para baixar rapidamente em qualidade baixa, digite:
```sh
youtube-dl -f 18 https://www.youtube.com/watch?v=3nQNiWdeH2Q
```
Para baixar rapidamente em qualidade HD, digite:
```sh
youtube-dl -f 22 https://www.youtube.com/watch?v=3nQNiWdeH2Q
```
Para baixar rapidamente somente audio MP3:
```sh
youtube-dl -x --audio-format mp3 https://www.youtube.com/watch?v=AHuY1NIRLr8
```
