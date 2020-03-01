Este artigo será destinado a uma dica de aplicativo mobile o qual retratará de um cliente MQTT.

# Introdução

Muitos já devem ter usados clientes mobiles para se conectar ao broker e fazer seus testes, confere?

Eu mesmo vinha usando o [MyMQTT](https://play.google.com/store/apps/details?id=at.tripwire.mqtt.client) e o [MQTT Client](https://play.google.com/store/apps/details?id=com.deepeshc.mqttrec), porém ambos aplicativos são extremamente simples e acabam agregando poucas funcionalidades.

Em um dado momento, efetuei uma pesquisa na play store para ver se existia alguma outra alternativa. Foi ai que encontrei o [IoT MQTT Dashboard](https://play.google.com/store/apps/details?id=com.thn.iotmqttdashboard). Um aplicativo simples, com uma boa interface e funcionalidades de fato bem interessantes.

## Funcionalidades

* Multi conexões
	* Suporte ao uso de SSL
	* Suporte ao uso de usuário/senha
	* Conexão aberta rodando em background
* Subscribe
	* Adição de componentes
	* Gráfico(caso a informação seja numérica)
	* QoS
	* Função de Notificação(versão pro do app)
	* JSON Converter(versão pro do app)
* Publish
	* Adição de componentes
		* Text
		* Button
		* Switch
		* Seekbar
		* Combo Box
		* Color Picker
		* Multi Buttons
		* Time Picker

## Prints

Basicamente o app é dividido em 3 telas principais: Todas inicial, Subscribe e Publish

### Tela inicial - Conexões

Nesta tela será listada todas conexões que você tem. Caso alguma conexão estiver aberta, será mostrado os circulo no card da conexão.

Na mesma tela também possui um botão flutuante para adicionar novas conexões.

![img](https://douglaszuqueto.com/uploads/articles/artigo-18/tela-inicial-conexoes.jpg)

### Adicionar Conexão

Na tela de cadastro de uma nova conexão temos um simples formulário, pedindo informações básicas para uma conexão com MQTT.

![img](https://douglaszuqueto.com/uploads/articles/artigo-18/adicionar-conexao.jpg)

### Tela Secundária - Conexão aberta

Quando a conexão é aberta, a tela abaixo é mostrada. Basicamente é as 2 telas no formato de Tabs

![img](https://douglaszuqueto.com/uploads/articles/artigo-18/conexao-aberta.jpg)

### Tela Subscribe

Sendo a primeira tela que se vê assim que se abre a conexão, nesta serão mostradas todas suas assinaturas através de simples componentes.

![img](https://douglaszuqueto.com/uploads/articles/artigo-18/conexao-aberta.jpg)

#### Adicionar Subscriber

Informações de entrada comuns, porém ressalta-se a escolha da unidade de medida, opção de notificação, json converter e escolha se a informação de entrada será numérica.

A opção de notificação é bem interessante, caso você tenha um sistema de alarme por exemplo, marcando esta opção você pode receber uma push notification em tempo real do ocorrido. Único fator é que essa funcionalidade está somente disponível na versão PRO do aplicativo.

![img](https://douglaszuqueto.com/uploads/articles/artigo-18/novo-subscriber.jpg)

### Gráfico

Como citado no tópico anterior, caso a opção **is numeric** for marcada você terá acesso à um gráfico. Para ter acesso à ele, basta clicar no componente que você deseja ver o histórico.

![img](https://douglaszuqueto.com/uploads/articles/artigo-18/subscriber-grafico.jpg)

### Tela Publish

Nesta tela será listado todos seus componentes para que você possa efetuar alguma ação, como ligar/desligar algo, enviar um determinado valor, dimerizar algo.

Existem mais componentes além dos ilustrados na imagem abaixo.

![img](https://douglaszuqueto.com/uploads/articles/artigo-18/tela-publish.jpg)

#### Adicionar Publisher

Clicando no botão para adicionar, uma pequena modal é aberta trazendo uma listagem com todos componentes que possui. Escolhendo algum, outra tela é aberta para você entrar com informações respectivas ao componente escolhido - nada de mais.

![img](https://douglaszuqueto.com/uploads/articles/artigo-18/novo-publisher.jpg)

## Vantagens

* Leve;
* Interface limpa e fluída;
* Funciona muito bem;
* Vai além de um simples app para conexão e teste com o broker mqtt;
* Funcionalidades interessantes como a de Notificações;
* Quantidade relativamente boa de componentes disponíveis;

## Desvantagens

* Na versão gratuita você verá publicidades no rodapé do aplicativo;
* Não possui uma tela de login;
* Possui poucas funcionalidades para a versão PRO do app(até o momento da publicação do artigo);
