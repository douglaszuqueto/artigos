## Introdução

Neste artigo abordaremos a configuração de uma Raspberry PI 3 e uma Raspberry PI Zero(W) sem precisar de um monitor e teclado. É isso mesmo :).

Muitas vezes se surge essa dúvida - em mim mesmo, quando iniciei neste mundo me deparei com o mesmo problema, pois não havia um teclado em casa, somente um monitor, então pouco iria adiantar. Então para começar acabei pegando um kit mouse/teclado emprestado da empresa rsrs. 

Nem todo mundo tem acesso facilmente a estes periféricos, e muitas pessoas já vierem perguntar para mim sobre isso - Douglas, tem como configurar sem precisar de um periférico adicional?. Vão surgindo dúvidas do tipo.

Então, depois de uma certa experiência e pesquisa atras destas respostas, deixo aqui registrado uma compilação de como será o **Fluxo** de uma configuração assim :D.

## Passos iniciais

Antes mesmo de começar, tenha em mãos seu cartão de memória com a imagem do Raspbian Lite já gravado. Caso ainda não tenha efetuado este passo, de uma olhada no artigo passado([link](https://goo.gl/Lp5b74)).

Com o cartão pronto, para ambas configurações - rpi 3, rpi zero w -, crie um **arquivo  vazio** dentro da pasta **boot** do cartão sd chamado **ssh**.

No meu caso, naveguei até a partição de **boot** do cartão ssd, e criei o arquivo usando o comando **touch**

```
[dzuqueto@localhost boot]$ cd /run/media/dzuqueto/boot/ # navegando ate a pasta boot
[dzuqueto@localhost boot]$ touch ssh # criando o arquivo vazio chamado ssh
[dzuqueto@localhost boot]$ 
```

... continua no tópico 1.

## 1 - Raspberry PI 3

... agora com o arquivo já criado, você pode desmontar as partições do cartão sd.

```
[dzuqueto@localhost ~]$ sudo umount /dev/mmcblk0p1 
[dzuqueto@localhost ~]$ sudo umount /dev/mmcblk0p2 
```
ou se preferir, pode desmontá-las via interface gráfica também.

Agora que o cartão está de fato preparado para a RPI 3, podemos conectar o cabo RJ45 à alguma porta (modem, roteador...) - no meu caso usei as portas do roteador. Como geralmente você tem um serviço DHCP Server rodando no roteador, isso fará com que, quando sua raspberry inicie, ela possa ganhar um endereço IP automaticamente.

Depois de ter efetuado a conexão de sua raspberry com o roteador, ligue-a e espere alguns instantes para que todos os processos de boot ocorram.

Passado alguns instantes, sua raspberry estará 'pronta para uso' e já deve ter assumido um endereço ip. Mas e agora Douglas - Qual endereço IP ela pegou?

É uma boa pergunta :P, mas fique calmo. Nesta etapa usaremos um utilitário muito famoso para quem mexe com redes - provavelmente para quem já mexa, irá conhecê-lo :).

O utilitário se chama **NMAP**, e é com eles que descobriremos o IP que a raspberry pegou. Então para que isso aconteça, você deverá instalá-lo em sua máquina. Em meu caso, como utilizo a Distro Fedora, instalei da seguinte forma:

```
sudo dnf install nmap
```

... mas se você usa alguma outra distro como Debian, Ubuntu, Mint ou demais baseados em Debian, provavelmente você conseguirá instalá-lo desta forma:

```
sudo apt-get install nmap
```

... basicamente o que mudou de uma distro para outra é o gerenciador de pacotes.

Para fazer o scan em sua rede, você deverá saber a faixa de sua rede - geralmente as redes vem configuradas no *192.168.0.0*, e no meu caso não é diferente. Portanto faça um scan com o comando abaixo:

```
sudo nmap -sP 192.168.0.0/24
```

Ou seja, com o comando acima, estarei verificando todos os hosts de minha rede. O meu retorno saiu como demonstra abaixo:

```
[dzuqueto@localhost ~]$  nmap -sP 192.168.0.0/24

Starting Nmap 7.40 ( https://nmap.org ) at 2017-04-04 19:03 -03

Nmap scan report for dlink.lan (192.168.0.1)
Host is up (0.0013s latency).
Nmap scan report for fedora.lan (192.168.0.100)
Host is up (0.0012s latency).
Nmap scan report for 192.168.0.101
Host is up (0.0033s latency).

Nmap done: 256 IP addresses (4 hosts up) scanned in 3.12 seconds

```

Veja que apareceu alguns hosts. Este resultado irá variar de rede para rede. Veja que no meu caso apareceu N hosts. E como no momento do teste, tenho apenas meu roteador, meu notebook e uma raspberry conectado, logo consigo identificar tranquilamente qual é o IP atribuído a raspberry: **192.168.0.101**.

Outra forma realizar a verificação do IP, é através da interface gráfica do seu Roteador(ou outro dispositivo), pois como ele fornece o serviço de DHCP, provavelmente terá alguma informação referente a listagem de dispositivos conectados em sua rede.

Por enquanto, neste tópico é isso. Encerramos com o IP em 'mãos'. Neste momento, você já pode pular para o tópico 3, onde será abordado os passos finais.

## 2 - Raspberry PI Zero W

Na Raspberry PI Zero(W), sabemos claramente que não possuímos uma interface de rede cabeada, logo, teremos que seguir num caminho um pouco diferente para chegar no mesmo resultado.

Pesquisando na internet, me deparei com o uso do tal **OTG**. Usando esta tecnologia, poderíamos conectar a rpi zero diretamente em uma porta **USB** do computador e por assim, ter uma espécie de rede entre rpi <-> computador.

Confira este vídeo antes de proceder para ficar mais claro o que será feito aqui. [Link do vídeo](https://goo.gl/x0lzZH).

Então para de fato ativar esta funcionalidade, teremos que editar os arquivos **config.txt** e o **cmdline.txt**.

Portanto abra-os e **adicione** as seguintes linhas no **final dos arquivos referentes**:

### Arquivo config.txt
```
dtoverlay=dwc2
```

### Arquivo cmdline.txt
```
modules-load=dwc2,g_ether
```

Depois de adicionado as referidas linhas, você pode desmontar as partições, e colocar o cartão sd em sua raspberry.

Agora, com tudo conectado, será utilizado um cabo normal(desses de carregador de celular) para conectar a raspberry pi zero em uma das portas usb do computador. 

**Obs:** Você deve conectar conectar o cabo usb na porta usb referente da rpi zero e não da porta de alimentação :);

Diante do mesmo procedimento que ocorreu na rpi 3, espere alguns instantes :).

Depois da raspberry estar pronta, eis que surge uma conexão entre seu computador e a raspberry pi - ainda não há comunicação. Iremos configurar isto no próximo passo.

No Fedora, eu tive que efetuar os seguintes passos:

* Abrir a conexão que foi criada;
* Logo depois abrir as referidas configurações;
* Selecionar a guia IPV4;
* Na opção de Endereços, selecionei a opção: ** Apenas conexão local **

Veja as imagens abaixo:

### Interfaces de rede
![img](https://douglaszuqueto.com/uploads/articles/artigo-27/interfaces-de-rede.png)

### Configurações da interface
![img](https://douglaszuqueto.com/uploads/articles/artigo-27/configuracao-da-interface.png)

### Configuração final
![img](https://douglaszuqueto.com/uploads/articles/artigo-27/configuracao-final.png)

Se você quiser, poderá optar por um processo manual através da linha de comando - fazendo o uso do utilitário **ifconfig**. Veja os comandos abaixo:


```
[dzuqueto@localhost ~]$ sudo ifconfig enp0s29u1u3 down
[dzuqueto@localhost ~]$ sudo ifconfig enp0s29u1u3 169.254.100.100 netmask 255.255.0.0 broadcast 169.254.255.255 mtu 1500
[dzuqueto@localhost ~]$ sudo ifconfig enp0s29u1u3 up
```

**OBS¹:** Este processo manual creio que seja mais recomendado caso sua distro seja diferente da minha :).

**OBS²:** ***enp0s29u1u3*** se trata do nome que minha interface recebeu. Caso você não saiba, rode o comando **ifconfig** e veja qual o nome dado a interface criada.

Basicamente foi isso que ocorreu quando configuramos via interface gráfica, mas sempre é bom relembrar dos comandos :P.

Com este workflow funcionando, agora você tem acesso ao host **raspberrypi.local**, é isso mesmo. Será através dele que conseguiremos efetuar uma conexão via ssh.

Se não está acreditando, é só rodar um ping apontando para o host e verás a 'mágica'.

```
[dzuqueto@localhost ~]$ ping raspberrypi.local 
PING raspberrypi.local (169.254.155.130) 56(84) bytes of data.
64 bytes from raspberrypi.local (169.254.155.130): icmp_seq=1 ttl=64 time=0.341 ms
64 bytes from raspberrypi.local (169.254.155.130): icmp_seq=2 ttl=64 time=0.320 ms
64 bytes from raspberrypi.local (169.254.155.130): icmp_seq=3 ttl=64 time=0.482 ms
64 bytes from raspberrypi.local (169.254.155.130): icmp_seq=4 ttl=64 time=0.368 ms
64 bytes from raspberrypi.local (169.254.155.130): icmp_seq=5 ttl=64 time=0.361 ms
```

Com o host **raspberrypi.local** em mãos,  já podemos ir para o tópico default, que neste caso se trata do tópico 3.

## 3 - Passos em comum - finalização

Já que temos o **IP** - no caso da RPI 3 ou o **Host** do RPI Zero, podemos realizar o acesso SSH a partir de um cliente. Neste caso usarei o cliente default que o terminal me entrega mesmo. Então com o comando **ssh pi@192.168.0.101** eu irei acessar a RPI 3 e com o comando **ssh pi@raspberrypi.local** irei acessar a PI Zero - simples assim.

Neste passo o que pode ocorrer é dar conflito com seu **know_hosts**. Este é um arquivo onde fica guardado todas suas conexões efetuadas até o momento. Então caso o endereço ip ou o host tenha alguma colisão e a chave seja diferente, você terá problemas no ato da conexão e acabará não conseguindo se conectar.

Para resolver o problema, basta abrir o arquivo, que nas distros linux até onde eu sei fica em uma pasta oculta do usuário. Então se você estiver na pasta raiz do usuário basta entrar no seguinte diretório: ** cd .ssh**. Somente isso. Lá você deverá ver o arquivo citado. Abrindo-o, você deverá procurar pelo ip ou pelo host referente e remover aquela linha. Depois basta tentar conectar novamente que tudo dará certo :).

## Finalizando

Então para finalizar o artigo, depois que você tenha efetuado as configurações iniciais, depois ter escolhido uma das placas, efetuado as configurações, e indo para o passo de finalização, você já estará apto a efetuar as configurações já citadas no artigo anterior, como configuração da rede wifi, configuração de locale, timezone, instalação de pacotes e etc.

**OBS:** Raspberry PI Zero(W) - Não esqueça de remover as flags adicionadas no tópico 2 - config.txt e cmdline.txt. Recomendo fazer isso depois que estiver configurado uma conexão WiFi.

Bem pessoal, por hoje é isso, espero que este post tenha/irá ajudar vocês assim como me salvará muitas vezes :).

Um abraço e até logo.

## Referências

* [15 Useful “ifconfig” Commands to Configure Network Interface in Linux](http://www.tecmint.com/ifconfig-command-examples/);
* [Raspberry Pi Zero USB/Ethernet Gadget Tutorial](https://goo.gl/x0lzZH);
* [Monitor logo](http://freevector.co/wp-content/uploads/2011/03/73711-tv-monitor.png);
* [Keyboard logo](http://www.free-icons-download.net/images/keyboard-icon-63746.png);
* [Stop logo](http://www.freeiconspng.com/uploads/stop-sign-png-26.png);