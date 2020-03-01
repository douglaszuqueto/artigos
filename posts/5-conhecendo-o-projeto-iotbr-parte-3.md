Este artigo tem como objetivo apresentar o software e como se dá o funcionamento do fluxo envolvido no software.

A base do software desenvolvido se dá pelo uso do nodejs no back-end e angularjs no front-end. O uso destas tecnologias foram o maior desafio, pois era o primeiro projeto que de fato iria aplicá-las. A escolha da utilização das mesmas se dá basicamente por 2 motivos.

* Nodejs: quando comecei a pesquisar e a estudar a plataforma nodejs, observei que ele era muito bom para aplicações de tempo real. então não precisa falar mais nada né?
* AngularJS: quando conheci angularjs, fiquei fascinado com a construção de aplicações SPA(Single Page Applications), de modo que não precisava ficar recarregando os assets ao dar um reload na página. Portanto, foi esse motivo que me convenceu a aplicar o angularjs no projeto.

Portanto, com esses 2 caras na stack, eu estava convicto que conseguiria aplicar uma comunicação em tempo real e também ter uma dashboard com uma boa usabilidade.

# Back-end

O back-end da aplicação se divide em duas camadas: O Broker MQTT e a camada de  API / WebSockets;

## Broker MQTT

No inicio do projeto, ainda estava perdido neste mundo, pois o mqtt era algo totalmente novo para mim, estava literalmente cheio de desafios. Conheci logo de cara o Mosquitto, que oferece um broker standalone, ou seja, ele roda a partir da linha de comando e trabalha de forma independente. 

Logo depois, pesquisando com foco em módulos para nodejs, eu conheci o Mosca, que se tratava de um módulo standalone(igualmente ao mosquitto) e também poderia ser embarcado na aplicação - poderia programa-lo, integrar com banco de dados, implementar autenticação e autorização de tópicos e etc. Portanto, percebi uma liberdade maior na utilização do Mosca e resolvi investir no mesmo.

O broker era relativamente simples - simples mesmo, pois a implementação dele foi a mais básica possível. Observe o trecho abaixo com o código do broker.

```
var mosca = require('mosca'); // Importa o modulo Mosca

var SETTINGS = {
    port: 1883, // Define a porta de operação do MQTT
};
// Pode ser integrado a banco de dados como: Redis e MongoDB
var MQTT_SERVER = new mosca.Server(SETTINGS); // Cria um Broker MQTT com base nas configurações

MQTT_SERVER.on('clientConnected', function (cliente) { // Evento: ocorre quando um novo cliente se conecta ao Broker
    console.log('Cliente Conectado', cliente.id); // Exibe uma mensagem com o ID do cliente conectado
});

MQTT_SERVER.on('published', function (packet, client) { //  Evento: ocorre quando uma mensagem é Puplicada
    console.log("Tópico: ", packet.topic, " | ", new Date().toISOString()); // Exibe uma mensagem com o tópico da mensagem recebida
});

MQTT_SERVER.on('clientDisconnected', function (cliente) {
    console.log('Cliente Desconectado: ', cliente.id);
});

MQTT_SERVER.on('ready', setup); // Inicio do Broker
function setup() {
    console.log('Servidor MQTT rodando');
}

```

Para quem estiver interessado no módulo segue o [link](https://github.com/mcollina/mosca), no repositório você encontrará exemplos e uma boa documentação. 

## API

A api também era simples, lembra que mencionei em outro artigo que não consegui implementar inúmeras coisas? Pois é. Eram poucos recursos, poucos mesmo. Mas o mais importante, era no que se referia à Tomada. Cada painel que tinha na dashboard era genérico. Ou seja, eles eram cadastrados e os dados ficariam salvo no banco. Veja na imagem abaixo o ciclo de vida da API.

![img](https://douglaszuqueto.com/uploads/articles/artigo-9/diagrama-webservice.png)

E também, observe o trecho do endpoint para efetuar o cadastro de uma nova tomada e em seguida os painéis que mencionei.
```
app.post('/tomada', function (req, res) {
    console.log("Dados: " + req.body);
    var model = new Tomada(req.body);
    model.save(function (err, data) {
        if (err) {
            console.log("Erro: " + err);
            return;
        }
        console.log("Tomada Cadastrada: " + data);
    });
    res.json(req.body);
});
```

![img](https://douglaszuqueto.com/uploads/articles/artigo-9/paineis-centrais.png)

## WebSockets

Os serviço de websockets foram desenvolvidos na mesma camada da api, mais irei tratar como um tópico aqui no artigo. 

Esta camada digamos que foi a principal parte do sistema que foi desenvolvida, pois era o ponto chave de onde toda comunicação real time acontecia do embarcado até a dashboard.

Este serviço estava 'interligado' com o broker mqtt, pois uma conexão era aberta com o mesmo. Esta conexão(ciente) ficava escutando por eventos de publish e subscribe, para então assim poder reencaminhar os dados advindos do mqtt via websockets para a dashboard. Veja o diagrama do fluxo na imagem abaixo.

![img](https://douglaszuqueto.com/uploads/articles/artigo-9/diagrama-websockets.png)

# Front-end

O principal objetivo do front-end era construir uma dashboard com a página inicial que mostrasse os dados enviados pelo embarcado em tempo real, assim fornecendo o consumo de cada tomada que estivesse conectado na rede. Veja a imagem abaixo.

![img](https://douglaszuqueto.com/uploads/articles/artigo-9/dashboard.png)

Nesta tela principal, um conexão com websockets é aberta, assim permitindo receber os dados e mostrar no seu respectivo painel. Como eu havia uma tomada somente, eu fiz um robozinho em nodejs para publicar dados fictícios para ver o funcionamento dos demais painéis da dashboard.

Basicamente no front-end é isso - além do cadastro das tomadas(não achei uma imagem para demonstrar :/ ). Não consegui implementar outras funcionalidades,  portanto fica para ser implementado no projeto IoT-BR.

# Resultados

O principal e o mais difícil foi desenvolvido - **desenvolver todas as camadas do projetos** - **do hardware ao software**, aplicando todas tecnologias que havia conhecido.

Por mais que ficaste incompleto porém funcional, eu gostei muito do resultado, ver algo que foi desenvolvido de ponta a ponta, e de fato funcionando, era algo muito satisfatório.

Com o projeto, ouve muitos aprendizados, uma clara visão de como o mundo IoT funciona/funcionará, arquiteturas para aplicações IoT, tecnologias quem podem ser aplicadas, quais protocolos e etc. 

**Observação**: Caso não tenha entendido de fato as tecnologias envolvidas no projeto(mqtt, api, sockets e etc), não se preocupe, nos artigos futuros, esses caras serão tratados individualmente.

É será com esse aprendizado que o projeto será refatorado, assim criando-se o projeto IoT-Br, mas esta pauta fica para o próximo e último artigo desta série :).