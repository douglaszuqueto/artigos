Neste artigo irei relatar sobre meus primeiros passos até conseguir fazer meu primeiro blink na raspberry pi.

**OBS:** Sempre é bom ressaltar - pelo menos neste artigo: O que será retratado aqui, será simplesmente o que eu aprendi antes de postar este artigo, ou seja - adquiri o conhecimento e tentei repassar a experiência e aprendizado para vocês sobre os passos que percorri até chegar em um Blink propriamente dito, beleza?.  :)

## Introdução

O primeiro passo que efetuei foi ir atrás de um Pinout(pinagem) da Raspberry PI 3 para tomar conhecimento da identificação e funcionalidade dos pinos. Consegui o pinout da imagem abaixo no site da Element14.

![img](https://douglaszuqueto.com/uploads/articles/artigo-13/rpi3-pinout.png)

Para mim foi relativamente fácil associar as GPIO por conta do conhecimento que já tenho com placas arduino e esp's.

A única coisa que deveria ficar atento no que tange GPIO's, seria a tensão de funcionamento bem como o nível lógico, pois ambos trabalham com 3.3V.  O medo mesmo, era torrar a placa haha.

## Materiais utilizados

* 1 Rasbperry Pi 3;
* 1 Led;
* Jumpers;
* 1 Resistor 100 Ohm;
* Protoboard para montagem do circuito;

## Circuito

O circuito demonstrado abaixo é bem simples, portanto não necessita de demais explicações.

Para quem se interessa, o software usado para montar o esquemático do circuito é o [Fritzing](http://fritzing.org/home/) - no qual se trata de uma solução open source-hardware voltado para makers.

![img](https://douglaszuqueto.com/uploads/articles/artigo-13/blink-rpi3.png)

## Dependências do script

O script que será desenvolvido neste artigo possui uma dependência de software - o pacote **RPi.GPIO**, pelo que observei ele já vem instalado por default no Raspbian.

Para saber mais detalhes do pacote, veja este [link](https://sourceforge.net/p/raspberry-gpio-python/wiki/Home/)

## Script comentado

Neste primeiro momento será utilizada a linguagem Python - escolhi ela pois na maioria dos projetos que vejo, ela é mais utilizada. Depois pretendo abranger outras como C, Go e JS(NodeJS).

Caso você esteja interessado / curioso,  poderá acompanhar os exemplos e simples projetos em um repositório que criei no github: [link](https://github.com/douglaszuqueto/raspberry-examples). Seria bacana se todos começassem a seguir essa adoção e manter seus projetos/testes centralizados no Github :P.

```python
import RPi.GPIO as GPIO # Aqui e importado o pacote de GPIO para que assim o script tenha acesso as GPIO da rpi
import time # Importa o pacote time - semelhante ao delay do arduino

GPIO.setwarnings(False) # Aqui e setado os 'warnings' como falso, para nao mostra-los no console
GPIO.setmode(GPIO.BOARD) # Seta o tipo de pinagem da gpio, neste caso sera BOARD
GPIO.setup(11, GPIO.OUT) # seta a gpio 11 como saida - semelhante ao pinMode do arduino

# Aqui e criado uma funcao para ser invocada no escopo do script
def led( pin, delay ):
    GPIO.output(pin, GPIO.HIGH) # seta a gpio como HIGH - semelhante ao digitalWrite do arduino
    time.sleep(delay) # seta um delay com base no tempo passado no parametro da funcao
    GPIO.output(pin, GPIO.LOW) # coloca o nivel logico em LOW
    time.sleep(delay)

try:
# Aqui e o loop do script - semelhante ao loop() do arduino
    while True:
        led(11, 1) # invoca a funcao led passando como parametro o pino do led bem como o tempo de delay, que neste caso e 1 segundo

except KeyboardInterrupt:
    GPIO.cleanup() # resetara o status de todas gpio, assim caso fosse interrompa o script e o led estivesse acesso, essa chamada fazera com que o le seja apagado

```

Caso deseje ver o código no Github aqui está o [link](https://github.com/douglaszuqueto/douglaszuqueto-artigos/blob/master/hello-world-com-gpios-na-rpi3/led.py).

Ressalto também, que neste repositório mencionado acima, será centralizado todos os scripts que será desenvolvido no decorrer dos artigos. Ambos serão separados pelo título do mesmo.

## Executando

A execução é bem simples. Basta navegar até o diretório do script e executar o comando abaixo:

```
python led.py
```

## Resultado

Caso queira ver o resultado, o mesmo está em meu [Instagram](https://www.instagram.com/p/BOK9JPOjTt3/?taken-by=douglaszuquetooficial).

## WiringPI

WiringPI é uma biblioteca escrita em C para que você possa controlar a GPIO do RPI sem precisar colocar a mão em programação. Portanto, caso você não manje de programação, consegue manipular as gpio's à partir da linha de comando.

### Instalação

Basicamente você irá clonar o repositório do github e depois fazer o build da biblioteca.

**Passos**

* 1º Clone o repositório: **git clone git://git.drogon.net/wiringPi**
* 2º Entre dentro da pasta **wiringPi**
* 3º Efetue o build: **./build**

### Acionando o led

Para acionar um led, você deverá seguir a mesma lógica de controle efetuado no script em python: **Deverá definir o modo da porta**, e depois **irá enviar o nível lógico**.

* Definindo a gpio como saída: **gpio mode 0 out**
* Ligando o led: **gpio write 0 1**
* Desligando o led: **gpio write 0 0**

Veja como ficou no terminal:

```
gpio mode 0 out # define o pino como uma saída
gpio write 0 1 # envia o nível lógico 1 - ligado
gpio write 0 0 # envia o nível lógico 0 - desligado
```

Para demais informações, acesse o [link](http://wiringpi.com/) do site.

## Finalizando

Este artigo parece simples, mas o mesmo levará uma base para os próximos artigos, como integração com protocolo MQTT, interface para controle à partir da Web, banco de dados, entre outras integrações.

O Próximo artigo já está à caminho e o mesmo deverá abordar uma integração simples com o protocolo MQTT.

## Referências

* [Pinout RPI3 - Element14](https://www.element14.com/community/docs/DOC-73950/l/raspberry-pi-3-model-b-gpio-40-pin-block-pinout#)
* [Fritzing](http://fritzing.org/home/)
* [RPi.GPIO](https://sourceforge.net/p/raspberry-gpio-python/wiki/Home/)
* [WiringPi](http://wiringpi.com/)

## Redes Sociais

Se estiver curtindo os artigos, não deixe de acompanhar minhas Redes Sociais e não esqueça de curtir, comentar e compartilhar os artigos :P

* [Facebook](https://www.facebook.com/douglaszuquetooficial)
* [Instagram](https://www.instagram.com/douglaszuquetooficial/)
* [Github](https://www.github.com/douglaszuqueto/)
* [Telegram](https://telegram.me/douglaszuqueto)