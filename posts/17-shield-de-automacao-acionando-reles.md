## Introdução

Felizmente vamos para a segunda etapa da série 'Conhecendo o shield de automação residencial para raspberry pi', nesta etapa será abordado o funcionamento e acionamento dos reles contidos na placa. Portando, caso ainda não tenha efetuado a leitura do primeiro artigo, fica a sugestão de leitura [neste link](https://douglaszuqueto.com/artigos/conhecendo-o-shield-de-automacao-residencial-para-raspberry-pi).

Resumidamente esta placa conta com 10 reles 12V que suporta tensões de 220V e corrente de até 10A. Nesta placa, os reles são acionados através do CI MCP23017, para quem não conhece, se trata de um expansor de portas que utiliza interface de comunicação I2C, ou seja, o suporte ao I2C usa apenas duas GPIO de sua raspberry pi, isso é demais não é?

Também será efetuado um mapeamento: *Rele -> Porta do CI*, para ficar de fácil manuseio para posteriormente colocar em prática em scripts, ferramentas por linha de comando e etc. Posteriormente ao mapeamento, testaremos o acionamento através de códigos que são disponibilizados pelo autor.

## Materiais utilizados

* Raspberry PI 3;
* Shield de Automação;
* Fonte de alimentação 12V 2A(ou superior);

## Mapeamento do circuito

Antes de efetuar o mapeamento, segue na imagem abaixo o pinout referente ao mcp2307.

![img](https://douglaszuqueto.com/uploads/articles/artigo-22/pinout-mcp23017.png)

Na tabela abaixo está listado o rele e sua respectiva conexão à gpio do mcp.

| Rele | MCP23017 |
|:---:|:---:|
| 1 | GPA0 |
| 2 | GPA1 |
| 3 | GPA2 |
| 4 | GPA3 |
| 5 | GPA4 |
| 6 | GPA5 |
| 7 | GPA6 |
| 8 | GPA7 |
| 9 | GPB0 |
| 10 | GPB1 |

## Testando o acionamento dos reles

Para efetuar os devidos testes, primeiramente iremos testar a referência que está no site. Portanto abra o [link](https://github.com/Moisesbr/raspberry_exp_board) e leia os tópicos 2 e 3, "Installing dependences" e "Enable I2C", respectivamente.

Isso se faz necessário pois você precisa ter as ferramentas para poder se comunicar via i2c. Logo após realizar os passos, você pode baixar este repositório para executar o exemplo ou até mesmo pegar o código que colocarei logo abaixo:

```python
from bottle import route, run, template, get, post, request, Response
from bottle import HTTPResponse
import json
import smbus

bus = smbus.SMBus(1) 	# For revision 1 Raspberry Pi, change to bus = smbus.SMBus(1) for revision 2.
address = 0x20 			# I2C address of MCP23017
bus.write_byte_data(0x20,0x00,0x00) # Set all of bank A to outputs 
bus.write_byte_data(0x20,0x01,0x00) # Set all of bank B to outputs

my_token = '2e52d3eb834e09f30509fcf4837478f207e71f59'

dic_pins = {"relay1":0,"relay2":1,"relay3":2,"relay4":3,"relay5":4,"relay6":5,"relay7":6,"relay8":7,"relay9":0,"relay10":1, "transistor1":3, "transistor2":2, "transistor3":4}
#Classe para criar objetos json
class Simple_Json_Object:
    def to_JSON(self):
        return json.dumps(self, default=lambda o: o.__dict__, 
            sort_keys=True, indent=4)

def verify_auth_token(token):
	try:
		if my_token == token:
			return True
		else:
			return False
	except:
		return False

@route('/relay/transistor_on_off', method='POST')
def transistor():
   transistor_name = request.forms.get('transistorname')
   state = request.forms.get('state')
   token = request.forms.get('token')
   valid_token = verify_auth_token(token)
   if valid_token == True:
      if transistor_name == 'transistor1' or transistor_name == 'transistor2' or transistor_name == 'transistor3':
         if state == "high":
            print "aqui"
            high_rele(dic_pins[transistor_name],"b")
            print dic_pins[transistor_name]
            theBody = json.dumps({'200': transistor_name}) # you seem to want a JSON response
            return HTTPResponse(status=200, body=theBody)
         elif state == "low":
            low_rele(dic_pins[transistor_name],"b")
            theBody = json.dumps({'200': transistor_name}) # you seem to want a JSON response
            return HTTPResponse(status=200, body=theBody)
         else:
            theBody = json.dumps({'412': "Precondition Failed"}) # you seem to want a JSON response
            return HTTPResponse(status=412, body=theBody)
      else:
         theBody = json.dumps({'412': "Precondition Failed"}) # you seem to want a JSON response
         return HTTPResponse(status=412, body=theBody)
   else:
      theBody = json.dumps({'401': 'Unauthorized'}) # you seem to want a JSON response
      return HTTPResponse(status=401, body=theBody)

@route('/relay/toogle_relay', method='POST')
def toogle():
	relay_name = request.forms.get('relayname')
	token = request.forms.get('token')
	valid_token = verify_auth_token(token)
	if valid_token == True:
		if relay_name == 'relay1' or relay_name == 'relay2' or relay_name == 'relay3' or relay_name == 'relay4' or relay_name == 'relay5' or relay_name == 'relay6' or relay_name == 'relay7' or relay_name == 'relay8':
			tooglerelay_func(dic_pins[relay_name],"a")
		if relay_name == 'relay9' or relay_name == 'relay10':
			tooglerelay_func(dic_pins[relay_name],"b")
		theBody = json.dumps({'200': relay_name}) # you seem to want a JSON response
		return HTTPResponse(status=200, body=theBody)
	else:
		theBody = json.dumps({'401': 'Unauthorized'}) # you seem to want a JSON response
		return HTTPResponse(status=401, body=theBody)

@route('/relay/on_off', method='POST')
def on_off():
	relay_name = request.forms.get('relayname')
	token = request.forms.get('token')
	state = request.forms.get('state')
	valid_token = verify_auth_token(token)
	if valid_token == True:
		if relay_name == 'relay1' or relay_name == 'relay2' or relay_name == 'relay3' or relay_name == 'relay4' or relay_name == 'relay5' or relay_name == 'relay6' or relay_name == 'relay7' or relay_name == 'relay8':
			if state == "high":
				high_rele(dic_pins[relay_name],"a")
			elif state == "low":
				low_rele(dic_pins[relay_name],"a")
			else:
				theBody = json.dumps({'412': "Precondition Failed"}) # you seem to want a JSON response
				return HTTPResponse(status=412, body=theBody)
		if relay_name == 'relay9' or relay_name == 'relay10':
			if state == "high":
				high_rele(dic_pins[relay_name],"b")
			elif state == "low":
				low_rele(dic_pins[relay_name],"b")
			else:
				theBody = json.dumps({'412': "Precondition Failed"}) # you seem to want a JSON response
				return HTTPResponse(status=412, body=theBody)
		theBody = json.dumps({'200': relay_name}) # you seem to want a JSON response
		return HTTPResponse(status=200, body=theBody)
	else:
		theBody = json.dumps({'401': 'Unauthorized'}) # you seem to want a JSON response
		return HTTPResponse(status=401, body=theBody)

@route('/relay/status', method='POST')
def estado_dos_reles():
   token = request.forms.get('token')
   valid_token = verify_auth_token(token)
   if valid_token == True:
      theBody = come_back_reles()
      return HTTPResponse(status=200, body=theBody)
   else:
      theBody = json.dumps({'401': 'Unauthorized'}) # you seem to want a JSON response
      return HTTPResponse(status=401, body=theBody)

def come_back_reles():
   aux_str_retorno = ""
   bus.write_byte_data(0x20,0x00,0x00) # Set all of bank A to outputs 
   bus.write_byte_data(0x20,0x01,0x00) # Set all of bank B to outputs
   # Read current values from the IO Expander
   retorno_bank_A =  bus.read_byte_data(address, 0x12)
   #print "{0:b}".format(retorno_bank_A)
   retorno_bank_B =  bus.read_byte_data(address, 0x13)
   #print "{0:b}".format(retorno_bank_B)
   bank_16bits = ( retorno_bank_B << 8) | retorno_bank_A
   #print "{0:b}".format(bank_16bits)

   obj_json = Simple_Json_Object()
   obj_json.relay_status = Simple_Json_Object()
   #obj_json.relay_status.retorno_reles = [0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 0, 0, 0, 1]
   obj_json.status_transistor = Simple_Json_Object()
   #obj_json.status_transistor.retorno_transistor = [0, 0]
   temp_array = []
   #for para montar json de reles
   #for n in [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 15]:
   for n in [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]:
      if (bank_16bits >> n) & 1 :
         temp_array.append(1)
      else:
         temp_array.append(0)
   obj_json.relay_status.return_relays = temp_array
   temp_array = []
   #for n in [10, 11, 12]:
   for n in [11, 10, 12]:
      if (bank_16bits >> n) & 1 :
         temp_array.append(1)
      else:
         temp_array.append(0)
   obj_json.status_transistor.return_transistor = temp_array
   #print(obj_json.to_JSON())
   return obj_json.to_JSON()   

def tooglerelay_func(output, bank):
   # Set the correct register for the banks
   if bank == "a" :
      register = 0x12
   elif bank == "b" :
      register = 0x13
   else:
      print "Error! Bank must be a or b"
   # Read current values from the IO Expander
   value =  bus.read_byte_data(address,register) 
   # Shift the bits for the register value, checking if they are already set first
   if (value >> output) & 1 :
      #already high go to lo low state
      value -= (1 << output)
   else:
      #go to lo high state
      value += (1 << output)
   # Now write to the IO expander
   bus.write_byte_data(address,register,value)

def high_rele(output, bank):
   # Set the correct register for the banks
   if bank == "a" :
      register = 0x12
   elif bank == "b" :
      register = 0x13
   else:
      print "Error! Bank must be a or b"
   # Read current values from the IO Expander
   value =  bus.read_byte_data(address,register) 
   if (value >> output) & 1 :
     print "Output GP"+bank.upper()+str(output), "is already high."
   else:
      value += (1 << output)
   #Now write to the IO expander
   bus.write_byte_data(address,register,value)
   print "\n\r debug high_rele: " + str(value)

def low_rele(output, bank):
   # Set the correct register for the banks
   if bank == "a" :
      register = 0x12
   elif bank == "b" :
      register = 0x13
   else:
      print "Error! Bank must be a or b"
   # Read current values from the IO Expander
   value =  bus.read_byte_data(address,register) 
   if (value >> output) & 1 :
      value -= (1 << output)
   else:
     print "Output GP"+bank.upper()+str(output), "is already low."
   # Now write to the IO expander
   bus.write_byte_data(address,register,value)
   print "\n\r debug low_rele: " + str(value)

run(host='0.0.0.0', port=9000)
```

Resumidamente esse código python tem como objetivo criar uma api restful para podermos controlar os reles usando alguns endpoints que a api nos entrega.

Basicamente as funcionalidades implementadas que temos acesso é:

* toogle
* on_off
* estado_dos_reles

Tendo o script em mãos e levando em conta que você já esteja com seu embarcado conectado, dependências instaladas e i2c ativado, você pode rodar o script com o comando abaixo:

```
python api_relay.py
```

Este comando irá iniciar a api e você poderá acessar utilizando: **ipDaRasp:9000**. A saída do comando em seu terminal será relativamente igual a essa:

```
pi@raspberrypi:~/projeto-arduino $ python relay.py 
Bottle v0.12.13 server starting up (using WSGIRefServer())...
Listening on http://0.0.0.0:9000/
Hit Ctrl-C to quit.
```

Nos 3 tópicos abaixo, será tratado individualmente a funcionalidade de cada um.

### Toogle

O toogle irá alternar entre o valor que o rele se econtra atualmente. 

Para efetuar os testes, assim como o README do repositório demonstra, também iremos utilizar o Curl para fazer a requisição para nossa api.

Veja abaixo como ficará o comando referente:

```
curl -X POST -F "token=2e52d3eb834e09f30509fcf4837478f207e71f59" -F "relayname=relay1" "http://192.168.0.103:9000/relay/toogle_relay"
```

Simples né? Consegue interpretar o comando acima?

Basicamente ele se divide em 4 parâmetros, são eles:

* Método HTTP: **-X POST**;
* Token: **-F "token=2e52d3eb834e09f30509fcf4837478f207e71f59"**
* Nome referente ao rele: **-F "relayname=relay1"**
* Url do endpoint: **"http://192.168.0.11:9000/relay/toogle_relay"**

Executando repetitivamente o comando acima, terá o famoso **blink** no rele :P.

### On Off

Como o próprio nome diz, nessa funcionalidade ligará ou desligará o rele dependendo do parâmetro que for passado no comando *curl*.

```
curl -X POST -F "token=2e52d3eb834e09f30509fcf4837478f207e71f59" -F "relayname=relay1" -F "state=high"     "http://192.168.0.11:9000/relay/on_off"
```
Observe que desta vez foi acrescendo o parâmetro **state** apontando para o valor ***high***, que vem a ser para ligar o rele. Caso queira desligar o rele, basta trocar o valor para ***low***. Veja também que a url foi mudada ;).

### Status dos reles

Nesta próxima funcionalidade, podemos efetuar a **leitura do status** que o rele se encontra no momento, para isso execute o comando abaixo:

```
curl -X POST -F "token=2e52d3eb834e09f30509fcf4837478f207e71f59" "http://192.168.0.103:9000/relay/status"
```

Simples né? Com isso uma listagem com o status de todos reles e transistores(será abordado num próximo momento) serão printados na tela. A saída será assim:

```
{
    "relay_status": {
        "return_relays": [
            0, 
            0, 
            0, 
            0, 
            0, 
            0, 
            0, 
            0, 
            0, 
            1
        ]
    }, 
    "status_transistor": {
        "return_transistor": [
            0, 
            0, 
            0
        ]
    }
}
```

Nada mais é que um retorno no formato ***json***. Observe que no último valor, o valor é 1, logo, sabe-se que o último rele - no caso o rele 10, está acionado :).

## Finalizando

Veja que o manuseio dos reles é bem simples e não tem mistério.

Neste artigo, o foco era demonstrar como se dava o funcionamento dos reles bem como o acionamento dos mesmos. O Script que foi utilizado tem outras funções como acionar os transistores, porém essa parte será tratada num próximo artigo. O que realmente queria era abordar o uso dos reles :p.

O exemplo que foi disponibilizado foi bem legal, vai além de uma simples implementação, primeiro porque é uma implementação de **API**, segundo porque tem uma 'certa' segurança, pois como você deve ter observado, precisa-se passar um token válido para poder manipular os reles. Caso você não tenha um token válido, a api lhe retorna uma mensagem de **acesso não autorizado**.

Poderia ter mais material sobre, como uma demo em formato de aplicativo ou até mesmo uma simples interface web para controle. Mas está tranquilo.

## Próximos passos

Como citei logo acima sobre a falta de material no que tange a interatividade com a placa, irei resolver este problema. Um dos passos será construir uma pequena aplicação web para controle dos 10 reles utilizando mqtt e websockets, para permitir a comunicação em tempo real. 

Futuramente pretendo desenvolver uma aplicação completa, pois temos em mãos um shield de automação, então nada melhor que colocar ele para uso haha. Aos poucos vamos caminhando para essa finalidade, cada artigo solto não é em vão. Começamos devagar e aos poucos vamos integrando as tecnologias :).

## Referências

[Repositório com os códigos de exemplo](https://github.com/Moisesbr/raspberry_exp_board);

Caso tenha encontrado algum erro de escrita, termo técnico, abordagem, não pense duas vezes em nos contatar :p.

Se curtir o artigo, não deixe de compartilhar com seus amigos, deixar aquele like e aquele feedback, ok?.

Abraço e até logo.