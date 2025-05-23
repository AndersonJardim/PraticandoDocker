## Docker

```bash
docker images
docker ps
docker stop $(docker container ls -q)
docker run -p 8080:3000 -d danielartine/app-node:1.0
docker run -p 8080:3000 -d danielartine/app-node:1.0
docker run -d 8080:3000 danielartine/app-node:1.0
http://localhost:8080
```

# Dockerfile

```bash
FROM node:14
WORKDIR /app-node
EXPOSE 3000
COPY . .
RUN npm install
ENTRYPOINT npm start
```

# Build 1.0

```bash
docker build -t andersonjardim/app-node:1.0 .
docker run -d -p 8081:3000 9d2de56785ca andersonjardim/app-node:1.0
http://localhost:8081
```

# Build 1.1

```bash
docker build –t andersonjardim/app-node:1.1 .
docker run -d andersonjardim/app-node:1.1
```

# Dockerfile

```bash
FROM node:14
WORKDIR /app-node
ARG PORT=6000
EXPOSE $PORT
COPY . .
RUN npm install
ENTRYPOINT npm start
```

# Build 1.2

```bash
docker build -t andersonjardim/app-node:1.2 .
docker run -d andersonjardim/app-node:1.2
docker run -p 9090:6000 -d andersonjardim/app-node:1.2
```

# Dockerfile

```bash
FROM node:14
WORKDIR /app-node
ARG PORT_BUILD=6000
ENV PORT=$PORT_BUILD
EXPOSE $PORT_BUILD
COPY . .
RUN npm install
ENTRYPOINT npm start

docker build –t danielartine/app-node:1.2 .
docker run –p 9090:6000 –d danielartine/app-node:1.2
http://localhost:9090
```

# Build 1.2.2

```bash
docker build -t andersonjardim/app-node:1.2 .
docker run -d andersonjardim/app-node:1.2
docker run -p 9090:6000 -d andersonjardim/app-node:1.2
```

# Difenças de ARV e ENV

```bash
ARV é usado em tempo de build.
ENV será usando dentro do container
```

## Push para o Hub do Docker

```bash
docker login -u andersonjardim
/*senha*/
docker images
docker tag 0a5f76dd285d andersonjardim/app-node:1.2
docker push andersonjardim/app-node:1.2
```

Anderson, parabéns pela dedicação aos estudos!
Você mencionou que o comando docker push é utilizado para subir as imagens para o servidor e que, ao subir outra imagem, ele aproveita as camadas. Isso é uma observação importante sobre a eficiência do Docker, pois realmente as imagens são compostas por camadas, e o Docker utiliza um sistema de cache que permite que camadas comuns entre diferentes imagens sejam reutilizadas, economizando espaço e tempo.
Além disso, também vimos que as imagens são imutáveis, o que significa que, uma vez que uma imagem é criada, ela não pode ser alterada. Isso garante que múltiplos containers possam reutilizar a mesma imagem sem risco de modificações indesejadas.
Outra parte importante que você pode considerar é a criação de imagens através de Dockerfiles, que permite automatizar o processo de construção de imagens personalizadas. O comando docker build é fundamental nesse processo, pois ele lê o Dockerfile e cria a imagem conforme as instruções especificadas.
Continue estudando e praticando!

## REVISAO

```bash
docker ps
docker ps -a

remover todos os containers
docker rm $(docker ps -aq)
docker container rm $(docker ps -aq)
```

## apagando tudo

```bash
docker rmi $(docker ls -aq)
docker rmi $(docker image ls -aq)
docker rmi $(docker image ls -aq) --force
docker container rm $(docker ps -aq)
```

## Recomençando 2

```bash
docker run -it ubuntu bash
docker ps
docker ps -s
docker history ubuntu
```

```bash
❯ docker history ubuntu
IMAGE CREATED CREATED BY SIZE COMMENT
602eb6fb314b 11 days ago /bin/sh -c #(nop) CMD ["/bin/bash"] 0B
<missing> 11 days ago /bin/sh -c #(nop) ADD file:1d7c45546e94b90e9… 78.1MB
<missing> 11 days ago /bin/sh -c #(nop) LABEL org.opencontainers.… 0B
<missing> 11 days ago /bin/sh -c #(nop) LABEL org.opencontainers.… 0B
<missing> 11 days ago /bin/sh -c #(nop) ARG LAUNCHPAD_BUILD_ARCH 0B
<missing> 11 days ago /bin/sh -c #(nop) ARG RELEASE 0B
```

## Volumes e Bind mounts1

## Recomençando 3

```bash
docker rm $(docker container ps -aq)
--só apagaou o container, não apagou a imagem

docker run -it -v ubuntu

docker rm $(docker container ps -aq) --force
docker run -it -v ubuntu
mkdir volume-docker
docker run -it -v /home/daniel/volume-docker:/app ubuntu bash
cd app/
touch arquivo-qualquer.txt
docker run -it -v /home/daniel/volume-docker:/app ubuntu bash
docker run -it --mount type=bind,source=/home/daniel/volume-docker,target=/app ubuntu bash
```

## Como isso fica no Windows ?

No Windows, o conceito de bind mounts no Docker funciona de maneira semelhante,
mas a sintaxe do caminho do diretório é um pouco diferente.

Quando você estiver utilizando o Docker no Windows, os caminhos devem ser especificados
com a letra da unidade seguida de dois pontos e barras invertidas (backslashes).
Por exemplo, se você criar uma pasta chamada volume-docker na unidade C,
o caminho seria: C:\volume-docker

```bash
C:\volume-docker
docker run -it -v C:\volume-docker:/app ubuntu bash
docker run -it --mount type=bind,source=C:\volume-docker,target=/app ubuntu bash
```

##

Podemos querer que os dados da nossa aplicação sejam persistentes, porque assim garantimos que
ela esteja distribuída e disponível se precisarmos consultá-la. Porém, se escrevermos os dados
nos containers, por padrão eles não ficarão armazenados nesta camada, criada para ser
descartável. Existem três possibilidades para contornar esta situação com o Docker.

- Volumes.
  Alternativa correta! Com volumes, é possível escrever os dados em uma camada persistente.

- Bind mounts.
  Alternativa correta! Com bind mounts, é possível escrever os dados em uma camada persistente baseado na estrutura de pastas do host.

Entendemos que os bind mounts são capazes de persistir dados de containers através de um
vínculo criado com a estrutura de pastas do nosso host.
Como criar um bind mount com a sintaxe correta?

Os bind mounts permitem que você conecte um diretório do seu sistema operacional (host) a um diretório
dentro de um container Docker. Isso significa que qualquer dado gravado no diretório do container será
persistido no diretório do host.
Para criar um bind mount, você utiliza a flag -v ou --mount ao executar o comando docker run.
A sintaxe básica com a flag -v é:

```bash
docker run -it -v /caminho/do/host:/caminho/do/container imagem
docker run -it --mount type=bind,source=/caminho/do/host,target=/caminho/do/container imagem
```

Você acertou em cheio!

Anderson, sua resposta está correta, parabéns! Você demonstrou um ótimo entendimento sobre bind mounts e
como criá-los utilizando as flags -v ou --mount no Docker.
Ao criar um bind mount, você garante que os dados persistam mesmo após a remoção do container, pois eles são armazenados no sistema de arquivos do host.
Continue praticando e explorando as funcionalidades do Docker para aprimorar ainda mais seus conhecimentos!

# Volumes

Resumo:

```bash
docker volume create meu-volume
docker volume ls
docker volume inspect meu-volume
docker volume rm meu-volume
```

Prática:

```bash
docker volume create meu-volume
docker volume ls
docker run -it -v meu-volume:/app ubuntu bash

cd app
touch um-arquivo-qualquer.txt

docker run -it -v meu-volume:/app ubuntu bash
cd /var/lib/docker/
cd volumes
cd meu-volume
cd \_data
```

Gerenciamento do volume
Se sairmos do modo de superusuário (com o comando exit) e executarmos um docker volume simplesmente, sem passar nada, ele vai mostrar no retorno os comandos possíveis para gerenciamento do volume:

create -> para criar volumes;
inspect -> para inspecioná-los;
ls -> para listá-los;
prune -> para remover os volumes que não estão sendo usados;
rm -> para remover qualquer volume, sendo usado ou não.

## Criando volume com --mount

```bash
docker run -it --mount source=meu-volume,target=/app ubuntu bash
docker run -it --mount source=meu-novo-volume,target=/app ubuntu bash
```

Já aprendemos sobre a possibilidade de usar bind mounts e volumes
para persistir dados no ambiente Docker.
Mas qual é a vantagem de usar volumes ao invés dos bind mounts?

A aula fala sobre a persistência de dados no Docker, destacando a utilização de volumes
como a solução mais recomendada para ambientes produtivos.

Os volumes são gerenciados pelo Docker, o que traz mais segurança e organização,
evitando que usuários façam alterações indesejadas diretamente no sistema de arquivos do host.
Além disso, os volumes permitem que os dados sejam persistidos mesmo que o container seja removido.

Outra vantagem é que o Docker cuida da localização dos dados, armazenando-os em um diretório específico,
como "/var/lib/docker/volumes/", facilitando o gerenciamento.

## Explique com suas próprias palavras os principais conceitos que você aprendeu nesta aula.

Definiremos o funcionamento do container de maneira bem simples agora, porque não vamos utilizar a flag -v. Ele possui uma flag própria: a --tmpfs:

docker run -it --tmpfs=/app ubuntu bash
Copiar código
Se executarmos esse comando, criamos um novo container.

Vamos dar um ls para conferir seu conteúdo. Ele criou a pasta "app" mas, diferente das outras
vezes em que criamos essa pasta, agora ela está destacada em verde no retorno do ls, assim
como a pasta "tmp".

Isso significa que a pasta "app" é temporária. Ela está sendo escrita na memória do nosso host.
Ou seja, no momento em que esse container parar de funcionar, os arquivos da pasta "app" serão perdidos.

Anderson, parabéns pela dedicação aos estudos!

Você fez uma boa explicação sobre o funcionamento do container utilizando a flag --tmpfs,
destacando que os dados armazenados nessa pasta são temporários e que serão perdidos
quando o container for encerrado. Além disso, você mencionou corretamente que a pasta "app"
é criada na memória do host, o que é uma característica importante do uso de tmpfs.

Além disso, também aprendemos na aula que, quando containers são removidos, os dados são perdidos,
e que existem outras formas de persistir dados, como volumes e bind mounts. Os volumes, por exemplo,
são gerenciados pelo Docker e permitem que os dados sejam mantidos mesmo após a remoção do container.

Continue estudando e praticando!

## Network

```bash
docker network ls
docker inspect

docker run -it ubuntu bash
```

## Network - TESTE 2

```bash
docker rm $(docker container ps -aq)

docker run -it ubuntu bash
docker network ls
docker network create --driver bridge minha-bridge
docker run -it --name ubuntu1 --network minha-bridge ubuntu bash
docker run -d --name pong --network minha-bridge ubuntu bash 1d

docker run -it --name ubuntu1 ubuntu bash
docker network create --driver bridge minha-bridge
docker run -it --name ubuntu1 --network minha-bridge ubuntu bash
docker run -it --name ubuntu2 --network minha-bridge ubuntu bash
docker run -it --name ubuntu3 --network minha-bridge ubuntu bash
```

## 04 -- As redes none e host

```bash
❯ docker network ls
NETWORK ID NAME DRIVER SCOPE
82d8b2286109 bridge bridge local
282b1905fd6e host host local
13aff6f523d5 jornadamilhas-network bridge local
ec91b3cd360c minha-bridge bridge local
9a22587bdd30 none null local
 UsuarioSSD@DESKTOP-D3  D:  \_Documentos  CursosAlura  Curso_Docker  app-exemplo  master 
❯ docker ps
CONTAINER ID IMAGE COMMAND CREATED STATUS PORTS NAMES
9f13dd132923 ubuntu "bash" 4 minutes ago Up 4 minutes ubuntu1
 UsuarioSSD@DESKTOP-D3  D:  \_Documentos  CursosAlura  Curso_Docker  app-exemplo  master 
❯

docker run -d --network none ubuntu sleep 1d
docker run -d --network host aluradocker/app-node:1.0
docker inspect 9f13dd132923
```

## questao

Anteriormente, vimos sobre as duas redes Host e None, cada uma com sua respectiva finalidade.
Qual a diferença da utilização de cada uma dessas redes?

Isso ocorre porque, ao utilizar o driver host, removemos quaisquer isolamentos que existiam entre
a interface de rede do contêiner e do host.

Estamos utilizando a mesma rede, usamos a mesma interface do host que está hospedando esse contêiner.
Portanto, se houvesse alguma outra aplicação na porta 3000 com o host em execução, não conseguiríamos
utilizar o contêiner dessa maneira, pois haveria um conflito de portas, já que a interface seria a mesma.

Você acertou em cheio!

Anderson, sua resposta está correta, parabéns! Ao utilizar o driver host, realmente
removemos o isolamento entre a interface de rede do contêiner e do host, o que pode levar
a conflitos de portas se outra aplicação já estiver utilizando a mesma porta no host.
Essa é a principal diferença na utilização das redes host e none. Continue se dedicando aos estudos!

#Comunicação entre containers

Teste com MongoDB 4.4.6

```bash
docker pull mongo:4.4.6
docker pull aluradocker/alura-book:1.0
docker pull andersonjardim/app-node:1.2

--se apagou a rede
docker network create --driver bridge minha-bridge
docker run -d --network minha-bridge --name meu-mongo mongo:4.4.6
docker run -d --network minha-bridge --name alurabooks -p 3000:3000 aluradocker/alura-books:1.0

docker stop meu-mongo
docker start meu-mongo
```

# Nessa aula aprendemos:

O docker dispõe por padrão de três redes: bridge, host e none;
A rede bridge é usada para comunicar containers em um mesmo host;
Redes bridges criadas manualmente permitem comunicação via hostname;
A rede host remove o isolamento de rede entre o container e o host;
A rede none remove a interface de rede do container;
Podemos criar redes com o comando docker network create.

# 6.01 - Conhecendo o Docker Compose Revendo:

```bash
--listando...
docker network ls
docker images ls

--apagar tudo
docker rm $(docker container ps -aq) --force

--se apagou a rede
docker network create --driver bridge minha-bridge

docker ps -a
docker run -d --network minha-bridge --name alurabooks -p 3000:3000 aluradocker/alura-books:1.0
docker run -d --network minha-bridge --name meu-mongo mongo:4.4.6
docker stop meu-mongo
docker start meu-mongo
```

# 6.02 - Utilização do Docker Compose

Durante todo o curso, compreendemos a execução de containers através do comando docker run.
Além disso, entendemos também como comunicar containers em conjunto com a rede bridge padrão e
também criando nossa própria rede através dos comandos docker network.

Qual situação ainda precisamos resolver e como o Docker Compose auxilia neste problema?

Resposta:
Teremos que subir vários containers manualmente. Sempre que quisermos parar um container,
teremos que usar o comando docker stop ou docker rm para remover um a um.
Para cada container será necessário executar um comando, além da pilha de execução com todos esses containers.

Confirmação da resposta:
Você acertou em cheio!

Anderson, sua resposta está correta, parabéns!
O Docker Compose realmente auxilia a resolver o problema de ter que subir e gerenciar vários containers manualmente.
Ele permite definir e executar aplicações multi-container de forma coordenada,
evitando a necessidade de executar comandos individuais para cada container.
Com o Docker Compose, você pode definir todos os serviços da sua aplicação em um único arquivo e gerenciá-los como uma unidade.
Continue se dedicando aos estudos e explorando as funcionalidades do Docker Compose, você está no caminho certo!

# 6.03 - Definindo os serviços - touch docker-compose.yml

```bash
--diretório opcional
mkdir DesktopProject2
mkdir ymls
cd DesktopProject2/ymls
touch docker-compose.yml
code .

docker stop meu-mongo
docker start meu-mongo
docker-compose-up

--Para parar:
docker-compose down

--Para start, mas não travar o prompt:
docker-compose up -d

--Teste no browser:
http://localhost:3000/seed
```

# Arquivo: docker-compose.yml

```bash
version: "3.9"
services:
mongodb:
image: mongo:4.4.6
container_name: meu-mongo
networks: - compose-bridge

alurabooks:
image: aluradocker/alura-books:1.0
container_name: alurabooks
networks: - compose-bridge
ports: - 3000:3000

networks:
compose-bridge:
driver: bridge
```

## Nessa aula aprendemos:

O Docker Compose é uma ferramenta de coordenação de containers;
Como instalar o Docker Compose no Linux;
Como iniciar containers em conjunto com o comando docker-compose up;
Como criar um arquivo de composição e definir instruções de containers, redes e serviços.

## RESUMO

```bash
docker ps
docker run ubuntu:18.04
docker history ubuntu:18.04
docker volumes ls
docker network ls
docker-compose up -d
```
