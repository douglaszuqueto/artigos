Neste artigo será tratado dos primeiros passos com a fantástica Raspberry PI 3 no mundo da Internet das Coisas.

Havia algum tempo que estava interessado em uma rasp3 por conta dos projetos de IoT. Queria descobrir o quanto ela seria poderosa no uso da mesma no ecossistema de internet das coisas, rodando pequenas aplicações nas mais variadas linguagens e cenários ou até como um IoT Gateway. 

Um dos outros motivos que aumentou o interesses foi quando comecei a conhecer o ecossistema Docker.  No decorrer que foi sendo estudado e aplicado nos projetos, me veio as seguintes curiosidades: 
* **Como será que o Docker se comportaria numa Rasbperry?**
* **Como seria montar um Cluster Swarm com ela?**

Devido os fatos e também a vontade de conhecer um embarcado desta categoria, *'infelizmente'* no Black Friday o Kit do FilipeFlop entrou em promoção, então não precisa descrever o resultado né(rsrs)?

## Composição do Kit

* 1 Raspberry PI 3
* 1 Fonte de alimentação 5V 2A
* 1 Case de acrílico
* 1 Cartão de memória micro sd 8GB classe 4 com adaptador
* 1 cabo hdmi

[Link do Kit comprado](http://www.filipeflop.com/pd-2b3658-kit-raspberry-pi-start.html)

## Preparação do cartão sd

O cartão que veio no kit é um sandisk 8gb classe 4 - comprarei um com o armazenamento maior e também classe 10 para obter uma maior performance nos projetos.

Para preparar o cartão de memória foi efetuado o download da imagem da distro Raspbian na versão 'Raspbian Jessie With Pixel' e para gravar a imagem no cartão foi utilizado o artigo oficial com os procedimentos de instação a partir do Windows usando o programa Win32DiskImager. [Link do artigo](https://www.raspberrypi.org/documentation/installation/installing-images/windows.md)

**Obs:** Será testado também com alguma distro linux, assim que validar o uso, este tópico será editado.

## Inicialização da Rasp e primeiras configurações

A primeira inicialização da rasp foi relativamente rápida :p, gostei mesmo da velocidade de inicialização do sistema. 

Neste primeiro contato foi configurado a rede - acesso via cabo e  wifi, e também foi efetuada a ativação do acesso SSH para que posteriormente eu não precise de monitor, mouse/teclado e sim de uma simples conexão ssh.

### Ativação do SSH

Para ativar a conexão com SSH, use o utilitário que o Raspbian oferece para rápidas configurações. O utilitário se chama 'Raspberry Pi Configuration', abrindo o utilitário, vá na aba interfaces habilite o serviço do SSH. Veja os detalhes na imagem abaixo.

![img](https://douglaszuqueto.com/uploads/articles/artigo-11/rpi3_ssh.PNG)

### Timezone, locales e configurações do teclado

No mesmo assistente do passo anterior, no último menu(Localisation), você terá opções para ajustar a data/hora do sistema(opção timezone), configurações de idiomas( opção locale) e também para atribuir o padrão brasileiro ao seu teclado(opção keyboard). Feito esses dois primeiros passos, clique em 'Ok'.

Minhas configurações ficaram assim:

* Locale : PT - Brasil
* Timezone: America - Sao_Paulo
* Keyboard: Portuguese - Brasil

**Obs** Será pedido para reiniciar a rasp neste momento.

![img](https://douglaszuqueto.com/uploads/articles/artigo-11/rpi3_timezone_locales_keyboard.PNG)

### Atualização da senha do usuário

Este passo é obrigatório pois a conexão SSH irá funcionar somente se a senha default do Raspbian for modificada. Portanto, para atualizar a senha, basta executar o comando abaixo e entrar com a nova senha.

```sh
sudo passwd pi
```

### Configuração da rede lan

Abra as configurações de rede, e escolha a interface eth0 e logo depois define um endereço IP fixo e salve as configurações.

Este passo será feito, para que possa ser feito uma conexão lan entre meu computador e a raspberry pi. Assim caso precise ter acesso, consigo facilmente conecta-los e assim ter acesso ao SSH. Veja os detalhes na imagem abaixo.

![img](https://douglaszuqueto.com/uploads/articles/artigo-11/rpi3_lan.PNG)

### Configuração da rede wlan

As configurações da rede wifi serão um pouco diferentes da rede lan. No raspbian se tem um arquivo de configuração onde é armazenado as configurações da rede wlan e as redes que a interface já se conectou.

Neste caso, eu atribui 2 redes, a rede wifi de casa e por vias de dar algum problema nesta rede, também adicionei a rede do hotspot de meu celular. Uma segurança a mais, já que meu único acesso a rasp será por meio da rede lan ou wlan através de uma conexão SSH.

1º Abre o seguinte arquivo de configuração com algum editor como nano ou vi.

```sh
sudo nano /etc/wpa_supplicant/wpa_supplicant.conf 
```

2º Observe as diretivas da configuração no arquivo aberto. O arquivo ficará parecido como mostra o código abaixo, 

```sh
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1
country=GB

network={
        ssid="douglas"
        psk="douglas"
        key_mgmt=WPA-PSK
}

network={
        ssid="hotspot"
        psk="hotspot"
        key_mgmt=WPA-PSK
}
```

Caso queira ter mais redes cadastradas, somente precisará duplicar a diretiva *network*  e configurar de acordo com suas credenciais. 
Feito a configuração, é só salvar e fechar o arquivo.

3º Reiniciando o adaptador de rede

```sh
sudo ifdown wlan0 && sudo ifup wlan0
```

## Configurações Gerais

A partir desses passos, você não necessitará mais ter um monitor, teclado e mouse, precisa somente de uma simples conexão SSH.

### Atualização dos repositórios e dos pacotes

Nada melhor que neste momento, atualizar os repositórios e os pacotes instalados no sistema, concorda? Para atualizar, execute o simples comando abaixo.

```sh
sudo apt-get update && sudo apt-get upgrade
```

Se tiver algo para ser atualizado, confirme para executar a atualização dos pacotes :). 

**Obs 1:** Velocidade do download das atualizações dos pacotes dependerá de sua conexão com a internet;

** Obs 2:** Durante a atualização propriamente dita a velocidade de instação dependerá da classe do seu cartão de memória.

### Instalação de pacotes básicos

Neste momento será instalado somente 2 pacotes. Vim - para edição via linha de comando e Htop - interface para acompanhamento dos processos e uso dos recursos do SO.

```sh
sudo apt-get install vim htop
```

### Remoção de pacotes desnecessários

Nesta etapa será desinstalado alguns pacotes desnecessários ou que estão desatualizados, como no caso do NodeJS, para isso execute o comando abaixo.

```sh
sudo apt-get autoremove nodejs
```

### Desabilitação da GUI do sistema

Alguém pode estar se perguntando o porque desta etapa, como meu foco é teste, iot e desenvolvimento não teria o porque manter ativado a interface gráfica do sistema. Até poderia manter ativada, a grosso modo não fazeria diferença já que meu acesso é somente ssh, porém desabilitando eu tenho uma mera economia de memória ram, que futuramente pode fazer alguma diferença :p.

**Passos:**

* 1º Execute o seguinte comando: sudo raspi-config
* 2º Navegue até a opção 3 (Boot Options) e de um Enter
* 3º Escolha a opção B1 (Desktop / CLI)
* 4º Novamente escolha a opção B1 (Console) e dê um Enter
* 5º Você será redirecionado para a primeira tela, navegue até a opção 'Finish' e finalize o processo.
* 6º Será solicitado uma reinicialização do sistema, portanto basta reiniciar.

## Finalizando

Com um ambiente 'finalizado', o próximo passo será instalar o Docker e também o Docker Compose para assim começar a testar as imagens do Docker Hub disponíveis para a arquitetura ARM e também criar as imagens para o uso dos serviços do projeto IoTBr.

Por hora - mesmo sendo um cartão classe 4 - se mostrou um bom desempenho para os rápidos testes que fiz, vamos ver como ficará com um classe 10. Estou bem impressionado com a placa, as expectativas estão altíssimas para os testes de serviços iot e também de saber como a rasp se comportaria em um cenário onde ela fosse um IoT Gateway local.

Finalizando veja a imagem abaixo com o htop aberto, observe que o consumo de ram está relativamente baixo(34mb) com a interface gráfica desabilitada - antes de desativar, o consumo estava em torno de 100mb.

![img](https://douglaszuqueto.com/uploads/articles/artigo-11/htop.png)

## Artigos recomendados

* [Raspberry Pi 3: Primeiras Impressões - FilipeFlop](http://blog.filipeflop.com/embarcados/raspberry-pi-3-primeiras-impressoes.html)
* [Primeiros passos com Linux na Raspberry Pi 3 - Embarcados](https://www.embarcados.com.br/primeiros-passos-linux-na-raspberry-pi-3/)