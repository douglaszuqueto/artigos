## Introdução

Primeiramente deixo meu agradecimento ao [Magno](https://github.com/magnomontecerqueira) pela doação da placa aqui mencionada.

Neste artigo será apresentado o shield de automação para Raspberry PI. Nele será falado sobre a placa,  materiais necessários para uso, suas divisões bem como conteúdo/documentações disponivéis para auxilio do desenvolvimento.

## Materiais necessários

* 1 Shield de Automação;
* 1 Raspberry PI 3;
* 1 fonte de alimentação 12V 2A ou posterior;

## Sobre a placa

A placa tem como foco automação residencial, possuindo saídas e entradas que trabalham em conjunto com uma Raspberry Pi. A placa é desenvolvida no Brasil e vendida na loja [Projeto Arduino](http://www.projetoarduino.com.br/raspberry-shield-de-automacao-residencial).

Na primeira impressão que tive dela logo que chegou, é que se trata de um belíssimo produto, possuindo um bom design, PCB de boa qualidade e considerei de fácil entendimento(trilhas e componentes utilizados).

Obs: Eletrônica não é minha área, logo não sei analisar a qualidade de uma placa. Mas para um Maker e entusiasta da área, a placa para mim é perfeita :P.

## Especificações

Segue abaixo uma lista referente as especificações da placa(retirado do site).

* Plug de alimentação 12v que já alimenta a raspberry evitando usar vários adaptadores.
* 3 transistores de saída controlados pelo MCP23017 (GPB2, GPB3, GPB4).
* 8 Entradas para contatos secos.
* Entrada TTL 5V já convertidos para ligar na raspberry
* Entradas analogicas usando MCP3008 via SPI .
* I2C 5v level converter pronto para usar o DS1307 Real Time Clock Module.
* CI MCP23017 Controlado pela I2C raspberry.
* 10 reles controlados CI MCP23017
* Carregador de bateria de chumbo embutido.
* Soquete para xbee

## Divisão da placa
Observe na imagem abaixo a divisão da placa e logo após suas respectivas funcionalidades.

![img](https://douglaszuqueto.com/uploads/articles/artigo-20/placa.jpg)

* 1 - Área destinada ao circuito de alimentação da placa;
* 2 - Circuito Integrado MCP23017 I2C;
* 3 - Circuito Integrado MCP3008 SPI;
* 4 - Socket para conexão da Raspberry PI;
* 5 - Reles;
* 6 - Transistores de saídas;
* 7 - Entradas de contato seco;
* 8 - Entradas analógicas;
* 9 - Socket para conexão de XBee;
* 10 - Socket para conexão de RTC;

## Projetos utilizando a placa

Segue no link de baixo um belo projeto de TCC que foi desenvolvido utilizando o shield de automação:

* [Automação Residencial com Raspberry Pi - Final](https://www.youtube.com/watch?v=4PhAngSLeHo)

## Próximos passos

Os próximos passos serão abordar os reles da placa, que no meu ponto de vista é o mais interessante dela, pois além de oferecer 10 reles, os mesmos são controlados pelo MCPMCP23017 no qual se utiliza comunicação i2c, ou seja, somente 2 pinos da sua raspberry serão utilizados para efetuar o controle dos reles. Doido né?

Também será testado o material oferecido no github e também iremos criar scripts para realizar o controle dos reles - provavelmente via interface web :p.

Confesso que estou bem empolgado quanto ao uso dela. Uma boa placa juntamente com um Embarcado poderoso, vai rolar bons projetos:  Mqtt, websockets, broker, api's de controle, interface web, banco de dados, app real time e por ai vai :P.

## Conteúdos e documentações referentes à placa
* [Raspberry pi Exp. Board User Manual](https://dl.dropboxusercontent.com/u/28708678/Raspberry%20pi%20Exp.pdf)
* [Raspberry pi Exp. Board Hardware Installation](https://docs.google.com/document/d/1az8SXCyIui2BPWNvqgpyk78QhrWnGo_psaqqlqsk1lQ/edit)
* [API examples for Expansion Board](https://github.com/Moisesbr/raspberry_exp_board)