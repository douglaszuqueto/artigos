## Introdução

Hoje em dia ter dados estatísticos de determinado nicho é importantíssimo concorda? Portanto para isso se faz necessário um local de armazenamento para salvar esses dados para que depois sejam tratados e manipulados de alguma forma.

E como solucionar este problema? Onde armazenar os dados? Em um arquivo txt, csv, xml, json, banco de dados? Minha resposta será: **Em um banco de dados**. E será isso que o artigo abordará - Uma integração com banco de dados, que neste caso será usado o famoso *sqlite*.

Nos projetos dos artigos anteriores já foi abordado conexão com mqtt, aplicações web, sensores, comunicação em tempo real, e agora chegou a vez de persistir tais dados.

Três artigos serão usados como base do desenvolvimento deste artigo, portanto caso ainda não os tenha lido, fica a listagem abaixo:

* [Do Hello World à integração com MQTT](https://douglaszuqueto.com/artigos/do-hello-world-a-integracao-com-mqtt);
* [Controle suas gpio's através da internet](https://douglaszuqueto.com/artigos/controle-suas-gpio-atraves-da-internet");
* [Sensoriamento Real Time com MQTT e Websockets](https://blog.dev/artigos/sensoriamento-real-time-com-mqtt-e-websockets);

## SQLite

Como citado, usaremos o ***sqlite*** como banco de dados. Usaremos ele pois é mais 'nativo' nos sistemas operacionais, assim não precisa-se ficar instalando dependências ou até mesmo precisar um servidor de banco de dados.

Mais adiante será abordado outros banco de dados , mas para o propósito deste artigo o sqlite se sairá muito bem e quando integrar com outros bancos de dados, vocês perceberão que pouca coisa mudará :).

## Estrutura dos artigos anteriores

Nos artigos anteriores geralmente usamos uma base, mudando apenas a funcionalidade do foco do artigo - acionar um botão, acompanhar o valor da temperatura. Percebe-se que pouca coisa é mudada de um artigo para o outro.

Neste não terá muita diferença. Pois será apenas acrescentado uma tabela, ou seja apenas html. 

Única coisa que vale apenar ressaltar, é que refatorei um pouco da aplicação no que tange as camadas de javascript. Caso queiram ver como ficou os arquivos refatorados, basta acessar o [link](https://github.com/douglaszuqueto/douglaszuqueto-artigos/tree/master/integrando-a-aplicacao-web-com-banco-de-dados/app/static/js) e visualizar os arquivos ***gauge.js*** e ***app.js***. 

O código **js** estava mal implementado e feio e tal dia, o amigo [Wendell](https://github.com/WendellAdriel) mandou um projeto que ele iria apresentar em um workshop. Olhei para o código dele e senti vergonha do meu haha, portanto não pensei duas vezes e comecei a refatorar a aplicação, que por sinal achei o resultado ótimo.

## Implementação

Para começar a implementação vamos seguir as etapas listadas abaixo:

* Criação da tabela;
* Implementação de um robô mqtt;
* Recuperação dos dados cadastrados;

A implementação da aplicação terá como base a aplicação implementada no artigo: [Sensoriamento Real Time com MQTT e Websockets](https://douglaszuqueto.com/artigos/sensoriamento-real-time-com-mqtt-e-websockets), onde a cada segundo o valor do sensor de temperatura é publicado e mostrado em tempo real na dashboard web.

### Criação da tabela

Para efetuar a criação da nossa tabela - *temperature*, será desenvolvido um pequeno script para efetuar a devida criação. Para ver como ficou o script veja o código abaixo:

```python
import sqlite3

conn = sqlite3.connect('database.db')
cursor = conn.cursor()

cursor.execute("""
    CREATE TABLE temperature (
        id INTEGER NOT NULL PRIMARY KEY AUTOINCREMENT,
        temperature TEXT NOT NULL,
        created_at TIMESTAMP DEFAULT NOW
    );
""")

print('Tabela temperature criada com sucesso.')

conn.close()
```

Veja que é um script bem simples, apenas importa o pacote *sqlite3*, faz a devida conexão e posteriormente cria a estrutura da tabela em nosso banco de dados(neste caso como estamos usando o sqlite, o banco de dados fica dentro de um arquivo, no nosso caso ele é chamado de *database.db* - pode-se atribuir qualquer nome :)).

Apenas isso e nosso banco já será criando juntamente com a estrutura da tabela *temperature*.

###  Implementação de um robô mqtt

Nesta etapa será criado um robô que será responsável por ficar escutando determinado tópico e caso o tópico seja o que queremos, uma função será invocada com o objetivo de cadastrar a temperatura recebida do sensor mcp9808.

Iremos usar a base do script mqtt tratado no artigo [Do Hello World à integração com MQTT](https://douglaszuqueto.com/artigos/do-hello-world-a-integracao-com-mqtt).

Veja abaixo como ficará nosso robô:

```python
import sys
import paho.mqtt.client as mqtt
import sqlite3

broker = "broker.iot-br.com"
port = 8080
keppAlive = 60
topic = 'DZ/#'

# funcao responsavel por cadastrar no banco de dados a temperatura do sensor
def insertTemperature(message):
    conn = sqlite3.connect('database.db')
    cursor = conn.cursor()

    cursor.execute("""
        INSERT INTO temperature
        (temperature)
        VALUES (?)
    """, (message,))

    conn.commit()
    conn.close()

def on_connect(client, userdata, flags, rc):
    print("[STATUS] Conectado ao Broker. Resultado de conexao: "+str(rc))

    client.subscribe(topic)

def on_message(client, userdata, msg):

    message = str(msg.payload) # converte a mensagem recebida
    print("[MSG RECEBIDA] Topico: "+msg.topic+" / Mensagem: "+ message)

    if msg.topic == 'DZ/gauge/temperature':

        insertTemperature(message) # invoca o metodo de cadastro passando por parametro a temperatura recebida

try:
    print("[STATUS] Inicializando MQTT...")

    client = mqtt.Client()
    client.on_connect = on_connect
    client.on_message = on_message

    client.connect(broker, port, keppAlive)
    client.loop_forever()

except KeyboardInterrupt:
    print "\nScript finalizado."
    sys.exit(0)
```

Observe que a base de conexão ao mqtt é a mesma do artigo citado acima. A diferença é que neste script integramos com o banco de dados.

Basicamente a lógica está na função ***insertTemperature***, pois é ela que será responsável por a cada vez que for invocada, cadastrar a temperatura no banco de dados. Veja que pouca coisa muda em relação ao script anterior(criação da tabela) , o que difere realmente é a **query sql **, que neste caso é uma query de inserção de dados no banco.

### Recuperação dos dados cadastrados

Até agora criamos a base necessária para que a temperatura do nosso sensor seja lida e cadastrada no banco de dados. Portanto o próximo passo será recuperar os dados para mostrar na nossa aplicação web.

Para começar vamos recuperar os dados cadastrados, para isso veja como deverá ficar seu arquivo ***index.py***.

**Observação:** A implementação desta etapa até a etapa final, é com base no artigo [Controle suas gpio's através da internet](https://douglaszuqueto.com/artigos/controle-suas-gpio-atraves-da-internet").

```python
import sqlite3
from flask import Flask, render_template

debug = True
app   = Flask(__name__)

# funcao responsavel por efetuar a leitura do banco de dados e nos retornar as temperaturas cadastradas
def getTemperature():
    conn   = sqlite3.connect('database.db')
    cursor = conn.cursor()

    cursor.execute("""
        SELECT id, temperature, strftime('%d/%m/%Y %H:%M:%S', created_at) as created_at FROM temperature
        ORDER BY id DESC
        LIMIT 50;
    """)

    return cursor.fetchall()

    conn.close()

@app.route('/')
def index():
    # veja que abaixo passamos um atributo 'temperatures' no metodo render_template
    # isso nada mais e que estamos passando as temperaturas recuperadas para nosso html
    # para que depois se possa mostrar os dados na tabela
    return render_template('index.html', temperatures=getTemperature())

if __name__ == "__main__":
    if debug:
        app.run(host='0.0.0.0', port=80, debug=True)
    else:
        app.run(host='0.0.0.0', port=80)

```

Assim como nos outros arquivos, aqui não foi muito diferente, tivemos pouca modificação. Simplesmente recuperamos as temperaturas através da função ***getTemperature*** para que posteriormente os dados retornados sejam repassados para a view(nossa página html).

Na query SQL eu aproveitei e já fiz a formatação da data, ficando no formato **08/02/2017 21:29:16**. Outra coisa que foi feito, foi **limitar** a quantidade de dados em 50 registros, a trava foi colocada pois como a temperatura é enviada a cada segundo, chegaria num ponto que teria 1 zilhão de registros :P.

Finalizando este passo, o próximo passo será mostrar os dados na view, confere?. 

Lembra que no retorno do *render_template* passamos uma variável chamada **temperatures**? pois então, ela está disponível no  escopo da página web.

Temos os dados todos disponíveis concorda? Agora precisamos usa-lôs de alguma forma. Para isso usaremos um laço de repetição **for** encapsulado com uma **table** - que será onde os dados serão mostrados.

Para usar o for, o **Flask** nos provê algumas diretivas, e uma delas é o *for*, então precisamos apenas percorrer os dados da variável *temperatures* e alimentar nossa tabela. Veja abaixo como ficará o trecho referente à nossa tabela:

```html
<table class="striped">
  <thead>
  <tr>
    <th>Id</th>
    <th>Temperature</th>
    <th>Created_at</th>
  </tr>
  </thead>
  <tbody>
  {% for t in temperatures %}
  <tr>
    <td>{{ t[0] }}</td>
    <td>{{ t[1] }}ºC</td>
    <td>{{ t[2] }}</td>
  </tr>
  {% endfor %}
  </tbody>
</table>
```

Bacana né? Com isso já conseguimos ver as últimas 50 temperaturas cadastradas em nosso banco de dados.

## Testando

Para você efetuar os testes, pode usar um cliente mqtt no celular, por comando de linha e etc. Eu utilizei o projeto desenvolvido no artigo [Sensoriamento Real Time com MQTT e Websockets](https://blog.dev/artigos/sensoriamento-real-time-com-mqtt-e-websockets). Fique a vontade com a escolha :).

Somente lembre-se que todos os tópicos da aplicação devem estar iguais :P. Eu utilizo o tópico **DZ/gauge/temperature**, fica a livre escolha também em qual tópico utilizar.

* [Código fonte da aplicação](https://github.com/douglaszuqueto/douglaszuqueto-artigos/tree/master/integrando-a-aplicacao-web-com-banco-de-dados);

## Resultados

Como de costume, para acessar a aplicação desenvolvida, basta acessar [este link](http://broker.iot-br.com).

Veja na imagem abaixo o print de como ficou nossa interface web:

![img](https://douglaszuqueto.com/uploads/articles/artigo-21/print.png)

## Próximos passos

Neste artigo fizemos uma simples abordagem com um banco de dados bem simples. Nos próximos projetos implementaremos mais funções, posso citar como exemplo *remover uma determinada temperatura*, algo simples :).

E mais adiante começaremos a usar outros banco de dados, como MySQL, Redis dentre outros.

## Finalizando

Veja que foi uma integração bem legal concorda? Basicamente foi a união dos projetos abordados nos artigos anteriores. Neste artigo, única coisa de diferente que foi abordado foi a integração com banco de dados, que era o real objetivo deste artigo.

Apenas fomos aproveitando os projetos e focamos realmente na integração que queríamos :). Espero que este projeto tenha sido útil para vocês pessoal.

Se curtir o artigo, não deixe de compartilhar com seus amigos, deixar seu like, seu comentário e curtir minha [página](https://www.facebook.com/douglaszuquetooficial) para ficar por dentro das novidades.

Um grande abraço e até logo.

## Referências
* [Gerenciando banco de dados SQLite3 com Python - Parte 1](http://pythonclub.com.br/gerenciando-banco-dados-sqlite3-python-parte1.html)
* [Flask - Rendering Templates](http://flask.pocoo.org/docs/0.12/quickstart/#rendering-templates)
* [Flask - List of Control Structures(For)](http://jinja.pocoo.org/docs/2.9/templates/#list-of-control-structures)