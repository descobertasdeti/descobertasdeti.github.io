---
layout: article
title: "Tutorial de C++: Uma abordagem resumida"
aside:
  toc: true
articles:
  data_source: site.sample_page
  show_excerpt: true
  show_readmore: true
---
# Introdução
Este artigo é baseado em curso que fiz, e para fins de melhor fixação do conteúdo resolvi fazer este tutorial. Sente na cadeira, pegue seu café e não olhe para Scroll da página pois este tutorial provavelmente ficará gigante. Em caso de muitos acessos e mais pessoas se interessando pelo assunto posso estar tentando melhorar o Tutorial que já disse e repito por hora a apenas pra referência. Qualquer dúvida, comentários do Disqus no final do post.
Bons Estudos!

# Começa aqui
Partindo do principio de que você certamente não sabe nada de programação, ou se sabe, é conhecimento voltado para outra linguagem comecemos preparando o ambiente. È eu sei há inúmeros tutoriais de como se prepara o ambiente mas só pra ficar bem documentado, não fará mal mais um.

## Instalando Code::Blocks
Para começarmos a escrever nosso códigos é interessante, ao menos para C++, o uso de uma IDE(Integrated Development Enviroment) pois facilitará muito na escrita e execução. Sem mais conversa, vamos ao que interessa.

### Windows
No Windows é bem simples a instalação.
1. Acesse a página de Downloads do [Code::Blocks](http://www.codeblocks.org/downloads/26).
2. Procure por algo parecido com "codeblocks-20.03mingw-setup.exe". Note que "20.03" é a versão da IDE. Logo essa pode ser(**NÃO** é uma afirmação) a única coisa que mude ao procurar pelo download do programa num futuro próximo.
3. Após o Download, localize em seu computador o arquivo baixado e clique duas vezes nele para iniciar a instalação.
4. Instalação acredito que seja auto-explicativa(Mais conhecido como Next...Next...Next... Finish).<br/>
Fique atento apenas a um detalhe: Ao instalar certifique-se de está fazendo a instalação completa(Full) assim como mostra a imagem abaixo.

IMAGEM DE INSTALAÇÃO COMPLETA DO CODE::BLOCKS(Passo 4)

Parabéns você instalou o Code::Blocks. Agora você pode avançar para a próxima seção.
### Linux
A maioria das distruibuçoes carrega em seu repositório uma versão do Code::Blocks, porém geralmente essa versão é extremamente antiga sendo necessário instalar a versão direto do site ou por outro método caso sua distribuição seja Rolling Release.
Pra fins didáticos(E pra ficar mais fácil também =P), vou demonstrar como se instala no ArchLinux e baseados apenas.
1. Atualize os repositórios digitando no seu terminal:<br/>
`sudo pacman -Syyyuu`
2. Instale o CodeBlocks digitando no seu terminal: <br/>
`sudo pacman -S codeblocks`

Parabeńs você instalou o Code::Blocks. Agora você pode avançar para a próxima secão.
OBS.: Caso não utilize Arch e baseados procure ajuda para completar a instalação na sua distribuição.

## Configuração Básica do Code::Blocks

Com programa instalado, agora precisamos fazer as configurações iniciais.
1. Abra o programa
2. Siga as imagens a seguir:
