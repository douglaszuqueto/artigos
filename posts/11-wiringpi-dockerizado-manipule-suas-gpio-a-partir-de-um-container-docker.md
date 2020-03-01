## Introdução

Desde que começou os artigos sobre a Raspberry foi tratado de alguns assuntos, um deles, foi o uso do Docker na rpi3, caso ainda não tenha lido o artigo, aqui está o [link](https://douglaszuqueto.com/artigos/docker-na-raspberry-pi-sim-e-possivel). Já quando começamos a tratar sobre GPIO's, mencionamos em um tópico o uso do WiringPI para fazer rápidas manipulações das gpio's da raspberry pi, deixo a referência do artigo [aqui](https://douglaszuqueto.com/artigos/hello-world-com-gpio-na-rpi3).

Então, neste artigo - unindo o útil ao agradável, demonstrarei como usar o WiringPI de forma Dockerizada como Comando, ou seja, através de um container docker preparado, você executará comandos de forma transparente, como se o wiringpi estivesse instalado localmente na rpi, porém ele será executado à partir de um container, interessante né?.

## Imagem Docker

Fazendo algumas pesquisas sobre manipulação de gpio's através de um container Docker, cheguei à uma imagem chamada [rpi-gpio](https://hub.docker.com/r/hypriot/rpi-gpio/). Esta imagem possui o wiringpi embarcado e é mantido pelo Hypriot. A imagem é extremamente leve, possuindo apenas 1mb.

## Efetuando o pull da imagem

Sem muito mistério, basta executar o comando abaixo para realizar o pull da nossa imagem.

```
docker pull hypriot/rpi-gpio
```

## Executando o container

Com a nossa imagem baixada, podemos executar o container e validá-lo para ver se tudo estará certo. Para isso execute o comando abaixo.

```
docker run --rm --cap-add SYS_RAWIO --device /dev/mem hypriot/rpi-gpio
```

Basicamente com esse comando, ele irá compartilhar o acesso às gpio's da rpi3, depois irá iniciar o container e removerá logo após a execução.

Parece um workflow um pouco estranho, mas é assim que os containers trabalham quando se tem o objetivo de usá-los para ter acesso a comandos do dia a dia.

Veja  abaixo a saída do nosso terminal após executar o comando:

```
Usage: gpio -v
       gpio -h
       gpio [-g|-1] [-x extension:params] ...
       gpio [-p] <read/write/wb> ...
       gpio <read/write/aread/awritewb/pwm/clock/mode> ...
       gpio readall/reset
       gpio unexportall/exports
       gpio export/edge/unexport ...
       gpio wfi <pin> <mode>
       gpio drive <group> <value>
       gpio pwm-bal/pwm-ms 
       gpio pwmr <range> 
       gpio pwmc <divider> 
       gpio load spi/i2c
       gpio unload spi/i2c
       gpio i2cd/i2cdetect
       gpio usbp high/low
       gpio gbr <channel>
       gpio gbw <channel> <value>

```

Observe que aparentemente está tudo certo né? Validaremos o uso logo logo.

## Tornando a execução de forma transparente

Percebeu que seria chato toda vez executar esse comando gigantesco né? 

Por isso que iremos transformar esse comando em um alias, para que o mesmo seja invocado de forma transparente somente com um comando.

Para tornar isso possível iremos navegar até a pasta pessoal do usuário e inserir o alias em um arquivo especifico para que o novo comando seja reconhecido normalmente.

Caso não existir um arquivo chamado ***.bash_aliases*** na raiz do seu usuário, crie-o e adicione as seguintes linhas:

```
function io(){
  docker run --rm --cap-add SYS_RAWIO --device /dev/mem hypriot/rpi-gpio "$@"
}

alias io=io # voce pode escolher o nome do seu alias
```
... salve o arquivo e depois rode o comando abaixo para reconhecer o que foi inserido:

```
source .bashrc
```

Feito isso, você só precisa digitar em seu terminal o nome do alias que você escolheu - no meu caso - *io*

Funcionou? Sim?. Viu só que beleza.

**OBS** A ideia de rodar comandos de forma dockerizada eu aprendi devido ao [Ambientum](https://github.com/codecasts/ambientum) .

## Validando o uso

Vamos efetuar alguns testes?

Você tem disponíveis alguns comandos, vamos testar somente alguns para de fato ver se realmente funcionando.

Vamos manipular um simples led - o mesmo exemplo foi tratado no artigo mencionado na introdução.

* Definindo a gpio como output: **io mode 0 out**
* Ligando o led: **io write 0 1**
* Desligando o led: **io write 0 0**

## Finalizando

Curtiu o uso de comandos dockerizados através do Docker?

Eu particularmente acho muito útil esta abordagem, primeiro porque não polui a distro e segundo porque funciona muito bem.

Mais adiante pretendo abordar outros cenários assim, e possivelmente será com o broker mqtt Mosquitto.

## Referências

* [Imagem hypiot/rpi-gpio](https://hub.docker.com/r/hypriot/rpi-gpio/)
* [Let's get physical with Docker on the Raspberry Pi](https://blog.hypriot.com/post/lets-get-physical/)