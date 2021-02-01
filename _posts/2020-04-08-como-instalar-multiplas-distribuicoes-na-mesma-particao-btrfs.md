---
title: "Como instalar multiplas distribuições Linux na mesma partição BTRFS"
permalink: multiplas-distros-btrfs
date: "2020-04-08 17:18"
comment: false
layout: article
tags: btrfs linux
---
# 1. Introdução
Se você procurou este artigo, que irá ficar deveras grande, você deve ser um usuário avançado de Linux ou um intermediário ao menos. Hoje estarei compartilhando com vocẽs uma idéia maluca que tive, que agora tenho o prazer de chamar de descoberta do ano. Ao final do artigo tentarei deixar um vídeo tutorial com todo o processo, mas não prometo que fique pronto de imediato. Sem mais delongas vamos ao que interessa.

* Do not remove this line (it will not be displayed)
{:toc}

# 2. Requisitos e obervações
Comecemos pelos requisitos
- Paciência
- Uma máquina virtual para testes(Recomendo VirtualBox)
- Duas isos de algum Linux que suporte particões BTRFS **OU** dois pendrives bootaveis com as respectivas distribuições de sua escolha(No caso de instalar numa máquina real)
  - No exemplo utilizarei Ubuntu e Manjaro
- Partições /boot separadas para cada distruição<br/>

**Observações:** No exemplo utilizarei Ubuntu e Manjaro, porém a partir do momento que você entender a lógica do processo poderá aplicar em qualquer distruibuição. Eu por exemplo utilizo este método na minha máquina real com Gentoo e Debian instalados na mesma partição. Então com alguns testes te garanto que você irá sair mestre nisso. <br/>
O /boot separado é pra fazermos o chainload do Grub de cada distruibuição, porém caso já tenha em mente instalar apenas duas pode deixar apenas a ultima distruição com o /boot separado. Como não sei se você irá utilizar mais de 2 distruibuições numa mesma partição BTRFS, acredito ser mais prudente deixar essa a recomendação padrão de todas as distros com /boot separado.<br/>
De posse dessas informações, recomendo que você teste o artigo em máquina virtual, pois assim você evita ficar a mercê de erros que venha a cometer e ficar sem sistema operacional.<br/>
{:.warning}

A partir daqui vou pressupor que você estará em maquina virtual. Mas não se preocupe darei dicas pra você que está instalando num hardware real.

# 3. Instalação da primeira distribuição
Não vou impor um modelo padrão de particionamento pois pode dar a impressão que pra funcionar precisa ser desta maneira, quando não é. Então aqui darei apenas um sugestão de particionamento, mas você poderá adaptar a sua realidade.

Sugestão de particionamento: <br/>

![Imagem particionamento manual](/assets/images/como-instalar-multiplas-distribuicoes-na-mesma-particao-btrfs/Ubuntu_1.png)<br/><br/>

![Imagem do particionamento sugerido](/assets/images/como-instalar-multiplas-distribuicoes-na-mesma-particao-btrfs/Ubuntu_2.png)

| Disco/Partição | Montagem | Tamanho         | Sistema de Arquivos | Nome    |
|----------------|----------|-----------------|---------------------|---------|
| Partição 1     | /boot    | 1GB             | EXT2                | Ubuntu  |
| Partição 2     | /boot    | 1GB             | EXT2                | Manjaro |
| Partição 3     | swap     | minimo 1G       | SWAP                | ***     |
| Partição 4     | /        | espaço restante | BTRFS               | ***     |

**Dica:** No caso de um Dual Boot com Windows, você pode criar uma partição extendida grande e aplicar o sugestão de particionamento na partição extendida.
{:.success}

Faça a instalação normalmente, porém se utilizando do particionamento manual para setar o /boot separado.

Ao final da instalação **NÃO REINICIE**, iremos fazer comandos adcionais no terminal.
Abra um terminal ainda no LiveCD e digite os seguintes comandos.

**Dica:** Ao copiar os comandos a seguir, o **'#'** antes de cada comando indica que você deve estar logado como root, para executar os comandos. Para logar como root digite `sudo su` em um terminal
{:.success}

Monte a partição BTRFS:<br/>
```shell
# mount /dev/sda4 /mnt
```
Entre no diretório '/mnt': <br/>
```shell
# cd /mnt
```
Crie um subvolume especialmente para o Ubuntu:<br/>
```shell
# btrfs subvolume create ubuntu
```
Clone os subvolumes '@' e '@home' para dentro do subvolume 'ubuntu' recém criado e delete os antigos.<br/>
```shell
# btrfs subvolume snapshot /mnt/@ /mnt/ubuntu
# btrfs subvolume snapshot /mnt/@home /mnt/ubuntu
# btrfs subvolume delete /mnt/@
# btrfs subvolume delete /mnt/@home
```
Agora detalhe importante. Será necessário modificar os arquivos /etc/fstab e fazer chroot no sistema para reinstalar o Grub na MBR e na /boot.<br/>
Mas primeiro vamos editar o /etc/fstab:<br/>
```shell
# nano /mnt/ubuntu/@/etc/fstab
```
Localize 'subvol=@' e 'subvol=@home' no arquivo e edite colocando 'ubuntu' antes de '@' nos dois parâmetros. Vai ficar mais ou menos assim:<br/>

![imagem ilustrativa fstab](/assets/images/como-instalar-multiplas-distribuicoes-na-mesma-particao-btrfs/Ubuntu_3.png)<br/><br/>

```shell
UUID=<UUID_da_partição> btrfs  defaults,subvol=ubuntu/@  0 1
UUID=<UUID_da_partição> btrfs  defaults,subvol=ubuntu/@home 0 2
```
Agora faremos chroot no Ubuntu pré instalado e reinstalar o Grub na MBR e na partição /boot: <br/>
```shell
# cd
# umount /mnt
# mount -o subvol=ubuntu/@ /dev/sda4 /mnt
# mount /dev/sda1 /mnt/boot
# mount --bind /proc /mnt/proc
# mount --bind /dev /mnt/dev
# mount --bind /sys /mnt/sys
# chroot /mnt /bin/bash
# grub-install /dev/sda
# grub-install --force /dev/sda1
# update-grub
# exit
# exit
```
Pronto! Agora você pode reiniciar o PC e partir para o próximo passo.<br/>

# 4. Instalação da segunda distribuição

Inicie a instalação normalmente, porém teremos algumas coisas diferentes a se fazer do que na primeira instalação. Vou detalhar todas logo abaixo:

- NÃO FORMATE a partição BTRFS, do contrário perderiamos o primeiro sistema instalado. Utilizando do particionamento manual, configure o instalador pra instalar o sistema sem formatar a partição. No nosso exemplo faremos isso com '/dev/sda4'
- Escolha a partição /boot para a segunda distruibuição. Lembrando que se escolher a partição incorreta aqui o seu Ubuntu ficará sem como dar boot, inutilizando mais umas vez nossa primeira instalação. No nosso exemplo escolheremos '/dev/sda2' como nosso /boot do Manjaro.
particionamento ficará como nas imagens abaixo.

![imagem particionamento manual](/assets/images/como-instalar-multiplas-distribuicoes-na-mesma-particao-btrfs/Majaro_1.png)<br/><br/>

![manter arquivos na particao btrfs](/assets/images/como-instalar-multiplas-distribuicoes-na-mesma-particao-btrfs/Manjaro_2.png)<br/><br/>

![imagem do particionamento geral](/assets/images/como-instalar-multiplas-distribuicoes-na-mesma-particao-btrfs/Manjaro_3.png)<br/><br/>

![resumo da instalacao](/assets/images/como-instalar-multiplas-distribuicoes-na-mesma-particao-btrfs/Manjaro_4.png)<br/>

Agora o processo será parecido com o que fizemos na primeira instalação com pequenas alterações.<br/>

Ao final da instalação **NÃO REINICIE**, iremos fazer comandos adcionais no terminal.
Abra um terminal no LiveCD e digite os seguintes comandos.

**Dica:** Ao copiar os comandos a seguir, o **'#'** antes de cada comando indica que você deve estar logado como root, para executar os comandos.
{:.success}

Monte a partição BTRFS:<br/>
```shell
# mount /dev/sda4 /mnt
```
Entre no diretório '/mnt': <br/>
```shell
# cd /mnt
```
Crie um subvolume especialmente para o Ubuntu:<br/>
```shell
# btrfs subvolume create manjaro
```
Clone os subvolumes '@' e '@home' para dentro do subvolume 'ubuntu' recém criado e delete os antigos.<br/>
```shell
# btrfs subvolume snapshot /mnt/@ /mnt/manjaro
# btrfs subvolume snapshot /mnt/@home /mnt/manjaro
# btrfs subvolume delete /mnt/@
# btrfs subvolume delete /mnt/@home
```
Agora detalhe importante. Será necessário modificar os arquivos /etc/fstab e fazer chroot no sistema para reinstalar o Grub na MBR e na /boot.<br/>
Mas primeiro vamos editar o /etc/fstab:<br/>
```shell
# nano /mnt/manjaro/@/etc/fstab
```
Localize 'subvol=@' e 'subvol=@home' no arquivo e edite colocando 'ubuntu' antes de '@' nos dois parâmetros. Vai ficar mais ou menos assim:<br/>
```conf
...
UUID=<UUID_da_partição> btrfs  defaults,subvol=manjaro/@  0 1
UUID=<UUID_da_partição> btrfs  defaults,subvol=manjaro/@home 0 2
```
Agora faremos chroot no Manjaro pré instalado e reinstalar o Grub na MBR e na partição /boot: <br/>
```shell
# cd
# umount /mnt
# mount -o subvol=manjaro/@ /dev/sda4 /mnt
# mount /dev/sda2 /mnt/boot
# mount --bind /proc /mnt/proc
# mount --bind /dev /mnt/dev
# mount --bind /sys /mnt/sys
# chroot /mnt /bin/bash
# grub-install /dev/sda
# grub-install --force /dev/sda2
# update-grub
# exit
# exit
```
Pronto! Agora você pode reiniciar o PC e partir para o próximo passo.<br/>

# 5. Chainload do GRUB
De posso do segundo sistema, já instalado, deve ter percebido que o Ubuntu não foi encontrado quando rodamos o 'update-grub' ainda no LiveCD. Mas é pensando nisso deixamos o /boot separado pra ambas as distribuições e instalamos o grub nelas. Sendo assim arrumar esse "problema" é mais fácil que tirar doce de criança.

Vamos lá:<br/>
Após bootar abra um terminal no sistema recém instalado e digite:

**Dica:** Ao copiar os comandos a seguir, o **'#'** antes de cada comando indica que você deve estar logado como root, para executar os comandos.
{:.success}
```shell
# nano /etc/grub.d/40_custom
```
```conf
...
menuentry "Ubuntu" {
   configfile (hd0,msdos1)/grub/grub.cfg
}
```
```shell
# update-grub
```

Agora é só reiniciar e a entrada grub do Ubuntu, estará prontinha pra uso. Reinicie e veja a mágica<br/>

![Grub Chainload](/assets/images/como-instalar-multiplas-distribuicoes-na-mesma-particao-btrfs/GRUB_1.png)<br/>

# 6. Considerações finais
Agora você pode adicionar quantas distros quiser na mesma partição economizando um espaço federal, e só selecionar qual você quer utilizar diretamente do grub na incialização do sistema. E o mais legal é que pode atualizar o grub ou kernel que dificilmente irá interferir nessa configuração que fizemos. Não é massa?
## 6.1. Dicas úteis
É interessante pra evitar duplicação de arquivos que você adicione ao fstab a partição BTRFS montada com a opção 'subvolid=5'. Pra quê isto? Isto é para quando caso você queira acessar um arquivo que está em outro subvolume btrfs, não tenha que reiniciar o PC só pra isto afinal estão todos na mesma partição, porém sem esta opção ativada no /etc/fstab isso torna impossível.
Para tal, adicione o seguinte no /etc/fstab:<br/>

**Dica:** Ao copiar os comandos a seguir, o **'#'** antes de cada comando indica que você deve estar logado como root, para executar os comandos.
{:.success}
```shell
# mkdir /btrfs
# nano /etc/fstab
```
```conf
...
UUID=<UUID_da_partição BTRFS> /btrfs          btrfs  defaults,subvolid=5 0 0
```
```shell
# mount -a
```
Isto irá lhe possiblitar acessar todos os subvolumes diretamente em '/btrfs'.<br/> EX:
Caso precise de um arquivo que baixou no Ubuntu, no Manjaro basta fazer um link simbolico com: <br/>
```shell
$ ln -s /btrfs/ubuntu/@home/Downloads ~/Downloads
```
Isto irá fazer um link simbolico da pasta Downloads da HOME do Ubuntu até a HOME atual, no exemplo o Manjaro.
Legal demais não é?<br/>

Outro detalhe, após tanta modificação acredito eu que não há "boot-repair" que repare esse sistema caso venha a se corromper. Então tenha sempre um pendrive com um Linux qualquer para poder exercer manutenções. Qualquer ajuda que precisar, me coloco a disposição.

# 7. Agradecimentos
Com isto finalizo mais um artigo aqui do Descobertas de T.I em caso de dúvidas comente! Será um prazer ajuda-lo em sua dúvida. Sugestão? Comente também! Colocarei uma referência a você na alteração sugerida. Um abraço, e fiquem com Deus.

# 8. To-Do
- Vídeo ilustrativo

* * *
### Notas de Rodapé
1. Reorganização do tutorial/artigo
2. Corrigidos vários erros das sintaxes dos comandos e gramática
3. Adicionado tabela ilustrativa e de conteudos.