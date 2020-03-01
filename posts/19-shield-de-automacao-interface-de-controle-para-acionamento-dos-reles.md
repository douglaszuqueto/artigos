## Introdução

No último artigo que tratamos sobre o shield rele foi abordado a utilização do material oferecido pela loja para realizar o acionamento dos reles. Caso ainda não tenha lido o artigo, fica [aqui](https://douglaszuqueto.com/artigos/shield-de-automacao-acionando-reles) a referência.

No tópico *Próximos passos* que abordei no artigo, mencionei a criação de uma aplicação web para controle dos 10 reles utilizando o protocolo **MQTT** juntamente com **WebSockets**, para que assim, possa-se garantir uma comunicação em *'tempo real'*.

## Materiais necessários

* [Raspberry PI 3](https://goo.gl/ZKqkAn);
* [Shield de Automação](https://goo.gl/uN0buU);
* Fonte de alimentação 12V 2A(ou superior);

## Dependências

Comentamos nos artigos anteriores que para o acionamento dos reles é utilizado o CI **MCP23017**, portanto para abstrair esta camada I2C utilizaremos uma biblioteca desenvolvida pela Adafruit.

A biblioteca está hospedada no Github, portanto abra [este link](https://github.com/adafruit/Adafruit_Python_GPIO) em uma nova aba. Abrindo o link você logo verá uma passo a passo para realizar a instalação da biblioteca. Deixo abaixo os passos para simples conferência e observações.

```
sudo apt-get update
sudo apt-get install build-essential python-pip python-dev python-smbus git
git clone https://github.com/adafruit/Adafruit_Python_GPIO.git
cd Adafruit_Python_GPIO
sudo python setup.py install
```

Caso você tenha efetuado a leitura do [artigo](https://douglaszuqueto.com/artigos/shield-de-automacao-acionando-reles), perceberá que os passos ***1*** e ***2***, já foram realizados, portanto basta apenas clonar o repositório e seguir o restante das instruções. 

## Script de exemplificação

Antes de desenvolver o script final para a Raspberry PI, vamos validar se a biblioteca está funcionando corretamente. Para isso veja abaixo como ficará o script de blink do **rele 1**:

```python
import sys
import time

import Adafruit_GPIO.MCP230xx as MCP
import Adafruit_GPIO as GPIO

mcp = MCP.MCP23017()
mcp.setup(0, GPIO.OUT)

relayName = 0

try:

  while(True):
    mcp.output(relayName, True)
    time.sleep(1)
    mcp.output(relayName, False)
    time.sleep(1)

except KeyboardInterrupt:
  mcp.output(relayName, False)
  print "\nScript finalizado."
  sys.exit(0)
```

Observe que na estrutura não tem nada demais. Realizamos a importação das libs necessárias, instanciamos nosso objeto - MCP23017, depois foi efetuada a configuração do *pino 0* como *Output*. E para realizar o *blink*, criamos um laço while com o objetivo de alternar o valor do rele de 1 em 1 segundo

Viu que foi bem simples? Nó próximo tópico já integraremos o script com o mqtt.

## Script Python

Como mencionado no tópico anterior, neste tópico apenas iremos realizar a integração com o *mqtt*, algo simples visto que já abordamos aqui no Blog alguns artigos sobre o assunto.

Portanto veja abaixo como ficará essa união:

```python
import sys
import time
import paho.mqtt.client as mqtt

import Adafruit_GPIO.MCP230xx as MCP
import Adafruit_GPIO as GPIO

broker    = "broker.iot-br.com"
port      = 8080
keppAlive = 60
topic     = 'DZ/rasp/relay/#'

mcp = MCP.MCP23017()
mcp.setup(0, GPIO.OUT)

def on_connect(client, userdata, flags, rc):
  print("[STATUS] Conectado ao Broker. Resultado de conexao: " + str(rc))

  client.subscribe(topic)

def on_message(client, userdata, msg):
  message = str(msg.payload)  # converte a mensagem recebida
  print("[MSG RECEBIDA] Topico: " + msg.topic + " / Mensagem: " + message)

  topic = str(msg.topic).split('/')
  relayName = int(topic[3])

  if message == '1':
    mcp.output(relayName, True)
  else:
    mcp.output(relayName, False)

  time.sleep(0.1)

try:
  print("[STATUS] Inicializando MQTT...")

  client            = mqtt.Client()
  client.on_connect = on_connect
  client.on_message = on_message

  client.connect(broker, port, keppAlive)
  client.loop_forever()

except KeyboardInterrupt:
  print "\nScript finalizado."
  sys.exit(0)
```

Pegando a arquitetura mqtt já abordada no blog e o script de exemplo que desenvolvemos para teste do rele, temos como resultado a estrutura acima :P.

Basicamente o segredo se encontra na função **on_message**, onde definimos nossa regra de negócio. Veja o trecho referente abaixo:

```python
message = str(msg.payload)  # converte a mensagem recebida
print("[MSG RECEBIDA] Topico: " + msg.topic + " / Mensagem: " + message)

topic = str(msg.topic).split('/')
relayName = int(topic[3])

if message == '1':
	mcp.output(relayName, True)
else:
	mcp.output(relayName, False)

time.sleep(0.1)
```

Observe que é realizado um tratamento no tópico, é realizado um *split* no tópico referente para que possamos pegar a última posição, que é a identificação do rele, logicamente ele está diretamente associado ao pino do mcp.

Nosso tópico terá esta estrutura: **DZ/rasp/relay/1**, note a última posição - *1*, esse será nosso rele que será acionado, e o que vir no corpo da mensagem, será o valor correspondente ao *Ligado/Desligado(1/0)*.

Caso queira testar o que foi desenvolvido até o momento, você pode usar o *cli do mosquitto* :).

```
mosquitto_pub -h broker.iot-br.com -p 8080 -t DZ/rasp/relay/1 -m 1
mosquitto_pub -h broker.iot-br.com -p 8080 -t DZ/rasp/relay/1 -m 0
```

## Interface Web

Dando continuidade ao desenvolvimento iremos desenvolver a interface gráfica e a interação necessária para realizarmos de fato o acionamento dos reles. A base será a mesma das aplicações web anteriores, apenas mudamos o corpo da aplicação - neste caso será switchs para realizar o On/Off dos reles.

### Arquivo: index.html

Para começarmos, será desenvolvido o 'layout', que apenas se trata de 10 switchs para fazer o devido acionamento do rele. 

O componente utilizado você pode conferir a forma original dele [neste link](http://materializecss.com/forms.html). Apenas dei uma reforçada no layout para ter uma melhor aparência :).

No trecho abaixo você pode conferir a parte principal do nosso arquivo html, que será a estrutura necessária para acionar o seu devido rele.

```html
 <div class="switch">
          <label>
            Rele 1
            <input type="checkbox" class="checkbox" name="0" disabled>
            <span class="lever"></span>
          </label>
</div>
```

Veja que no fundo nada mais é do que um *checkbox* elegante :P. Esse trecho você pode observar primeiramente que se refere ao rele 1 de nosso shield, observe o principal atributo que é o **name**, neste caso será **0**, pois é a numeração correspondente ao *mcp23017*.

Para cada componente desses, terá um evento de associação: o **change**. Este evento será responsável por recuperar o *name* do componente acionado e irá enviar ao mqtt o tópico contendo esse name juntamente com o *value* do checkbox - 0 ou 1.

### Arquivo: app.js

No arquivo *app.js* possui a mesma estrutura das aplicações anteriores, apenas foi feita sua modificação para que a regra de negócio se adapte à ele.

Portanto não vamos explicar linha por linha e sim apenas as 2 principais funções do nosso fluxo, são elas: **sendMessage** e **createMessage**. Veja abaixo a estrutura dos mesmos.

```js
  const createMessage = (topic, value) => {
    let message  = new Paho.MQTT.Message(value.toString());
    message.destinationName = `DZ/rasp/relay/${topic}`;
    mqtt.send(message);

    Materialize.toast(`Rele ${parseInt(topic) + 1}: ${value === 1 ? 'Ligado' : 'Desligado'}`, 1000);
  };

  function sendMessage(el) {
    let topic = el.srcElement.name;
    let value = this.checked ? this.value = 1 : this.value = 0;

    createMessage(topic, value);
  }
```

A função sendMessage, será a função atrelada ao evento que mencionamos acima, o **change**, ela irá recuperar o *name* do checkbox, neste caso será 0(foi mencionado no trecho html) e também recuperará o valor de acordo com o acionamento - *on/off*. Com as informações necessárias em mãos, repassamos a responsabilidade para a função *createMessage*.

A função **createMessage**, nada mais irá fazer que montar nosso pacote contendo as informações para que possamos envia-lá via websocket, e como nossa rasp estará na escuta através do tópico assinado, ela irá receber isso e fazer o tratamento que foi abordado no tópico acima.

## Resultados

Para acessar a aplicação aqui desenvolvida, basta abrir [este link](http://broker.iot-br.com).

Como resultados podemos observar os prints abaixo.

### Aplicação na web

![img](https://douglaszuqueto.com/uploads/articles/artigo-24/print-desktop.png)

### Aplicação no mobile

![img](https://douglaszuqueto.com/uploads/articles/artigo-24/print-mobile.jpg)

### Log da raspberry pi

```
[STATUS] Inicializando MQTT...
[STATUS] Conectado ao Broker. Resultado de conexao: 0
[MSG RECEBIDA] Topico: DZ/rasp/relay/5 / Mensagem: 1
[MSG RECEBIDA] Topico: DZ/rasp/relay/5 / Mensagem: 0
[MSG RECEBIDA] Topico: DZ/rasp/relay/6 / Mensagem: 1
[MSG RECEBIDA] Topico: DZ/rasp/relay/6 / Mensagem: 0
[MSG RECEBIDA] Topico: DZ/rasp/relay/5 / Mensagem: 1
[MSG RECEBIDA] Topico: DZ/rasp/relay/5 / Mensagem: 0
[MSG RECEBIDA] Topico: DZ/rasp/relay/1 / Mensagem: 1
[MSG RECEBIDA] Topico: DZ/rasp/relay/1 / Mensagem: 0
[MSG RECEBIDA] Topico: DZ/rasp/relay/5 / Mensagem: 1
[MSG RECEBIDA] Topico: DZ/rasp/relay/5 / Mensagem: 0
[MSG RECEBIDA] Topico: DZ/rasp/relay/5 / Mensagem: 1
[MSG RECEBIDA] Topico: DZ/rasp/relay/5 / Mensagem: 0
[MSG RECEBIDA] Topico: DZ/rasp/relay/6 / Mensagem: 1
[MSG RECEBIDA] Topico: DZ/rasp/relay/6 / Mensagem: 0
[MSG RECEBIDA] Topico: DZ/rasp/relay/7 / Mensagem: 1
[MSG RECEBIDA] Topico: DZ/rasp/relay/7 / Mensagem: 0
[MSG RECEBIDA] Topico: DZ/rasp/relay/8 / Mensagem: 1
```

## Finalizando

Tenho só uma coisa a dizer: **Do caralho**, simples assim haha.

Tirando a brincadeira, eu curti muito mesmo o resultado, claro que é uma aplicação boba, mas você estar aqui, controlando remotamente algo - me refiro remotamente pois meu Broker está na Nuvem -, isso é muito satisfatório, muito mesmo :). Como se diz aqui pelo Sul: ** Mais feliz que criança nova quando ganha um pacote de bala** haha.

Código desenvolvido no projeto: [link](https://github.com/douglaszuqueto/douglaszuqueto-artigos/tree/master/shield-de-automacao-interface-de-controle-para-acionamento-dos-reles).

## Próximos passos

O próximo passo será fazer algo mais sério, colocar uma integração com banco de dados para gravar um log de todas as ações, talvez fazer uma área de login, atribuir nome aos reles e por ai vai.

Obrigado pela leitura e até logo pessoal. Abraços :)

## Referências
* [MaterializeCSS - Forms](http://materializecss.com/forms.html);
* [Adafruit_Python_GPIO](https://github.com/adafruit/Adafruit_Python_GPIO);
* [CSS Toggle Switch Examples](http://callmenick.com/post/css-toggle-switch-examples);