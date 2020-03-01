Como mencionado nos primeiros artigos, esse projeto surgiu a partir do meu TCC, no qual o tema refere-se à- 'Monitoramento em tempo real do consumo da energia elétrica utilizando tecnologias open source'. Este artigo será o primeiro de uma série de artigos, com o objetivo de apresentar o projeto IoTBr.

Caso não tenha lido os outros artigos, ambos estão no painel a direita de vocês, corre lá :P.

## Introdução

Até então todo software desenvolvido não estava liberado, e também, o desenvolvimento do mesmo não ficou 100% implementado como o  pretendido -ficou apenas funcional(toda integração envolvida)
O projeto basicamente se dividia em duas partes: Embarcado(Hardware + Firmware) e Software. Observe abaixo as duas imagens, uma representando o hardware construído e o software, respectivamente.

![img](https://douglaszuqueto.com/uploads/articles/artigo-7/hardware.png)

![img](https://douglaszuqueto.com/uploads/articles/artigo-7/software.png)

## Objetivo do TCC

O projeto consistia em desenvolver um protótipo - do hardware ao software, e em com base nesse cenário queria aplicar conhecimentos que vinha adquirindo até o momento do tcc. Todo esse desenvolvimento, resultou em uma tomada inteligente e uma dashboard, que em tempo real, enviava dados do embarcado para o software, assim, resultando na representação das informações demonstrado na imagem anterior.


## Hardware

O hardware, era formado basicamente por:

* 1 Arduino
* 1 ESP8266 modelo 01
* 1 sensor de corrente ACS712 30A
* 1 Fonte 5V com regulador de tensão para 3.3V


## Software e ferramentas

* NodeJS
* Broker MQTT utilizando o módulo Mosca para NodeJS
* Socket.IO
* MongoDB
* AngularJS
* Bower
* NPM
* MaterializeCSS


Bem pessoal, esse artigo foi bem curto, pois resolvi abordar de forma simples o objetivo do tcc e o que foi utilizado no desenvolvimento. Nós próximos artigos, será tratado de forma individual o Hardware, Software/ferramentas bem como o real objetivo dessa série de artigos.

Curtiu? Não deixe de compartilhar com seus amigos este artigo e deixar o like na minha página , esse é um passo importante para o crescimento dos projetos :P.

Aaaa, o próximo artigo será publicado em breve :D.
