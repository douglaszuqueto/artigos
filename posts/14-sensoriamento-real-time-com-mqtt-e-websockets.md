Neste projeto será demonstrado como integrar o nodemcu juntamente com um sensor e a partir desse conjunto acompanhar os dados em uma aplicação web real time, tudo isso graças a utilização do mqtt juntamente com webosockets :).

## Introdução

Como mencionado anteriormente, iremos criar um simples projeto para que você possa acompanhar em tempo real o valor do seu sensor.

Portanto iremos utilizar a mesma base da aplicação web abordada no artigo "[Controle suas gpio's através da internet](https://douglaszuqueto.com/artigos/controle-suas-gpio-atraves-da-internet)".

O sensor que será utilizado neste artigo será um MCP9808 da Adafruit , um sensor de temperatura de alta precisão que utiliza comunicação I2C, para mais detalhes confira o artigo que escrevi  no FilipeFlop - [link](http://blog.filipeflop.com/wireless/adafruit-io-plataforma-iot.html)

## Materiais Utilizados
* 1 [NodeMCU](https://goo.gl/vOGqxz)
* 1 [Sensor de temperatura MCP9808](https://goo.gl/N50AfL)
* [Jumpers macho-macho](https://goo.gl/xVoa7i)
* 1 [Protoboard](https://goo.gl/Pj2RZ7)

## Esquemático

![img](https://douglaszuqueto.com/uploads/articles/artigo-19/esquematico.png)

## Sketch NodeMCU

```
/************************* Inclusão das Bibliotecas *************************/

#include <ESP8266WiFi.h>
#include <PubSubClient.h>

#include <Wire.h>
#include "Adafruit_MCP9808.h"

/****************************** Conexão WiFi *********************************/

const char* SSID      = "homewifi_D68"; // rede wifi
const char* PASSWORD  = "09114147"; // senha da rede wifi

/****************************** Broker MQTT **********************************/

const char* BROKER_MQTT = "broker.iot-br.com";
int BROKER_PORT         = 8080;

/*************************** Variaveis globais *******************************/

long previousMillis = 0;

/************************ Declaração dos Prototypes **************************/

void initSerial();
void initWiFi();
void initMQTT();
void initMCP9808();

/************************ Instanciação dos objetos  **************************/

Adafruit_MCP9808 mcp9808 = Adafruit_MCP9808();
WiFiClient client;
PubSubClient mqtt(client); // instancia o mqtt

/********************************* Sketch ************************************/

void setup() {
  initSerial();
  initWiFi();
  initMQTT();
  initMCP9808();
}

void loop() {
  if (!mqtt.connected()) {
    reconnectMQTT();
  }
  recconectWiFi();
  mqtt.loop();
  sensorLoop();

}

/*********************** Implementação dos Prototypes *************************/

/* Função responsável por publicar a cada X segundos o valor do sensor */
void sensorLoop() {
  unsigned long currentMillis = millis();
  if (currentMillis - previousMillis > 1000 && mqtt.connected()) {
    previousMillis = currentMillis;

    mcp9808.shutdown_wake(0);
    float c = mcp9808.readTempC();

    char temp[4];
    dtostrf(c, 2, 2, temp);
    mqtt.publish("DZ/gauge/temperature", temp);
    delay(250);
    mcp9808.shutdown_wake(1);

  }
}

/* Conexao Serial */
void initSerial() {
  Serial.begin(115200);
}

/* Configuração da conexão WiFi */
void initWiFi() {
  delay(10);
  Serial.println("Conectando-se em: " + String(SSID));

  WiFi.begin(SSID, PASSWORD);
  while (WiFi.status() != WL_CONNECTED) {
    delay(100);
    Serial.print(".");
  }
  Serial.println();
  Serial.print("Conectado na Rede " + String(SSID) + " | IP => ");
  Serial.println(WiFi.localIP());
}

/* Configuração da conexão MQTT */
void initMQTT() {
  mqtt.setServer(BROKER_MQTT, BROKER_PORT);
  mqtt.setCallback(mqtt_callback);
}

/* Inicialização do Sensor */
void initMCP9808() {
  if (!mcp9808.begin()) {
    Serial.println("Sensor MCP não pode ser iniciado!");
    while (1);
  }
}

/* Funcão responsável por receber os callbacks do mqtt */
void mqtt_callback(char* topic, byte* payload, unsigned int length) {

  String message;
  for (int i = 0; i < length; i++) {
    char c = (char)payload[i];
    message += c;
  }
  Serial.println("Tópico => " + String(topic) + " | Valor => " + String(message));
  Serial.flush();
}

/* Demais implementações */

void reconnectMQTT() {
  while (!mqtt.connected()) {
    Serial.println("Tentando se conectar ao Broker MQTT: " + String(BROKER_MQTT));
    if (mqtt.connect("anonymous")) {
      Serial.println("Conectado");
      mqtt.subscribe("DZ/gauge/temperature");

    } else {
      Serial.println("Falha ao Reconectar");
      Serial.println("Tentando se reconectar em 2 segundos");
      delay(2000);
    }
  }
}

void recconectWiFi() {
  while (WiFi.status() != WL_CONNECTED) {
    delay(100);
    Serial.print(".");
  }
}
```

## Aplicação Web

Como mencionado na introdução, a base da aplicação web utilizada neste projeto será a mesma do artigo relacionado, iremos apenas trocar o componente de ON/OFF pelo Gauge.

Para nos auxiliar na implementação do Gauge, iremos usar a biblioteca javascript denominada [c3js](http://c3js.org). Caso queira ver o exemplo da documentação e exemplo, basta acessa [este link](http://c3js.org/samples/chart_gauge.html).

Partindo para implementação, basicamente você irá alimentar o gauge a partir dos dados recebidos do sensor. Para este projeto foi colocado um intervalo de 1 segundo na publicação dos dados do nosso sensor.

Veja abaixo como ficou os arquivos principais: index.html e app.js, respectivamente.

### Arquivo index.html

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no"/>
    <title>Douglas Zuqueto - Sensoriamento Real Time com MQTT e Websockets</title>

    <!-- Load CSS -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/materialize/0.97.3/css/materialize.min.css">
    <link href="/static/css/c3.min.css" rel="stylesheet" type="text/css">

    <!-- Load JS -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/paho-mqtt/1.0.1/mqttws31.min.js"
            type="text/javascript"></script>
    <style media="screen">
        .app {
            margin-top: 20px;
        }
    </style>
</head>
<body>
<div class="container app">
    <h5 class="center">Sensoriamento Real Time com MQTT e Websockets</h5>
    <br>
    <form method="GET" action="#">
        <div class="row">
            <div class="input-field col s6">
                <input id="broker" type="text" class="validate">
                <label for="broker" class="active">Broker</label>
            </div>
            <div class="input-field col s6">
                <input id="port" type="text" class="validate">
                <label for="port" class="active">Port</label>
            </div>
        </div>
        <div class="input-field col s12">
            <input id="topic" type="text" class="validate">
            <label for="topic" class="active">Topic</label>
        </div>
        <div class="row">
            <input class="btn green col s5" type="submit" id="save" name="save" value="Salvar">
            <input class="btn blue col s5 right" type="button" id="connect" name="connect" value="Conectar">
        </div>
    </form>
    <div class="row">
        <div class="col s12">
            <div class="center">
                <!-- Gauge -->
                <div id='gauge'></div>
            </div>
            <br>
            <h6 class="center">
                Confira o artigo em:
                <a href="https://blog.dev/artigos/sensoriamento-em-real-time" target="_blank">
                    "Sensoriamento Real Time com MQTT e Websockets"
                </a>
            </h6>
        </div>
    </div>
    <!-- Patrocinio -->
    <div class="row">
        <div class="col s12 center">
            <a href="https://douglaszuqueto.com">
                <img src="/static/images/dz.png" width="15%" title="Douglas Zuqueto">
            </a>
        </div>
    </div>

    <!-- Load JS -->
    <script src="//code.jquery.com/jquery-1.11.3.min.js"></script>
    <script src="https://d3js.org/d3.v3.min.js"></script>
    <script src="/static/js/c3.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/materialize/0.97.3/js/materialize.min.js"></script>

    <!-- Load JS APP -->
    <script src="/static/js/app.js" type="text/javascript"></script>
</div>
</body>
</html
```

### Arquivo app.js

```js
/* json com configuracoes iniciais de conexao */
var json = {
    broker: 'test.mosquitto.org',
    topic: 'DZ/gauge/temperature',
    port: 8080
};

/* resgata as informações do localStorage caso existir */
if (JSON.parse(localStorage.getItem('mqtt'))) {
    json = JSON.parse(localStorage.getItem('mqtt'));
}

/* Instancia o paho-mqtt */
var mqtt = new Paho.MQTT.Client(
    json.broker,
    parseInt(json.port),
    "DZ-" + Date.now()
);

/* define aos eventos seus respectivos callbacks*/
mqtt.onConnectionLost = onConnectionLost;
mqtt.onMessageArrived = onMessageArrived;

function onConnectionLost(responseObject) {
    return console.log("Status: " + responseObject.errorMessage);
}
function onMessageArrived(message) {
    var msg = message.payloadString;
    console.log(message.destinationName, ' -- ', msg);

    if (msg > 50) {
        return false;
    }
    if (msg == gauge.data.values('temperature')[0]) {
        return false;
    }

    // metodo responsável por atualizar o Gauge
    gauge.load({
        columns: [
            ['temperature', msg]
        ]
    });

}

/* define aos eventos de Conexão seus respectivos callbacks*/
var options = {
    timeout: 3,
    onSuccess: onSuccess,
    onFailure: onFailure
};
function onSuccess() {
    console.log("Conectado com o Broker MQTT");
    mqtt.subscribe(json.topic, {qos: 1}); // Assina o Tópico
    Materialize.toast('Conectado ao broker', 1000);
}

function onFailure(message) {
    console.log("Connection failed: " + message.errorMessage);
}

/* função de conexão */
function init() {
    mqtt.connect(options); // Conecta ao Broker MQTT
}

/* Configuracao do Gauge */
var gauge = c3.generate({
    bindto: '#gauge',
    data: {
        columns: [
            ['temperature', 0]
        ],
        type: 'gauge',
    },
    gauge: {
        label: {
            format: function (value, ratio) {
                return value + ' ºC';
            },
            show: false
        },
        min: 0,
        max: 50,
        units: ' ºC',
    },
    color: {
        pattern: ['#227EAF', '#F97600'],
        threshold: {
            unit: 'ºC',
            max: 50,
            values: [10, 30]
        }
    },
    size: {
        height: 180
    }
});

/* App */
$(document).ready(function () {
    $('#broker').val(json.broker);
    $('#port').val(json.port);
    $('#topic').val(json.topic);

    /* Eventos de configuração */
    $('#save').on('click', function () {
        var broker, topic;
        broker = $('#broker').val();
        port = $('#port').val();
        topic = $('#topic').val();

        /* salva no localStorage os dados do formulário */
        localStorage.setItem("mqtt", JSON.stringify({broker: broker, port: port, topic: topic}));

        location.reload();
        return false;
    });

    $('#connect').on('click', function () {
        init();
    });
});

```
## Rodando o projeto

Para poder rodar o projeto, segue o mesmo esquema do artigo "Controle suas gpio's através da internet", com a única diferença que a pasta será referente a este projeto.

## Resultados

Como foi mencionado, nossa interface web não mudou em praticamente nada - basicamente foi tirado o botão ON/OFF e colocado o nosso gauge no lugar.

Caso queira ver como ficou a  interface web, basta acessar o domínio do [iot-br](http://broker.iot-br.com).

Quem quiser, todo código fonte está no repositório do github: [link](https://github.com/douglaszuqueto/douglaszuqueto-artigos/tree/master/sensoriamento-real-time-com-mqtt-e-websockets)

Você também pode utilizar esta aplicação web para efetuar seus testes, deixarei a aplicação online por tempo indeterminado. Para utilizar basta usar algum broker - inclusive o meu, apontar para seu tópico, porta da conexão e salvar, depois basta conectar :).


### Aplicação Web

Observe na imagem abaixo os resultados:

![img](https://douglaszuqueto.com/uploads/articles/artigo-19/print-desktop.png)

### Protótipo

Na imagem abaixo se refere ao protótipo construído para exemplificar este artigo.

![img](https://douglaszuqueto.com/uploads/articles/artigo-19/prototipo-.jpg)

## Finalizando

Veja como foi fácil integrar o nodemcu com uma aplicação web em tempo real, apenas pequenas modificações e obtivemos o monitoramento em tempo real do mcp9808.

Os próximos passos será realizar uma abstração de algumas bibliotecas para que fique mais fácil a implementação. Muitas vezes repetimos sempre as mesmas coisas no firmware como por exemplo conexão com rede wifi, conexão com mqtt e etc. Visto isso, estou realizar esta abstração para que me poupe algumas linhas de códigos e também fiquei com uma fácil leitura :p.

Outro passo importante será implementar uma aplicação web que suporte diversos componentes web, como o botão implementado no artigo aqui mencionado, o gauge, gráficos e afins, assim permitindo testes rápidos com o mqtt.

Por enquanto é isso galera. Forte abraço e até breve.

Se curtiu o artigo, não esqueça de compartilhar e curtir minha [página](https://www.facebook.com/douglaszuquetooficial) para ficar por dentro das novidades :).

## Referências
* [Adafruit io – Uma Nova Plataforma de IoT](http://blog.filipeflop.com/wireless/adafruit-io-plataforma-iot.html)
