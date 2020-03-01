## Introdução

Depois de algum tempo sem artigos, estamos voltando a ativa :). E para este artigo, nada melhor do que tratar de uma nova plaquinha :P. Portanto neste artigo será tratado do novo modelo da Raspberry - A Raspberry PI Zero W.

Será abordado suas especificações, pinagem e já partiremos para uma configuração de primeiro momento até realizar um blink com ela.

Caso ainda não saiba nada a respeito da placa, do seu lançamento e alguns outros detalhes fica uma rápida referência abaixo:

* [Novo Raspberry Pi Zero W com wifi e bluetooth - FilipeFlop](https://goo.gl/KWUnZh);

Se quiser detalhes sobre a compra da raspberry pi zero w, fiz um pequeno post na minha página relatando sobre, segue o [link](https://goo.gl/dYUXNv) caso seja de seu interesse.

## Especificações

Abaixo segue as especificações da plaquinha:

* 1GHz, single-core CPU;
* 512MB RAM;
* Mini HDMI and USB On-The-Go ports;
* Micro USB power;
* HAT-compatible 40-pin header;
* Composite video and reset headers;
* CSI camera connector;
* 802.11 b/g/n wireless LAN;
* Bluetooth 4.1;
* Bluetooth Low Energy (BLE);

**OBS:** Especificações retiradas do site oficial, para maiores detalhes acesse o [link](https://goo.gl/6cKzqy).

## Materiais necessários

* Raspberry PI Zero W;
* Fonte de alimentação 5V 2A;
* barra de headers;
* Cartão de memória 16GB Classe 10;
* Adaptador OTG;
* Adaptador mini-hdmi - hdmi;
* Teclado usb/sem fio;
* Monitor;

## Pinout

Para detalhes sobre a referência dos pinos, segue a imagem abaixo retirada da Element14.

![img](https://douglaszuqueto.com/uploads/articles/artigo-26/pinout-raspberrypi-zero-w.png)

## Primeiros passos
Para darmos os primeiros passos, iremos percorrer certo caminho até chegarmos no resultado final.

Para quem acompanha os artigos aqui publicados, já foi tratado da Raspberry PI 3, lá no artigo foi utilizado Windows para gravação da imagem no SD Card como também foi utilizado o Sistema Operacional Raspbian(versão 2016). Ressaltei também queria iria testar a outra distro - Raspbian Lite bem como efetuar a gravação da imagem a partir de uma Distro Linux.

A distro Raspbian Lite é uma imagem relativamente leve em relação ao Raspbian With Pixel. Além de não possuir interface gráfica, um monte de pacotes pré-instalados como jogos, programas e afins não vem instalado, pois não faz sentido já que não se faz necessário a utlização da interface gráfica.

Caso não tenha efetuado a leitura do artigo, fica [aqui o link](https://goo.gl/B79UO2). Deixo aqui ressaltado, se você quiser usar o Windows para gravação, não tem problema algum, o processo será o mesmo :).

### Gravando a imagem

Antes de efetuar a gravação da imagem, precisamos dela, correto?. Para isso acesso o [site](https://goo.gl/YsQmbn). Você terá duas opções: Baixar a diretamente ou efetuar o download via torrent.

![img](https://douglaszuqueto.com/uploads/articles/artigo-26/rasbpian-lite-download.png)

Com a imagem já baixada e descompactada(a imagem possui um tamanho de 1,4GB), já podemos efetuar a gravação da mesma no nosso cartão de memória. Nas minhas raspberry's, eu venho utilizando cartões de 16GB classe 10. Você não é obrigado usar um com este tamanho de armazenamento tão pouco ser classe 10, fica a livre escolha de cada um de vocês.

Para começar, insira seu cartão de memória(geralmente na compra do cartão micro sd ele já vem com adaptador, então caso não o tenha, você precisará de um). Logo em seguida rode os comandos abaixo em seu terminal.

#### Comando df -h

```
[dzuqueto@localhost ~]$ sudo df -h
Sist. Arq.                 Tam. Usado Disp. Uso% Montado em
devtmpfs                   3,9G     0  3,9G   0% /dev
tmpfs                      3,9G   22M  3,9G   1% /dev/shm
tmpfs                      3,9G  1,7M  3,9G   1% /run
tmpfs                      3,9G     0  3,9G   0% /sys/fs/cgroup
/dev/mapper/fedora-root     79G   11G   64G  15% /
tmpfs                      3,9G  336K  3,9G   1% /tmp
/dev/sda1                  477M  181M  268M  41% /boot
/dev/mapper/fedora00-home  123G   11G  107G   9% /home
tmpfs                      788M   24K  788M   1% /run/user/42
tmpfs                      788M  6,3M  781M   1% /run/user/1000
/dev/mmcblk0                15G  8,0K   15G   1% /run/media/dzuqueto/50D0-D721
```

Com o comando **sudo df -h** você verá todas partições que estão em seu sistema. Observe a última linha: **/dev/mmcblk0**, este carinha ai se refere ao cartão de memória :). Bem simples, este utilitário apenas nos ajudará saber qual *'endereço'* do nosso cartão.

#### Comando umount

Agora iremos rodar outro comando para **desmontar a partição**. Para isso usaremos o utilitário ***umount*** , o qual você passará o endereço de seu cartão para que de fato a partição possa ser desmontada. Portanto execute o seguinte comando: **sudo umount /dev/mmcblk0**. Se não der nenhum erro, sua partição estará desmontada :). Caso queira validar, é só executar novamente o comando abordado no tópico acima - seu cartão não deverá mais aparecer na lista.

#### Comando dd

Com tudo pronto, agora de fato poderemos gravar a imagem do raspbian no cartão. Para isso utilizaremos o famoso utilitário ***dd***. Portanto para realizar a gravação você deverá usar o seguinte comando: **sudo dd bs=4M status=progress if=/home/dzuqueto/Downloads/2017-03-02-raspbian-jessie-lite.img of=/dev/mmcblk0**

O processo demorará alguns minutos, você terá um feedback do status da gravação devido à flag passada *status=progress*. Veja o feedback abaixo:

```
[dzuqueto@localhost ~]$ sudo dd bs=4M status=progress if=/home/dzuqueto/Downloads/2017-03-02-raspbian-jessie-lite.img of=/dev/mmcblk0
1388314624 bytes (1,4 GB, 1,3 GiB) copied, 57,094 s, 24,3 MB/s   
```

Lembre-se que o tempo que demorará para de fato efetuar a  gravação dependerá de inúmeros fatores - principalmente da Classe se seu cartão.

Depois de 2 minutos(no meu caso) de espera, a gravação está completa, você deverá ver um feedback como o da imagem abaixo:

```
332+1 registros de entrada
332+1 registros de saída
1393557504 bytes (1,4 GB, 1,3 GiB) copied, 133,031 s, 10,5 MB/s
```

Agora você deverá remover o cartão de memória e inseri-lo novamente. Este processo não será apenas manual, na sua inserção duas partições serão criadas, a partição **/boot** e a partição do **Sistema Operacional**(está partição ficará somente como leitura, não se assuste, é assim mesmo :) ).

### Configurações do boot

Como possuo um monitor VGA, eu necessito utilizar o conversor HDMI -> VGA. Então para tudo ocorrer bem, é necessário realizar algumas configurações no arquivo **config.txt** que se encontra dentro da partição *boot*.

Então com base nas configurações para fazer o monitor VGA de fato funcionar e de acordo com as características do meu monitor(resolução), você deverá encontrar as seguintes linhas no arquivo original.

```
# uncomment if hdmi display is not detected and composite is being output
#hdmi_force_hotplug=1

# uncomment to force a specific HDMI mode (this will force VGA)
#hdmi_group=1
#hdmi_mode=1

# uncomment to force a HDMI mode rather than DVI. This can make audio work in
# DMT (computer monitor) modes
#hdmi_drive=2
```

... portanto, com base nas configurações necessárias para meu monitor, as linhas referentes ficaram assim:

```
# uncomment if hdmi display is not detected and composite is being output
hdmi_force_hotplug=1

# uncomment to force a specific HDMI mode (this will force VGA)
hdmi_group=2
hdmi_mode=47

# uncomment to force a HDMI mode rather than DVI. This can make audio work in
# DMT (computer monitor) modes
hdmi_drive=2
```

Como relatado, as configurações são adequadas para meu monitor, seu caso pode ser diferente, você pode ter um monitor hdmi, ou um monitor vga mais 'atual'. 

Fazendo a modificação, apenas salve o arquivo e feche-o. Logo em seguida você pode desmontar as partições a partir de sua interface gráfica ou até mesmo ver o novo nome das partições com o comando acima relatado(df -h) e em seguida usar o *umount* para desmontar as partições. No meu caso removi pela interface gráfica mesmo :).

![img](https://douglaszuqueto.com/uploads/articles/artigo-26/print-cartao-sd.png)

## Preparando o ambiente

Como relatei no decorrer do artigo, usaremos monitor e teclado(no próximo artigo eu tratarei de como proceder com uma instalação sem necessidade de monitor muito menos do teclado).

Então prepare seu ambiente ai em sua casa. O meu ficou como demonstra na imagem abaixo:

![img](https://douglaszuqueto.com/uploads/articles/artigo-26/montagem.jpg)


## Primeiras Configurações

Efetuando a alimentação da placa, você deverá ver alguns processos 'correndo' em seu monitor até preparar por completo o sd card para depois obtermos acesso ao terminal.

Neste processo de primeiras configurações, efetuaremos alguns processos, alguns já abordado no primeiro artigo com a raspberry pi 3.

**Cronograma**

* 1 - Configurações de idiomas/timezone em geral;
* 2 - Ativação do SSH;
* 3 - Expansão do sistema de arquivos;
* 4 - Configuração do layout do teclado;
* 5 - Configuração da rede wifi;
* 6 - Atualização dos repositórios e pacotes;
* 7 - Instalação básica de pacotes;

**OBS:** Os itens 1, 2 e 3 serão realizados a partir do próprio utilitário do Raspbian - **raspi-config**

### 1,2,3 - Idioma/timezone, ssh, expansão do sistema de arquivos

Para realizar a configuração iremos abrir o raspi-config. Para isso, basta executar o seguinte comando: **sudo raspi-config**.

Uma tela azul(não - não é a tela de erro do Windows) deverá abrir neste momento. Para ajudar,  as opções são todas numeradas, portanto iremos se basear por elas para realizar a configuração.

#### 1 - Configurações de idiomas/timezone em geral

Para configurar o item 1 você deverá navegar até a opção de número ***4***. Você terá 4 opções.

Primeiramente entraremos na opção 1 para escolher o **locale**. Navegue e selecione a opção **pt_BR.UTF-8 UTF-8** e em seguida escolheremos **pt_BR.UTF-8**. O processo durará alguns segundos.

De volta ao menu principal, entre na opção 4 novamente, e vamos fazer a seleção da **Timezone**. Você deverá selecionar America -> Sao_Paulo(ou conforme sua timezone) em seguida, confirme e dê um ok.

#### 2 - Ativação do SSH

Na menu principal, navegue até a opção **5** e depois até a opção **P2 SSH**. Selecione e ative a Conexão SSH.

#### 3 - Expansão do sistema de arquivos

Opção **7**, depois escolha a primeira opção **A1**

### 4 - Configuração do layout do teclado

Nesta etapa, realizaremos um processo manual para configuração do nosso teclado funcionar com layout **ABNT2**.

Para isso, navegaremos até a pasta **/etc/default** e abriremos o arquivo **keyboard** através do editor de texto *nano*. No terminal digite: **sudo nano keyboard**.

**OBS:** Quando você tentar digitar uma **/** provavelmente irá sair outro caractere, pois o teclado ainda não está configurado. Então para de fato digitar a /, você deverá apertar a tecla de **Dois pontos / ponto e vírgula**. 

Você deverá procurar pela opção **XKBMODEL** e **XKBLAYOUT**, ambas você deverá substituir o valor default por **abnt2** e **br**, respectivamente. Salve o arquivo usando ***ctrl+o*** e depois feche-o utilizando ***ctrl+x***.

Agora para aplicar as configurações sem precisarmos reiniciar a plaquinha, execute o seguinte comando: **sudo service keyboard-setup restart**. Esse simples comando recarregará as configuraçoes, portanto neste momento, teste teclando o **ç** se funcionar, ocorreu tudo bem :).

### 5 - Configuração da rede wifi

A configuração da rede wifi, abordamos no outro artigo, apenas colocarei aqui os passos, sem mais delonga :).

Passo 1: **sudo nano /etc/wpa_supplicant/wpa_supplicant.conf **
Passo 2: escreve as linhas abaixo

```

ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1
country=BR

network={
        ssid="Douglaszuqueto"
        psk="SUA_SENHA"
}
```

Passo 3: **sudo ifdown wlan0**
Passo 4: **sudo ifup wlan0**

A partir deste momento, você deverá estar conectado a sua rede wifi. Para ver se está tudo certo(conectividade e acesso a internet) digite o comando **ping google.com**, se estiver pingando, voilá, está conectado :).

### 6 - Atualização dos repositórios e pacotes

Como já estamos na nossa rede wifi, também já possuímos acesso a internet, certo?.

Portanto para proceder com a atualização dos repositórios, execute o seguinte comando: **sudo apt-get update**.

Com os repositórios atualizados, você já pode verificar se possui atualizações dos pacotes e instala-los com o comando: **sudo apt-get upgrade**.

Como a distro é bem atual, todo o processo acima demorou menos de 6 minutos :).

**OBS:** na utilização de ambos comandos, dependerá de sua velocidade de conexão com a internet, se demorar não se preocupe hehe.

### 7 - Instalação básica de pacotes

Neste passo iremos instalar algumas ferramentas que nos ajudarão nos próximos passos. Portanto, será instalada as seguintes ferramentas:

* vim
* htop
* pip(python)
* git

Para instalarmos, execute o comando: **sudo apt-get install vim htop python-pip git**.

O processo completo, demorou em torno de 5 minutos. Vamos dar uma folga e reinicie sua rasp: **sudo reboot**.

## Blink

Para realizarmos o blink, utilizaremos a base do artigo **[Hello World com GPIO na RPI3](https://goo.gl/B79UO2)**. Será usado o mesmo circuito e mesmo código :). Portanto, basta acessar o artigo e seguir com os mesmo passos :).

![img](https://douglaszuqueto.com/uploads/articles/artigo-26/pi-zero-blink.jpg)

## Acessando sua placa via SSH

A partir de agora, depois de tudo configurado, podemos acessar a raspberry pi sem necessidade dos periféricos adicionais(monitor e teclado). Utilizamos a fim de uma configuração inicial, portanto daqui a pouquinho você já pode 'descartá-los'.

Antes de efetuar o descarte propriamente dito, vamos primeiro executar apenas um comando para verificar qual o **IP** atribuído à sua Raspberry PI. Execute o seguinte comando: **ifconfig wlan0**.

Terá um pocado de informações, mas procure pelo seguinte termo: **inet end.**, logo após estará seu endereço IP :), no meu caso, o ip atribuído é **192.168.0.125**.

Agora em seu computador, já temos o necessário para efetuar o acesso ssh. Basta executar apenas o comando referente: **ssh pi@192.168.0.125**, nesta primeira conexão será exibida uma mensagem, apenas confirme. Logo após, será pedido para digitar a senha do usuario referente, e como não mudamos a senha default, ela é **raspberry**. 

Será mostrada a seguinte tela caso tudo tenha dado certo:

```
The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Sat Apr  1 23:21:43 2017
```
... ou seja, tudo deu certo, agora você não precisa mais utilizar teclado e monitor :). Portanto neste momento, você não necessita mais dos periféricos.

### Configurações adicionais

No passo acima começamos a utilizar ssh para acesssar a raspberry remotamente. O modo de autenticação como vimos é via **password**. Concorda que este fluxo é um pouco chato né? Cada vez que formos querer acessar a placa teremos que ficar sempre digitando a senha, sem falar que este método não soa tão seguro assim.

Para resolver este problema e também tornar o fluxo melhor, através do ssh, pode-se utilizar chaves criptográficas - chaves públicas e privadas.

Portanto teremos que gerar um **key pair** em nosso computador e depois efetuar uma cópia da chave pública para a raspberry. Para gerar o par de chaves em seu computador execute o seguinte comando: **ssh-keygen**. Será perguntado algumas coisas, você pode simplesmente concordar com tudo até finalizar.

Com o par de chaves geradas, agora você pode efetuar uma cópia da chave pública para a raspberry. Para efetuar o procedimento utlize o seguinte comando: **ssh-copy-id pi@IP_DA_RASPBERRY**. Será perguntada a senha e logo depois tudo estará certo.

A partir de agora, seu embarcado já estará apto para aceitar conexões advindas de seu computador sem a necessidade de digitar a senha. Animal não é?.

## Consumo de recursos

Abaixo, a fim de sanar a curisidade de alguns, fica um print da saída do htop, onde mostra os processos correndo, consumo de processamento, memória ram, swap dentre outras informações.

![img](https://douglaszuqueto.com/uploads/articles/artigo-26/htop.png)

## Finalizando

Neste primeiro contato, estou muito impressionado com o novo modelo. Havia algum tempo que queria realizar a compra da PI Zero, mas logo depois vieram com o lançamento desta versão - então não deu outra: Compra efetuada.

Como comentei com o pessoal nos grupos, fiquei impressionadíssimo com o tamanho dessa coisinha. Veja a imagem abaixo com um comparativo entre a PI 3, Linkit 7688 Duo e NodeMCU:

![img](https://douglaszuqueto.com/uploads/articles/artigo-26/comparativo.jpg)

Animal não é? É realmente impressionante, sem mais.


Bem, por hoje é isso pessoal. Espero que vocês tenham gostado dessa leitura assim como eu gostei de escrever este artigo :). Um abraço e não esqueça: Gostou? Compartilhe com seus amigos e siga minha página no [Facebook](https://goo.gl/qgivIv) :)

## Referências

* [Raspberry PI](https://goo.gl/13q5jc);
* [Element14](https://goo.gl/iLwyKf);

