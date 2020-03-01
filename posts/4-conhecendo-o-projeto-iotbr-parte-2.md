Dando continuidade a série de artigos, neste artigo será abordado o Hardware do protótipo que foi desenvolvido no TCC.

## O Protótipo

Como mencionado no [artigo anterior](https://douglaszuqueto.com/artigos/conhecendo-o-projeto-iotbr-parte-1), o protótipo foi constituído de 1 arduino pro mini, 1 esp8266, 1 sensor de corrente e uma fonte 5v com regulador de tensão de 3.3 para a alimentação do esp8266.

Esse protótipo resultou numa tomada(semelhante a uma tomada, porém em formato T) sem fio que realizava a medição do consumo de energia e enviava o valor através da rede wifi utilizando o protocolo MQTT. Veja a foto da tomada abaixo.

![img](https://douglaszuqueto.com/uploads/articles/artigo-8/tomada.png)

## Por que resolvi fazer uma tomada e não adaptar diretamente na caixa da tomada?

Antes mesmo de tomar esta decisão, estava convicto de fazer algo diretamente na caixa da tomada, mas não havia gostado muito da ideia, pois me faltava conhecimento, logo eu não tinha certeza se fosse implementado de fato iria funcionar, principalmente por conta da comunicação wifi.

Diante desse cenário, fui em busca por outras soluções e por acaso(risos) eu achei uma solução perfeita(no meu ponto de vista), que foi o T com 4 saídas mostrado na imagem anterior. Esse cara se encaixou muito bem no projeto, pois considerava os pontos a seguir importantes.

 * 1º seria mais fácil a colocação do embarcado;
 * 2º corria menos risco dos pacotes ou a conexão do wifi se perder;
 * 3º teria liberdade para testar em qualquer tomada pois não estaria preso a ter que mexer fisicamente nas tomadas ou na fiação;
 * 4º daria uma liberdade maior para futuras implementações como: sensor de tensão, modulo rele - por exemplo;

## Fluxo de trabalho

O funcionamento do protótipo tinha um fluxo único: Sensor de corrente -> Arduino (efetuava os cálculos) -> Comunicação Serial -> ESP8266 -> Broker MQTT. Esse era o fluxo que acontecia por parte do embarcado. Observe o fluxo de forma simples na imagem abaixo.

![img](https://douglaszuqueto.com/uploads/articles/artigo-8/comunicacao-serial.png)

O fluxo era relativamente simples, concorda?. O mais complicado era implementar o cálculo com base no valor recolhido pelo sensor e posteriormente enviar esses dados via comunicação serial e receber tudo certinho no esp8266.

O envio dos dados do arduino para o esp tinha intervalo de +-  700ms, pois deveria receber no esp8266 o pacote, e reenvia-lo via mqtt para o broker, então esse foi um intervalo agradável para fechar aproximadamente com um fluxo total  de 1 segundo até o recebimento do pacote no cliente(será abordado na parte 3 desta série de artigos).

Agora, na imagem abaixo, veja o diagrama que demonstra o fluxo do embarcado até o Broker MQTT. 

![img](https://douglaszuqueto.com/uploads/articles/artigo-8/comunicacao-mqtt.png)

Este fluxo foi muito aplaudido quando tudo funcionou(risos), pois era o maior projeto e mais complexo que havia desenvolvido até o momento. A velocidade do envio dos dados era extremamente rápido - mesmo possuindo N camadas, o resultado desta primeira etapa era **espetacular**.


## Trecho do firmware utilizado no Arduino e no ESP8266

### Arduino

![img](https://douglaszuqueto.com/uploads/articles/artigo-8/firmware-arduino.png)

### ESP8266

![img](https://douglaszuqueto.com/uploads/articles/artigo-8/firmware-8266.png)

**Obs: Assim que conseguir testar novamente todo o firmware, colocarei no meu github.
	

Finalizando este artigo, ** deixo claro que embarcados é meu hobby**, sou um grande entusiasta da área. Portanto, tudo que desenvolvi neste protótipo foi com base no conhecimento que adquiri ao longo dos anos desde que conheci a **Plataforma Arduino**.

Agradeço pela leitura e até o próximo artigo, onde será tratado sobre o software da aplicação.