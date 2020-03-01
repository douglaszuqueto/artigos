## Introdução

Neste rápido artigo, fazeremos a integração do sensor de temperatura MCP9808 com a Raspberry PI3, será algo simples, apenas para ficar aqui documentado a utilização caso precise de uma base em futuros projetos.

## Dependências

Para utilizar o sensor na raspberry pi, recomendo ver [este artigo](https://learn.adafruit.com/mcp9808-temperature-sensor-python-library/overview) da Adafruit, onde ela aborda a utilização do sensor na rpi e também na beaglebone black. De quebra já tem o esquemático de ligação e passo a passo para instalação do biblioteca necessária para o funcionamento.

## Materiais utilizados

* [Sensor de temperatura MCP9808](https://goo.gl/N50AfL)
* [Raspberry PI 3](https://goo.gl/ZKqkAn)
* [Jumpers macho-fêmea](https://goo.gl/Bhmmfl)

## Script de exemplificação

No próprio [repositório clonado](https://github.com/adafruit/Adafruit_Python_MCP9808), tem um simples exemplo para você rodar logo após que instalar todas dependências necessárias bem como a instalação da biblioteca.

Veja o trecho minificado retirado do exemplo que conta no repositório:

```
import time
import Adafruit_MCP9808.MCP9808 as MCP9808

sensor = MCP9808.MCP9808()
sensor.begin()

print('Press Ctrl-C to quit.')
while True:
    temp = sensor.readTempC()
    print('Temperature: {0:0.3F}*C'.format(temp))
    time.sleep(1.0)
```

O exemplo é simples, ele simplesmente printa em seu terminal a temperatura do sensor em *Graus Celsius* a cada 1 segundo. Veja como deverá ficar a saída assim que rodarmos o script:

```
pi@raspberrypi:~/utilizando-o-mcp9808-na-raspberry-pi $ python mcp9808-example.py
Press Ctrl-C to quit.
Temperature: 28.875*C
Temperature: 28.812*C
Temperature: 28.812*C
Temperature: 28.812*C
Temperature: 28.812*C
Temperature: 28.875*C
Temperature: 28.812*C
Temperature: 28.812*C
Temperature: 28.875*C
```

## Integrando com nossa aplicação mqtt

Como de praxe, vamos enviar os dados da temperatura via mqtt para nossa aplicação web. Será a mesma abordagem relatada no artigo [Sensoriamento Real Time com MQTT e Websockets](https://douglaszuqueto.com/artigos/sensoriamento-real-time-com-mqtt-e-websockets). Única diferença é que será utilizado o python pois estamos na raspberry pi.

Veja abaixo como ficará o script de integração do sensor com o mqtt:

```
import sys
import time
import paho.mqtt.client as mqtt
import Adafruit_MCP9808.MCP9808 as MCP9808

broker = "broker.iot-br.com"
port = 8080
keppAlive = 60
topic = 'DZ/rasp/temperature'

sensor = MCP9808.MCP9808()
sensor.begin()

def on_connect(client, userdata, flags, rc):
  print("[STATUS] Conectado ao Broker. Resultado de conexao: " + str(rc))

  client.subscribe(topic)

try:
  print("[STATUS] Inicializando MQTT...")

  client = mqtt.Client()
  client.on_connect = on_connect

  client.connect(broker, port, keppAlive)

  while(True):
    temp = sensor.readTempC()
    temp = '{0:0.2F}'.format(temp)
    print("Temperature: " + temp + "*C")

    client.publish(topic, temp)

    time.sleep(1.0)

except KeyboardInterrupt:
  print "\nScript finalizado."
  sys.exit(0)
```

Não tem nada demais, é simplesmente a base python do mqtt que utilizamos nos artigos anteriores e a junção com o script de leitura mencionado no tópico anterior. 

Executando o script você terá uma saída no seu terminal, e também terá o feedback na aplicação web que desenvolvemos no artigo mencionado acima.

```
pi@raspberrypi:~/projects/github/douglaszuqueto-artigos/utilizando-o-mcp9808-na-raspberry-pi $ python mcp9808.py 
[STATUS] Inicializando MQTT...
Temperature: 28.81*C
Temperature: 28.81*C
Temperature: 28.88*C
Temperature: 28.81*C
Temperature: 28.88*C
Temperature: 28.81*C
Temperature: 28.81*C
^C
Script finalizado.
```

Para visualizar em tempo real o valor enviado pelo sensor, basta acessar a aplicação web desenvolvida no artigo citado: [broker.iot-br.com](http://broker.iot-br.com).

## Finalizando

Bem, como dito na introdução deste artigo, o mesmo seria mais para referência para próximos projetos mais complexos. Mas deixo aqui o registro pois tenho certeza que para alguém também será útil :P.

**Obs:** Eu utilizo o mcp como exemplo pois é o único sensor de temperatura que possuo em casa. Mas caso você acompanhe meus artigos já viu a maleabilidade que se tem para outros tipos de aplicações, seja com sensores, embarcados ou software. Com esta abordagem não é diferente, você pode pegar por exemplo o famoso ***DTH22***, procurar sua respectiva lib em python e efetuar a mudança, e tudo estará funcionando :).

Um abraço e até a próxima ;).

## Referências

* [MCP9808 Temperature Sensor Python Library](https://learn.adafruit.com/mcp9808-temperature-sensor-python-library/overview)
* [Adafruit_Python_MCP9808](https://github.com/adafruit/Adafruit_Python_MCP9808)
* [Sensoriamento Real Time com MQTT e Websockets](https://douglaszuqueto.com/artigos/sensoriamento-real-time-com-mqtt-e-websockets)