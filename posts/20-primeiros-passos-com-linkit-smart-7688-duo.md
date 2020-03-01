## Introdução

E ai galera, tudo beleza? 

Creio que vocês já devem estar enjoando dos artigos abordando somente a raspberry pi, pois então, ano passado(2016), havia comprado a Linkit no blackfriday. Ela estava parada até o momento pois o foco estava concentrado na exploração da Raspberry PI(essa saga ainda vai longe :P), então para mudar um pouco o ritmo, relatarei os primeiros passos com esta fantástica plaquinha.

Para começar, recomendo fortemente a leitura do Artigo "[Linkit Smart 7688 Duo – Uma placa 2 em 1](https://goo.gl/AYYSBq)" que o [André Curvello](https://www.facebook.com/andre.ml.curvello) escreveu para o [Blog do Filipe](http://blog.filipeflop.com). No artigo ele aborda a introdução sobre a placa, apresentando suas características, seu hardware, utilização e etc.

## Materiais necessários

Para começar, você irá precisar apenas da Linkit e de um cabo usb(aqueles utilizados em celular), segue a listagem abaixo:

* [LinkIt Smart 7688 Duo](https://goo.gl/SZfSl4);
* Cabo usb;

## Primeira conexão

Para começar, conecte sua placa em uma porta usb. O boot completo durará em torno de 30 segundos, logo após isso, você deverá observar que um Ponto de Acesso está disponível em sua lista de redes WiFi. O nome da rede wifi da minha placa tem o nome de ***LinkIt_Smart_7688_1B89ED***, logo você também terá um nome parecido ou igual a esse.

Conectando-se na rede wifi, você deverá abrir em seu browser o ip de sua linkit, no meu caso o ip dela é **192.168.100.1**. Acessando o ip, será carregador a página de login e você será solicitado para definir uma senha para o usuário **root**(usuário default do sistema). Veja na imagem abaixo a tela referente:

![img](https://douglaszuqueto.com/uploads/articles/artigo-25/linkit-login.png)

Logo após realizar a definição da senha e efetuar o login na tela, você será redirecionado pra tela principal, onde será apresentado algumas informações referentes ao sistema. Consta informações como Informação da plataforma, informações da conta, informações de software e também uma opção para resetar a placa.

Veja qual a versão do firmware que está instalado em sua placa, até o momento da escrita deste artigo, a última versão que consta no site, é a versão **v0.9.4**. Caso essa não seja sua versão, no próximo tópico será abordado como realizar a atualização do firmware.

## Atualizando o firmware

Para efetuar a atualização do firmware, você deverá ir até o site e baixar a última versão do firmware, fica [aqui o link](https://docs.labs.mediatek.com/resource/linkit-smart-7688/en/downloads) para acesso.

Efetuando o download, você deverá descompactar o arquivo baixado e depois voltar para a interface, voltando você deverá clicar no botão **Upgrade Firmware**.

![img](https://douglaszuqueto.com/uploads/articles/artigo-25/linkit-upgrade.png)

Uma opção irá se abrir para você escolher o firmware baixado, escolha-o e depois clique em upgrade, uma pequena modal irá se abrir dizendo a seguinte mensagem: "Upload Firmware - Uploading...", logo após o processo ser concluído, a mensagem da imagem abaixo aparecerá para você:

![img](https://douglaszuqueto.com/uploads/articles/artigo-25/linkit-upload.png)

Espere de acordo com as instruções dadas acima.

## Configurando a rede

Depois do nosso firmware ser atualizado, iremos acessar novamente a Linkit e neste próximo passo conectaremos ela na rede WiFi, no meu caso, irei conectá-la ao meu roteador.

Portanto acesso o menu *Network*, selecione a opção **Station Mode**, logo após,clique em **Detected Wi-Fi network**, aqui se abrirá uma lista de redes wifi disponíveis, faça a escolha de sua rede e logo depois digite a senha da rede. Veja a tela referente na imagem abaixo:

![img](https://douglaszuqueto.com/uploads/articles/artigo-25/linkit-wifi.png)

Logo depois de dar o Ok, sua linkit será reiniciada e conectada na rede de seu roteador.

![img](https://douglaszuqueto.com/uploads/articles/artigo-25/linkit-wifi-restart.png)

## Conhecendo o OpenWrt

Para quem não sabe, o firmware da Linkit é desenvolvido em cima do famoso firmware **OpenWrt**, logo temos acesso ao seu painel administrativo. Na sua interface da linkit, você verá uma mensagem como essa: **For advanced network configuration, go to OpenWrt.**, clique no link e você será redirecionado para a tela de login do openwrt. Veja na imagem abaixo a tela de login:

![img](https://douglaszuqueto.com/uploads/articles/artigo-25/openwrt.png)

Logue no painel utilizando as mesmas credenciais do usuário *root*. A partir de agora você terá acesso a um imenso acervo de informações, ferramentas e utilitários para operar sua linkit a nível de Sistema Operacional.

Sinta-se a vontade para fuçar, porém tome cuidado no que irá fazer :P.

Na imagem abaixo segue um print da área principal que é mostrada assim que você efetua o login:

![img](https://douglaszuqueto.com/uploads/articles/artigo-25/openwrt-theme.png)

Perceba que o tema não é nada agradável, mas não se preocupe, basta seguir os passos a seguir e você terá uma interface bem melhor na qual é baseada no BootstrapCSS.

**Passos:**

* Menu System -> 
* Sub-menu Language and Style ->
* Escolha o tema bootstrap ->
* Somente salve e aplique clicando no botão *Save & Apply*

Basta atualizar a página e teremos nossa área principal assim:

![img](https://douglaszuqueto.com/uploads/articles/artigo-25/openwrt-bootstrap.png)

## Explorando os exemplos contidos por default no firmware

Por default o firmware da linkit trás uns 3 exemplos para você testar em sua placa, para chegar até nesses exemplos será necessário você acessar via **SSH** o SO de sua placa. Portanto basta abrir seu terminal ou o Putty - como preferir, e abrir a conexão de acordo com o IP e usuário de sua linkit. No meu caso, ficará assim:

```
ssh root@192.168.0.130
```

Acessando, você verá uma tela parecida como demonstra abaixo:

```
→ ssh root@192.168.0.130
root@linkit.lan's password: 


BusyBox v1.23.2 (2016-09-27 07:54:34 CEST) built-in shell (ash)

  _______                     ________        __
 |       |.-----.-----.-----.|  |  |  |.----.|  |_
 |   -   ||  _  |  -__|     ||  |  |  ||   _||   _|
 |_______||   __|_____|__|__||________||__|  |____|
          |__| W I R E L E S S   F R E E D O M
 -----------------------------------------------------
 CHAOS CALMER (15.05.1, r49203)
 -----------------------------------------------------
  * 1 1/2 oz Gin            Shake with a glassful
  * 1/4 oz Triple Sec       of broken ice and pour
  * 3/4 oz Lime Juice       unstrained into a goblet.
  * 1 1/2 oz Orange Juice
  * 1 tsp. Grenadine Syrup
 -----------------------------------------------------
root@mylinkit:~# 
```

Agora que você está 'dentro' de sua linkit, navegue até a pasta **IoT/examples** que está na raiz do sistema operacional.

```
cd /IoT/examples
```

Dentro da pasta você verá 3 arquivos, são eles:

* blink-gpio44.js
* blink-gpio44.py
* bus-test.js

Veja que dentre os arquivos listados, temos arquivos js e python. Tudo isso devido ao fato que a Linkit vem com Nodejs e o Python embarcados na placa - é isso mesmo.

Portanto para rodar os exemplos é simples:

```
node blink-gpio44.js
```
 ... e
 
 ```
 python blink-gpio44.py
 ```
 
 Ambos fazerão um blink no led :).

## Finalizando

Bom, percebe-se claramente que neste artigo foi focado mais nos primeiros passos com a linkit -passos práticos mesmo, que era o real objetivo do artigo, por isso mencionei a indicação de leitura do artigo do Curvello.

Estou curtindo bastante a placa. Como comentei com alguns amigos, curti muito a arquitetura que ela possui, de ter um Microcontrolador e um Microprocessador rodando na mesma placa e trabalhando de forma independente um do outro.

Sem dúvidas é uma bela placa para projetos de automação residencial por exemplo. Em meus testes eu consegui tranquilamente rodar a aplicação abordada no artigo "[Integrando a aplicação web com banco de dados](https://goo.gl/dNQGK0)" e também consegui rodar o Broker MQTT Mosquitto, e o melhor - o consumo de recursos é minimo.

Então a dica é: Conheça já esta plaquinha :P.

## Próximos Passos

Como já citado acima, um dos próximos passos será validar os projetos aqui já desenvolvidos, e também começar a utilizar o microcontrolador que vem embarcado na placa, validar o uso de reles, sensores, comunicação serial entre o microcontrolador e microprocessador e ver também a utilização da comunicação em formato *Bridge*.

Por enquanto é isso amigos. Até a próxima.

## Referências

* [Linkit Smart 7688 Duo – Uma placa 2 em 1](https://goo.gl/AYYSBq);
* [Download do firmware](https://docs.labs.mediatek.com/resource/linkit-smart-7688/en/downloads);
* [Linkit - Get started](https://docs.labs.mediatek.com/resource/linkit-smart-7688/en/get-started);