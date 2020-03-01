## Introdução

Saindo um pouco do cenário da Raspberry PI e voltando para o famoso ESP8266, iremos neste artigo integrar o mesmo com o protocolo MQTT.

Este artigo já ficará de base para os próximos artigos e projetos que usaram esp juntamente com mqtt.

Neste artigo será implementado um simples script para ligar/desligar um led/ventilador/cafeteira, neste caso, por conta da praticidade será usado um led. Também será usado a mesma dashboard do artigo "[Controle suas gpio's através da internet](https://douglaszuqueto.com/artigos/controle-suas-gpio-atraves-da-internet)".

Muito legal essa maleabilidade né? Implementa para integrar com a rasp e de quebra, já consegue usar com esp8266 e com qualquer outro embarcado. Tudo isso graças ao protocolo e também a Web.

## Materiais utilizados
* NodeMCU 
* Protoboard
* Jumpers
* Led
* Resistor 100 Ohm

## Dependências do projeto

Para poder executar este projeto, você terá de ter a IDE do Arduino bem como as ferramentas necessárias para programar o ESP.

Também será necessário instalar a biblioteca [PubSubClient](https://github.com/knolleary/pubsubclient), que neste caso, é a que uso à algum tempo já. Para efetuar a instalação da biblioteca não tem mistério né? Só precisa baixar o zip e usar a interface do próprio arduino para adicioná-la. Ou até mais fácil, é usar o próprio gerenciador de bibliotecas da ide - eu usei este método :).

## Firmware

```c
// Libs
#include <ESP8266WiFi.h>
#include <PubSubClient.h>

// Vars
const char* SSID = "homewifi_D68"; // rede wifi
const char* PASSWORD = "09114147"; // senha da rede wifi

const char* BROKER_MQTT = "broker.iot-br.com"; // ip/host do broker
int BROKER_PORT = 8080; // porta do broker

// prototypes
void initPins();
void initSerial();
void initWiFi();
void initMQTT();

WiFiClient espClient;
PubSubClient MQTT(espClient); // instancia o mqtt

// setup
void setup() {
  initPins();
  initSerial();
  initWiFi();
  initMQTT();
}

void loop() {
  if (!MQTT.connected()) {
    reconnectMQTT();
  }
  recconectWiFi();
  MQTT.loop();
}

// implementacao dos prototypes

void initPins() {
  pinMode(D5, OUTPUT);
  digitalWrite(D5, 0);
}

void initSerial() {
  Serial.begin(115200);
}
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

// Funcão para se conectar ao Broker MQTT
void initMQTT() {
  MQTT.setServer(BROKER_MQTT, BROKER_PORT);
  MQTT.setCallback(mqtt_callback);
}

//Função que recebe as mensagens publicadas
void mqtt_callback(char* topic, byte* payload, unsigned int length) {

  String message;
  for (int i = 0; i < length; i++) {
    char c = (char)payload[i];
    message += c;
  }
  Serial.println("Tópico => " + String(topic) + " | Valor => " + String(message));
  if (message == "1") {
    digitalWrite(D5, 1);
  } else {
    digitalWrite(D5, 0);
  }
  Serial.flush();
}

void reconnectMQTT() {
  while (!MQTT.connected()) {
    Serial.println("Tentando se conectar ao Broker MQTT: " + String(BROKER_MQTT));
    if (MQTT.connect("ESP8266-ESP12-E")) {
      Serial.println("Conectado");
      MQTT.subscribe("DZ/esp8266-01/led");

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

O script acima basicamente fará 3 coisas: se conectar à rede wifi, se conectar ao broker e ficará na escuta por eventos de publish.

Toda vez que que uma mensagem for publicada e o tópico for *DZ/esp8266-01/led*  o esp estará escutando por este tópico, logicamente quando receber a mensagem, o mesmo irá tratar e efetuar a regra de negócio, que no nosso caso como se trata de uma base , será apenas acionar um led ou algo qualquer conectado ao shield rele por exemplo.

Acho que não tem muito mais o que explicar, devido ao fato de se tratar de uma conexão simples à rede wifi juntamente com uma simples conexão com o broker mqtt.

## Testando

Neste teste eu tive que mudar de broker mqtt, no outro artigo foi usado o broker do mosquitto.org: *test.mosquitto.org*, porém por algum motivo desde o inicio da semana(09/01/2017) ele não esta funcionando - ao menos para mim :p.

Dado à esse motivo, como eu tenho uma VPS, acabei instalando e configurando um simples broker mqtt com websockets. Logo logo, faço um artigo demonstrando passo à passo de como proceder com a instalação e configuração.

Para de fato realizar o teste usarei a mesma interface usada no outro [artigo](https://douglaszuqueto.com/artigos/controle-suas-gpio-atraves-da-internet), somente coloquei a adição de um novo campo no formulário para tornar a conexão mais genérica com isso acaba me beneficiando e também quem for utilizá-la.

Não esqueça, para ter acesso à ela, você so precisa acessar o [iot-br.com](http://iot-br.com).

Veja como ficou a 'nova' interface na imagem abaixo:

![img](https://douglaszuqueto.com/uploads/articles/artigo-17/print-sistema.png)

## Finalizando

Então como dito, seria um artigo curto que somente servirá de base para os futuros artigos e projetos. Nos projetos que envolvam o embarcado pretendo abordar o uso de sensores e atuadores com o controle à partir de uma aplicação web.

Por enquanto é isso pessoal. Abraços.

**Curtiu? Não deixe de seguir a Fan Page [Douglas Zuqueto](https://www.facebook.com/douglaszuquetooficial)**

## Referências
* [PubSubClient](https://github.com/knolleary/pubsubclient)
* [ESP8266 - Arduino](https://github.com/esp8266/Arduino)