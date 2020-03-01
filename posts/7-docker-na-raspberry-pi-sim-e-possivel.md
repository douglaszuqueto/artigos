No [artigo anterior](https://douglaszuqueto.com/artigos/primeiros-passos-com-raspberry-pi-3) - o qual eu descrevi sobre os primeiros passos com Rasbperry  - eu mencionei a curiosidade que tinha de validar o uso do docker em uma ou mais raspberry's, portanto toda brincadeira começará por este artigo, o qual irá tratar dos passos de instalação/configuração do docker e também abordará um teste prático para que vocês possam observar o docker em ação na plaquinha.

## Instalação e Configuração do Docker

Para instalar o Docker na Rasp é relativamente fácil, já que se tem um script automatizado que você deverá executar. Para proceder com a instalação execute o comando abaixo.

```sh
curl -sSL https://get.docker.com | sudo sh
```

O script executado terá como objetivo verificar o SO, adicionar os repositórios necessários, atualizá-los e depois efetuar o download do Docker juntamente com suas dependências(aqui, todo o processo demorou em torno de 3 min).

Se tudo der certo, você já terá o Docker instalado, bastando apenas mais 2 passos para a finalização do processo. A primeira coisa a se fazer, é atribuir o seu usuário ao grupo do docker, para que seu usuário tenha acesso ao docker sem que precisa rodar *sudo docker* toda vez, então para que funcione, execute o comando abaixo.

```sh
sudo usermod -aG docker seuUsuario
```

Pronto, agora é só reiniciar sua rasp e depois de reinicializado, execute o comando abaixo para verificar se tudo está Ok:

```sh
docker ps
```

... se tudo ocorreu bem você terá uma saída parecida com a da imagem abaixo no seu console:

![img](https://douglaszuqueto.com/uploads/articles/artigo-12/docker-ps.png)

Caso queira mais informações basta acessa a documentação: [link](https://docs.docker.com/engine/installation/)

## Instalação do Docker-Compose

Esse processo é bem simples, somente 1 comando é necessário para instalação. Logo depois dê um *docker-compose -v* para ver se tudo ocorreu bem. Veja ambos comandos abaixo.

```sh
sudo pip install docker-compose
```

```sh
docker-compose -version

docker-compose version 1.9.0, build 2585387
```


Mais informações: [link](https://docs.docker.com/compose/install/)

## Construindo uma imagem Docker

Usar o Docker em uma arquitetura ARM difere um pouco das demais arquiteturas devido à este fator. Logo, existem poucas imagens que suportem esta arquitetura. 

Assim que instalei o docker e o configurei, peguei o código desta plataforma e tentei rodar, mas claro - foi sem sucesso. Tendo esse problema em mente, comecei a pesquisar algumas imagens de acordo com o ambiente que eu utilizo para ver qual medida tomar para que assim pudesse iniciar os testes. 

### Imagem Alpine

Achei uma imagem alpine para arquitetura arm [easypi/alpine-arm](https://hub.docker.com/r/easypi/alpine-arm), funcionou muito bem, logo criei a própria versão, com uma única diferença - adicionei o Timezone de São Paulo.

Veja no código abaixo como ficou o Dockerfile da imagem alpine.

```
FROM easypi/alpine-arm

MAINTAINER Douglas Zuqueto <douglas.zuqueto@gmail.com>

ENV TZ=America/Sao_Paulo

RUN apk update && \
    apk upgrade && \
    apk add --no-cache tzdata && echo 'America/Sao_Paulo' > /etc/timezone && \
    rm -rf /var/cache/apk/* && rm -rf /var/lib/apt/lists/*

```

### Imagem com servidor web

Está imagem será baseada em cima da imagem anterior, porém nesta imagem terá embarcado o Caddy - Servidor web desenvolvido em Go - semelhante ao nginx. Veja o código abaixo.

```
FROM douglaszuqueto/alpine-arm7

MAINTAINER Douglas Zuqueto <douglas.zuqueto@gmail.com>

COPY Caddyfile /root

RUN apk update && \
    apk upgrade && \
    apk add --no-cache curl  && \
    cd ~ && \
    curl -o caddy.tar.gz "https://caddyserver.com/download/build?os=linux&arch=arm&arm=7&features=" && \
    tar -zxvf caddy.tar.gz && \
    apk del curl && rm -rf /var/cache/apk/* && rm -rf /var/lib/apt/lists/* && rm -rf caddy.tar.gz

EXPOSE 80

CMD ["/root/caddy", "-port", "80", "-conf", "/root/Caddyfile"]

```

Essa imagem terá um arquivo de configuração para o servidor web, neste caso o arquivo se chama Caddyfile, veja o código do mesmo abaixo.

```
*:80 {

  root /app
  log stdout
  errors stderr

  gzip {
    level 1
  }
  
}
```

### Build e Push das imagens

Para cada imagem construída, será efetuado um build com base no Dockerfile de cada uma e posteriormente será enviada(push) para o Docker Hub. Antes de mais nada, ressalto que cada dockerfile está em uma pasta diferente de acordo com o nome da imagem, para ficar mais claro veja na imagem abaixo como ficou a estrutura de diretórios:

![img](https://douglaszuqueto.com/uploads/articles/artigo-12/tree.png)

#### Build 

* **Build 1:** docker build -t douglaszuqueto/alpine-arm7 .
* **Build 2:** docker build -t douglaszuqueto/caddy-arm7 .

**OBS:** Perceba que os comandos para realizar o build acima possui a diretiva *douglaszuqueto*, essa diretiva nada mais é do que meu usuário no docker hub.

#### Push

Para o envio das imagens ao Docker hub é fácil. Veja os comandos abaixo.

* ** Push 1:** docker push douglaszuqueto/alpine-arm7:latest
* ** Push 2:** docker push douglaszuqueto/caddy-arm7:latest

**OBS:** Para o envio das imagens ao docker hub é necessário ter uma conta, pois no primeiro envio será pedido suas credenciais.

Todas imagens acima, estão no meu [docker hub](https://hub.docker.com/r/douglaszuqueto) e no [github](https://github.com/douglaszuqueto/rasp-docker-images).

## Validando o uso do Docker

### Testando o servidor web

Veja que no tópico anterior que foi mostrado a estrutura de diretórios, note que havia um **helloworld**, e dentro desta pasta, uma pasta chamada *app*, pois então, crie um simples index.html dentro da pasta *app*. O meu ficou como no código abaixo.

```html
<!DOCTYPE html>
<html>
<head>
<title>Docker RPI3</title>
</head>
<body>

<h1>Caddy Server running on Raspberry PI 3</h1>

</body>
</html>
```
O arquivo Caddyfile ficou o mesmo que da imagem anterior, porém o Dockerfile sofreu uma mudança, então veja o código abaixo do arquivo.

```
FROM douglaszuqueto/caddy-arm7

MAINTAINER Douglas Zuqueto <douglas.zuqueto@gmail.com>

COPY app/index.html /app/

EXPOSE 80

CMD ["/root/caddy", "-port", "80", "-conf", "/root/Caddyfile"]
```

Para rodar a imagem de hello world primeiramente precisa-se fazer o build da mesma como nos passos anteriores.

* **Build:** docker build -t douglaszuqueto/helloworld-arm7 .

Agora basta rodar o comando abaixo e acessar o ip de sua rasbperry pi que verá o resultado.

```
docker run -it --rm -p 80:80 douglaszuqueto/helloworld-arm7
```

Observe o resultado na imagem abaixo, tudo rodando lindamente na raspberry pi 3 :D

![img](https://douglaszuqueto.com/uploads/articles/artigo-12/print.png)

### Desempenho

Esse foi um teste bem simples. Antes do rodar o teste acima, o processamento da rasp estava quase nulo, e o consumo de memória em torno de 68mb de ram.

Logo que o container foi iniciado, o processamento permaneceu quase nulo e o consumo da memória aumentou em 6mb, totalizando 74mb.

### Teste de stress do container

Vamos ver se o docker se comporta realmente bem na raspberry? Para isso, irei fazer um simples teste de desempenho do servidor web usando o siege - trata-se de um utilitário que irá calcular quantas requisições por segundo a aplicação será capaz de aguentar.

Fazendo um teste com 100 clientes durante 20 segundos, teve uma média de 35 requisições/segundo.

## Finalizando

Sobre o uso dos recursos na rasp, veja um print do htop na hora do processo que foi mencionado no tópico anterior.
 
![img](https://douglaszuqueto.com/uploads/articles/artigo-12/htop.png)

Observa-se que o uso dos núcleos do processador ficou em torno de 60% durante o processo e a memória ram em média de 135~140mb. 

Gostei dos resultados, mas ainda irão melhorar pois o cartão sd é classe 4, então julgo que é um grande gargalo - mesmo tendo bons resultados. *Já efetuei a compra de um Sandisk 16gb classe 10, em breve refazerei os testes.*

Mais artigos como esse terão o Docker envolvido, pretendo fazer testes rodando Broker em container e até tentar embarcar uma aplicação python, nodejs ou C para o controle de GPIO.

## Materiais Recomendados

* [Livro - Docker para desenvolvedores - Rafael Gomes](https://leanpub.com/dockerparadesenvolvedores)
* [Vídeos - Docker na Prática - CodeCasts(necessário assinatura)](https://codecasts.com.br/series/docker-na-pratica)

## Referências

* [Docker](https://docker.com)
* [Imagem AlpineLinux for RaspberryPi](https://hub.docker.com/r/easypi/alpine-arm/)
