Como mencionado no [artigo anterior](https://douglaszuqueto.com/artigos/do-hello-world-a-integracao-com-mqtt), o próximo artigo seria a criação de uma página web para controlar o led, pois bem, este artigo visa demonstrar a criação desta página para que você possa conectar suas coisas na web e poder ter controle sobre elas - Controlar uma lâmpada, ventilador....

# Introdução

Nesse primeiro momento, aviso que o script desenvolvido no artigo anterior será mantido, e aqui neste artigo, será criado apenas o projeto que fará a interatividade com o nosso projeto anterior, tudo isso de forma desacoplada, ou seja, os 2 projetos são independentes, por isso resolvi dividir o artigo anterior :).

Portanto, para criar a página web propriamente dita, será utilizado como servidor web uma simples aplicação Python com Flask. Flask é um microframework leve para aplicações python simples e de fácil aprendizado - particularmente achei bem tranquilo.

Como tecnologias web, de praxe, será usado HTML, CSS e JS, juntamente com algumas libs para nos auxiliar, como MaterializeCSS e Jquery. Não podemos esquecer também do principal - o script cliente para conexão com MQTT,  que neste caso será usado o paho-mqtt também - porém na versão para web.

## Criação da base da aplicação

Como mencionado acima, usaremos python juntamente com o Flask. Para proceder com o inicio da aplicação, devemos instalar a nossa dependência - usa-se a mesma ideia dos artigos anteriores: Usando o **PIP**

```
sudo pip install flask
```

Antes de continuarmos, para ficar claro, observe como ficará a estrutura de pastas da aplicação:
```
├── index.py
├── static
│   ├── dz.png
│   └── script.js
└── templates
    └── index.html

2 directories, 4 files

```

Na raiz, temos um arquivo *index.py*, que conterá o código necessário para servir nossa aplicação web, no restante, teremos duas pastas, templates e static - ambas são convenções que o Flask usa para organização. Na *static* terá a minha logo e o script, no qual terá toda lógica da aplicação em si para fazer a integração com o MQTT.

Já na pasta *templates*, por hora teremos um arquivo somente: *index.html*, que terá uma simples estrutura para tornar possível nossa interatividade com o led conectado na gpio da rpi3.

### Aquivo index.py

```python
# importa os pacotes
from flask import Flask, render_template

# instancia o flask
app = Flask(__name__)

# define uma rota, neste caso a rota raiz
@app.route('/')
def index():
    # render_template como o nome ja diz, ira renderizar nosso index.html
    return render_template('index.html')

if __name__ == "__main__":
    # inicia a aplicacao
    # 2 parametros sao passados, host e port
    # host = seu ip, neste caso sera 0.0.0.0 pois rodara em todas interfaces de rede
    # port = porta que sera usada, neste caso iremos rodar na porta 80
    app.run(host='0.0.0.0', port=80)

```

Observe acima que o que havia relatado anteriormente - uma aplicação utilizando Flask é bem simples rsrs.
O código está comentando, logo acho que passando os olhos você conseguirá entender a lógica aplicada.

Ficou com dúvida sobre o código acima? Dê uma olhada na [documentação](http://flask.pocoo.org/) do Flask :).


### Arquivo index.html

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <title>Douglas Zuqueto - Controle suas gpio através da internet</title>
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/materialize/0.97.3/css/materialize.min.css">
  <script src="https://cdnjs.cloudflare.com/ajax/libs/paho-mqtt/1.0.1/mqttws31.min.js"
  type="text/javascript"></script>
  <style media="screen">
  .app {
    margin-top: 60px;
  }
  </style>
</head>
<body>
</body>
<div class="container">
  <div class=" app">
    <h4 class="center">Controle suas gpio através da internet</h4>
    <br>
    <form method="GET" action="#">
      <div class="input-field col s12">
        <input id="broker" type="text" class="validate">
        <label for="broker" class="active">Broker</label>
      </div>
      <div class="input-field col s12">
        <input id="topic" type="text" class="validate">
        <label for="topic" class="active">Tópico</label>
      </div>
      <div class="input-field col s12">
        <input class="btn green" type="submit" id="save" name="save" value="Salvar informações">
        <input class="btn blue" type="button" id="connect" name="connect" value="Conectar">
      </div>
    </form>
    <br>
    <div class="col s12">

      <div class="center">
        <button class="waves-effect waves-light btn green btn-led" id="btn-on" disabled>ON</button>
        <button class="waves-effect waves-light btn red btn-led" id="btn-off" disabled>OFF</button>
      </div>
      <br>
      <h5 class="center">
        Confira o artigo clicando <a
        href="https://blog.dev/artigos/controle-suas-gpio-atraves-da-internet"
        target="_blank">aqui</a>
      </h5>
    </div>
    <p class="center">
      <br>
      <img src="/static/dz.png" class="" style="width:10%"/>
      <br>
      <strong>Douglas Zuqueto</strong>
    </p>
  </div>
</div>
<script src="//code.jquery.com/jquery-1.11.3.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/materialize/0.97.3/js/materialize.min.js"></script>
<script src="/static/script.js" type="text/javascript"></script>
</body>
</html>

```

A estrutura html não tem mistério, basicamente é dividida em 2 blocos: Formulário de configuração / conexão e Botões de ação - ligar / desligar o led(neste caso).

### Arquivo script.js

```js
/* json com configuracoes iniciais de conexao */
var json = {
  broker: 'test.mosquitto.org',
  topic: 'DZ/rasp/led'
};

/* resgata as informações do localStorage caso existir */
if( JSON.parse(localStorage.getItem('mqtt'))){
  json = JSON.parse(localStorage.getItem('mqtt'));
}

/* Instancia o paho-mqtt */
var mqtt = new Paho.MQTT.Client(
  json.broker,
  8080,
  "DZ-" + Date.now()
);

/* define aos eventos seus respectivos callbacks*/
mqtt.onConnectionLost = onConnectionLost;
mqtt.onMessageArrived = onMessageArrived;

function onConnectionLost(responseObject) {
  return console.log("Status: " + responseObject.errorMessage);
}
function onMessageArrived(message) {
  return console.log(message.destinationName, ' -- ', message.payloadString);
}

/* define aos eventos de Conexão seus respectivos callbacks*/
var options = {
  timeout: 3,
  onSuccess: onSuccess,
  onFailure: onFailure
};
function onSuccess() {
  console.log("Conectado com o Broker MQTT");
  mqtt.subscribe(json.topic, {qos: 1}); // Assina o Tópico led/1
  $(".container .btn-led").removeAttr('disabled');
}

function onFailure(message) {
  console.log("Connection failed: " + message.errorMessage);
}

/* função de conexão */
function init() {
  mqtt.connect(options); // Conecta ao Broker MQTT
}

/* função auxiliar para ligar/desligar o objeto conectado */
function led(value) { // Evento do Botão Desligar
  message = new Paho.MQTT.Message(value); // Cria uma nova mensagem
  message.destinationName = json.topic; // Define o tópico a ser enviado, neste caso: led/1
  mqtt.send(message); // Envia a mensagem
}

$(document).ready(function () {
  $('#broker').val(json.broker);
  $('#topic').val(json.topic);

  /* Eventos de configuração */
  $('#save').on('click', function () {
    var broker, topic;
    broker = $('#broker').val();
    topic = $('#topic').val();

    /* salva no localStorage os dados do formulário */
    localStorage.setItem("mqtt", JSON.stringify({broker: broker, topic: topic}));

    location.reload();
    return false;
  });

  $('#connect').on('click', function(){
    init();
  });

  /* Eventos Liga / Desliga objeto conectado */
  $('#btn-on').on('click', function () {
    led("1");
  });
  $('#btn-off').on('click', function () {
    led("0");
  });

});

```

O script js também está comentado. Para quem já programa para web estará mais que familiarizado com o que foi desenvolvido acima.

Tentei deixar o script o mais simples e burro possível para ficar de fácil entendimento. Portanto a responsabilidade do mesmo será inicializar as configurações, será efetuada as devidas configurações da conexão com o mqtt e depois, irá tratar os eventos de interatividade entre a página web e o mqtt. 

Basicamente temos 4 eventos principais:
* Configuração do mqtt
* Conexão com o mqtt
* Ligar o objeto
* Desligar o objeto

## Executando o projeto

Você poderá executar de várias formas o projeto.

Poderá ir criando os scripts de acordo com a estrutura passada e de acordo que irá lendo o artigo ou também poderá pegar os scripts do nosso [repositório](https://github.com/douglaszuqueto/douglaszuqueto-artigos/tree/master/controle-suas-gpio-atraves-da-internet) que está no Github.

Para os mais chegados com Git e Github, saberá o que fazer :p.

Tendo a estrutura do projeto, você já está apto a rodá-lo, para isso rode o comando abaixo:

```
sudo python index.py
```

Como estamos utilizando python e tecnologias web, você poderá rodar o projeto no seu computador, na própria raspberry ou até mesmo colocá-lo na nuvem.

Como eu tenho uma VPS, resolvi colocar o projeto lá para ficar ao alcance de todos. Porém eu não deixarei rodando eternamente lá, ficará por algumas horas ou dias de acordo com a hora de publicação deste artigo.

Para acessar a aplicação online é só acessar o domínio que comprei para o projeto [IoTBr](http://iot-br.com).

**OBS:** Não esqueça de rodar o script desenvolvido no [artigo anterior](https://douglaszuqueto.com/artigos/do-hello-world-a-integracao-com-mqtt) na raspberry.
## Resultados

### Rodando no desktop

![img](https://douglaszuqueto.com/uploads/articles/artigo-15/print-desktop.png)

### Rodando no mobile

![img](https://douglaszuqueto.com/uploads/articles/artigo-15/print-mobile.png)


## Finalizando

Observou que não é um bixo de 7 cabeças construir um simples projeto e conectá-lo na web né?

Este artigo foi baseado em cima do artigo anterior, mas nada impede de você trocar o led e ligar uma lâmpada, um ventilador, você só precisa colocar um shield rele e fazer as devidas conexões. 

Neste caso, seu eu quisesse, eu colocaria um shield rele na mesma porta em que o led está ligado, e eu facilmente já estaria ligando/desligando algo, legal né?.

**Ressalto novamente pessoal, caso estejam curtindo os artigos, não deixe de seguir minha [página no facebook](https://www.facebook.com/douglaszuquetooficial/), parece simples mas é importante para mim :)**. 


## Referências
* [Flask](flask.pocoo.org)
* [Paho Mqtt - Cliente JavaScript](https://eclipse.org/paho/clients/js/)
* [MaterializeCSS](materializecss.com)
* [JQuery](https://jquery.com/)


Por enquanto é isso pessoal. Abraços.