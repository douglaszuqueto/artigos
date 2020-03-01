No [artigo anterior](https://douglaszuqueto.com/artigos) foi relatado sobre Hello World com a GPIO na rpi, usando como exemplo o famoso Blink. Já neste artigo será abordado uma implementação de um script python que usará o protocolo MQTT.

## Introdução

Como mencionado acima, o exemplo que será construído neste artigo terá a integração com MQTT. Faz algum tempo que já tenho contato com o uso do MQTT, porém era no ecossistema ESP8266.

Diante do conhecimento que já tinha, foi bem tranquilo pesquisar e implementar o uso do MQTT na rasp juntamente com python. Como referência será usado o artigo do Pedro Bertoleti - "[Raspberry Pi 3 na IoT - MQTT e Python](https://www.embarcados.com.br/raspberry-pi-3-na-iot-mqtt-e-python/)".

A implementação e instalação da biblioteca foi bem tranquilo, o maior empecilho era o Python, mas com a escrita e pesquisa para o primeiro artigo sobre rasp e gpio, consegui entender facilmente o fluxo da aplicação e podemos dizer que agora *'consigo me virar'*.

Caso queiram ver a implementação do Pedro, coloquei o script no repositório dos exemplos -[link](https://github.com/douglaszuqueto/raspberry-examples/blob/master/python/mqtt/pedro_embarcados.py).

**OBS:** Para quem desconhece o protocolo mqtt, deixo algumas referências abaixo. Como o uso do mqtt já está bem popular nos projetos, não teria o porque de escrever sobre o protocolo em si sendo que já tem muitos artigos na internet - inclusive brasileiro.

* [MQTT - Protocolos para IoT - Embarcados](https://www.embarcados.com.br/mqtt-protocolos-para-iot/)
* [Serie MQTT Essentials - HiveMQ](http://www.hivemq.com/mqtt-essentials/)

## Lista de Materiais

Será usado a mesma lista de materiais do artigo anterior, caso ainda não tenha lido o artigo anterior, leia-o [aqui](https://douglaszuqueto.com/artigos/hello-world-com-gpio-na-rpi3).

## Instalação das dependências

Para este exemplo, você terá que ter duas dependências instaladas: RPi.GPIO(já vem instalada no Raspbian) e paho-mqtt. Faltando apenas uma dependência, iremos instala-la através do PIP do Python.

* Paho-MQTT
	```
	sudo pip install paho-mqtt
	```
	
## Criação do script

Para criar o script, será usado como base o script desenvolvido pelo Pedro e também o script que foi desenvolvido no [artigo](https://douglaszuqueto.com/artigos/hello-world-com-gpio-na-rpi3) anterior. O script será relativamente simples e de fácil entendimento.

```python
import sys
import paho.mqtt.client as mqtt # importa o pacote mqtt
import RPi.GPIO as GPIO

ledPin = 11

broker = "test.mosquitto.org" # define o host do broker mqtt'
port = 1883 # define a porta do broker
keppAlive = 60 # define o keepAlive da conexao
topic = 'DZ/#' # define o topico que este script assinara

GPIO.setwarnings(False)
GPIO.setmode(GPIO.BOARD)
GPIO.setup(ledPin, GPIO.OUT)

# funcao on_connect sera atribuida e chamada quando a conexao for iniciada
# ela printara na tela caso tudo ocorra certo durante a tentativa de conexao
# tambem ira assina o topico que foi declarado acima
def on_connect(client, userdata, flags, rc):
    print("[STATUS] Conectado ao Broker. Resultado de conexao: "+str(rc))

    client.subscribe(topic)

# possui o mesmo cenario que o on_connect, porem, ela sera atrelada ao loop
# do script, pois toda vez que receber uma nova mensagem do topico assinado, ela sera invocada
def on_message(client, userdata, msg):

    message = str(msg.payload) # converte a mensagem recebida
    print("[MSG RECEBIDA] Topico: "+msg.topic+" / Mensagem: "+ message) # imprime no console a mensagem

    # testara se o topico desta mensagem sera igual ao topico que queremos, que neste caso remete ao led
    if msg.topic == 'DZ/rasp/led':

        # basicamente nessa condicional testara se o valor recebido sera 1, sendo 1 acende o led
        # e, caso receber qualquer outra coisa, apagara o led
        if(message == '1'):
            GPIO.output(ledPin, GPIO.HIGH)
        else:
            GPIO.output(ledPin, GPIO.LOW)

try:
    print("[STATUS] Inicializando MQTT...")

    client = mqtt.Client() # instancia a conexao
    client.on_connect = on_connect # define o callback do evento on_connect
    client.on_message = on_message # define o callback do evento on_message

    client.connect(broker, port, keppAlive) # inicia a conexao
    client.loop_forever() # a conexao mqtt entrara em loop ou seja, ficara escutando e processando todas mensagens recebidas

except KeyboardInterrupt:
    print "\nScript finalizado."
    GPIO.cleanup()
    sys.exit(0)

```

[Código fonte do script](https://github.com/douglaszuqueto/douglaszuqueto-artigos/blob/master/do-hello-world-a-integracao-com-mqtt/mqtt.py)

**OBS** Para que você possa testar tranquilamente, sugiro trocar o tópico para seu nome, ou algo que seja 'único', pois assim você correrá menos risco de ser atrapalhado nos testes caso outra pessoa esteja usando este mesmo tópico.

Para rodar o script, é só executar o comando abaixo:
```
 python script.py
 ```

## Testando

Para efetuar os testes, você poderá usar diversas ferramentas. Neste caso será usado duas em específico, um utilitário por linha de comando e um aplicativo mobile.

### Mosquitto Pub

O mosquitto_pub é um utilitário do mosquitto.org que é executado de forma simples a partir da linha de comando. Caso queira mais informações, acesse a [documentação](https://mosquitto.org) do utilitário.

Para publicarmos uma mensagem é bem simples, basicamente você terá 3 parâmetros no comando, sendo eles:
* -h | host do broker
* -t | tópico
* -m | mensagem

Nosso comando ficará da seguinte forma para ligar um led.
```
mosquitto_pub -h test.mosquitto.org -t DZ/rasp/led -m 1
```
 ... e caso queira desliga-lo, você precisa somente repetir o comando com uma mensagem diferente de **1** que o led se apagará.

### Aplicativo Mobile

Existem diversos clientes para mobile, neste caso será usado o [MyMQTT](https://play.google.com/store/apps/details?id=at.tripwire.mqtt.client), um cliente simples e eficaz para os testes.

Ao abrir o aplicativo, basicamente você deverá configurar a conexão com o broker mqtt colocando o host, que no nosso caso, estamos usando o broker do mosquitto: ***test.mosquitto.org.***

Já para o envio de mensagem, é preciso entrar no menu *Publish* , entrar com o tópico e a mensagem, após publicar, seu led deverá acender. Veja na imagem abaixo a tela de publish.

![img](https://douglaszuqueto.com/uploads/articles/artigo-14/mymqtt-publish.png)


## Próximos passos

A ideia inicial deste artigo seria integrar com MQTT e já criar uma interface web para controle do Led, porém resolvi dividi-lo para ficar de fácil entendimento e separar as camadas.

Portanto no próximo artigo iremos desenvolver uma simples interface web para controlar o led, e provavelmente será usado Python juntamente com um simples framework web para python chamado *Flask*.

## Finalizando

Projetos envolvendo MQTT sem dúvidas, eram os que mais eu aguardava para desenvolver, pois geralmente envolve N camadas, e eu realmente gosto muito dessa parte - integrar N tecnologias e N linguagens.

Sem dúvidas, foi um passo bem legal, e de quebra estou aprendendo python :).

Por enquanto é isso galera. Um abraço e até breve.

## Referências

* [Raspberry Pi 3 na IoT - MQTT e Python - Embarcados](https://www.embarcados.com.br/raspberry-pi-3-na-iot-mqtt-e-python/)
* [Paho MQTT - Python](https://github.com/eclipse/paho.mqtt.python/tree/develop)
* [Mosquitto Publish](https://mosquitto.org/man/mosquitto_pub-1.html)