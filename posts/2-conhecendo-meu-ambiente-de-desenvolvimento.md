Olá amigos, como estão? 

Vamos dando continuidade ao nosso trabalho com o 2ª artigo, caso não tenha visto o primeiro, não deixe de conferi-lo([Link](https://douglaszuqueto.com/artigos/artigo-de-lancamento-do-projeto))

Neste artigo, será descrito sobre o ambiente de desenvolvimento que venho utilizando para desenvolver meus projetos. 
Acho um ponto interessante de se relatar, pois muitas pessoas tem dificuldade em montar um ambiente de desenvolvimento que sane os requisitos de projeto.

Dividirei em alguns subtítulos as categorias que pretendo abordar como Sistema Operacional, softwares de desenvolvimento, ferramentas e de que maneira eu coloco meus projetos para rodar na 'nuvem'. Então vamos lá.

## Sistema Operacional

No inicio de minha formação acadêmica, eu utilizava Windows como sistema operacional. No decorrer dos semestres fui descobrindo o mundo 'livre' e comecei a conhecer distros Linux, comecei conhecendo distros como Ubuntu e Mint, rodava os so's em dual boot juntamente com o Windows.

Algum tempo depois, comprei um novo notebook e quis colocar uma distro única, porém teve certa incompatibilidade com o hardware, então pedi a indicação do meu amigo [Samuel](https://github.com/SamuelMoraesF), o mesmo me indicou a distro **Fedora**.

Então é com essa distro, que me encontro no momento, quase 2 anos passados. E sim, **eu não me arrependo**. 
É uma distro estável e boa para trabalhar, pois atende muito bem minhas necessidades como Desenvolvedor e Maker.

**Dica:** Caso seja um desenvolvedor/maker e não use nenhuma distro linux, recomendo dar uma chance. (Não sou FanBoy). Quem usa diariamente, sabe muito bem do que estou falando.

## IDE Arduino

No dia de hoje, tenho ela na versão 1.6.12. Eu sempre gostei da ide, é simples, eficaz e cumpre o que promete. Para meu perfil, como hobbista, me sinto super satisfeito.

## Software de Desenvolvimento

Para ambiente de desenvolvimento uso 2 IDE's. PHP Storm para desenvolver este [blog](https://github.com/douglaszuqueto/blog) e Web Storm para desenvolvimento do projeto de 
[IoT](https://github.com/douglaszuqueto/iot-br). São ótimas IDE's, possui uma boa interface, te concede um bom nível de produtividade e ótima integração com versionamento Git.

Quando necessito editar algum arquivo rapidamente, utilizo Atom ou Vim mesmo quando estou no terminal. 

## Docker

É através do [Docker](http://docker.com/) que toda 'magia' acontece. Uso no Blog, uso no IoT-Br, uso em outros projetos, simplesmente é muito <3 haha. Docker é impressionante, advindo de uma curva de aprendizado muito baixa para aprende-lo a usar. Tem me ajudando bastante, pois não sou um especialista em infraestrutura.

Para quem está interessado no assunto, recomendo o livro do [Rafael Gomes](https://github.com/gomex). [Link](https://leanpub.com/dockerparadesenvolvedores)

## Laravel

Utilizo o [Laravel](https://laravel.com) como framework de desenvolvimento para esta plataforma. Venho trabalhando com ele desde inicio do ano e é um framework fantástico, digo fantástico pois levo em conta não somente o framework em si, mas também pela comunidade envolvida, pela ótima documentação, popularidade e ótimos projetos de referência para estudo. 

Cito um case brasileiro, formado por 3 membros da comunidade, sendo eles: [Vinicius](https://github.com/vinicius73), [Diego](https://github.com/hernandev) e pelo [Vedovelli](https://github.com/vedovelli). São caras sencionais e sem sombra de dúvidas - estão entre os mais fluentes do assunto aqui do Brasil. Eles mantém dois projetos no qual eu considero importantíssimo. Codecasts e Ambientum.

### CodeCasts 

O projeto [CodeCasts](https://codecasts.com.br) tem como objetivo levar um conteúdo de extrema qualidade e mesmo assim mantendo com um custo acessível. Todo código fonte do projeto também está no GitHub ([Link](https://github.com/codecasts/codecasts)). 

Sou assinante e recomendo. **Eu não estou fazendo propaganda**. Simplesmente admiro o trabalho dos caras, pois aprendi muito com eles, sempre estão ajudando a comunidade com seus projetos, e um ponto no qual considero importantissímo: são bem acessíveis. Muito do que está aplicado neste blog foi conhecimento que adquiri através do CodeCasts.

### Ambientum

O projeto [Ambientum](https://github.com/codecasts/ambientum) é um conjunto de imagens Docker que o Diego criou com o objetivo de facilitar com o worflow de desenvolvimentismo e de produção de aplicações utilizando Laravel / VueJS / NodeJS. 

Estas imagens Docker, também estão sendo usados neste meu projeto. Em ambos cenários: Desenvolvimento, Testes e Produção. É incrível a facilidade e maleabilidade que possuo de fazer deploy da aplicação que está em desenvolvimento para por em produção.


## NodeJS

[NodeJS](https://nodejs.org/en/) resumidamente é uma plataforma que interpreta o Javascript no lado do servidor. Eu conheci nodejs através de projetos envolvendo arduino, essas aplicações com real time. Achei simplesmente fantástico. 

Foi neste instante que comecei a estudar, e aplicar em pequenos projetos com arduino, sensores, ethernet shield e etc. Estava altamente pirado com tudo que eu poderia fazer, foi ai que o famoso TCC estava chegando, e obviamente teria que optar por um tema.

Bom, no meu projeto, eu queria fazer algo que tivesse muita interatividade, queria aplicar conceitos, técnicas que havia aprendido. Devido a esses fatos, juntei arduino, sensores, esp8266, protocolo mqtt, api rest, web sockets, banco de dados(mongodb), api rest, dashboard em angular. Com toda essa catrefada de tecnologias, desenvolvi um protótipo funcional de uma tomada(formato de T) que tinha como objetivo monitorar o consumo de energia elétrica em tempo real, assim, mostrando os dados na dashboard. Foi bem bacana haha, e louco :P.

No projeto da plataforma de IoT, se originou deste meu TCC, que agora estou refatorando, documentando e está liberado para todos. Provavelmente no próximo artigo, será especifico para tratar do projeto.

## Como coloco minhas aplicações em produção

Como utilizo Laravel no blog e nodejs no iot-br, seria impossível manter um ambiente comum(hospedagens compartilhadas que vemos por ai). Então eu tenho 2 VPS's (Servidor Virtual Privado - em português) na [Digital Ocean](https://digitalocean.com), uma vps para cada projeto.

Em ambas vps's eu utilizo o Droplet de 5 Dólar, que me entrega 512mb de ram. Até o momento está me atendendo muito bem. 
Então com esse ambiente e em conjunto com Git / GitHub, Docker e Docker Compose, eu consigo facilmente rodar minhas aplicações nessa VPS. 

Caso alguém tenha interesse em conhecer/utilizar a plataforma, pode usar esse meu [link](https://m.do.co/c/302f8d3a3de6) de referência no qual concede $ 10 para a pessoa testar, da para usar tranquilamente por dois meses com um droplet de $ 5. E caso, tiver alguma dúvida quanto a Digital Ocean pode me chamar inbox :).
## Ferramentas diversas

* Gulp;
* NPM;
* Composer
* Bower;
* Git;
* Trello;
* Dentre outras;

Por hoje é isso amiguinhos, desculpe por ter somente texto, mas precisava escrever sobre este tema. Nos próximos artigos, garanto que irá começar ficar mais legal haha. Aguardem. 

Obrigado pela leitura e até a próxima :D.