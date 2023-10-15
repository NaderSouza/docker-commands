# Comandos, ajuda e dicas do Docker - Traduzido (credits to [bradtraversy](https://gist.github.com/bradtraversy/89fad226dc058a41b596d586022a9bd3))

### Mostrar comandos e comandos de gerenciamento

```
$ docker
```

### Informações da versão do Docker

```
$ docker version
```

### Mostrar informações como número de contêineres, etc.

```
$ docker info
```

### Mostrar informações e opções dos sub comandos.

```
$ docker system --help
```


### Mostrar informações de quanto e o que esta sendo utilizado dos diretórios do Docker 

```
$ docker system df
```
### Mostrar informações do container 

```
$ docker ps -a  
```

### Mostrar informações das imagens

```
$ docker images    
```

### Remove um contêiner parado

```
$ docker rm <container_id> 
```

### Força a remoção de um contêiner em execução (usa SIGKILL)

```
$ docker rm -f <container_id>   
```

### Remove uma imagem - falhará se houver uma instância em execução dessa imagem, ou seja, um contêiner

```
$ docker rmi <image_id>    
```
### Força a remoção da imagem mesmo se ela for referenciada em vários repositórios, ou seja, o mesmo ID de imagem com vários nomes/tags - ainda falhará se houver um contêiner docker referenciando a imagem

```
$ docker rmi -f <image_id> 
```
 

### Mostrar mais informações do sistema dos diretórios

```
$ docker system df -v
```

### Mostra os eventos que ocorrem na máquina 

```
$ docker system events
```

### Mostra os eventos que ocorrem na máquina de uma data e hora específica

```
$ docker system events --since 2002-05-01T14:20 (data exemplo) 
```

### Remove todos os conteúdos do seu sistema
```
$ docker system prune
```

### Remove todos os conteúdos específico do seu sistema 
```
$ docker (image - network - volume) prune
```



<br>



# TRABALHANDO COM RECIPIENTES

### Crie e execute um contêiner em primeiro plano

```
$ docker container run -it -p 80:80 nginx
```

### Crie e execute um contêiner em segundo plano

```
$ docker container run -d -p 80:80 nginx
```

### Forma abreviada

```
$ docker container run -d -p 80:80 nginx
```

### Nomeando contêineres

```
$ docker container run -d -p 80:80 --name nginx-server nginx
```

### DICA: O QUE A EXECUÇÃO FEZ

- Procurei uma imagem chamada nginx no cache de imagens
- Se não for encontrado no cache, ele procura o repositório de imagens padrão no Dockerhub
- Puxado para baixo (versão mais recente), armazenado no cache de imagens
- Iniciou em um novo contêiner
- Especificamos pegar a porta 80- no host e encaminhar para a porta 80 no contêiner
- Poderíamos fazer "$ docker container run --publish 8000:80 --detach nginx" para usar a porta 8000
- Podemos especificar versões como "nginx:1.09"

### Listar contêineres em execução

```
$ docker container ls
```

OU

```
$ docker ps
```

### Lista todos os contêineres (mesmo que não estejam em execução)

```
$ docker container ls -a
```

### Parar contêiner

```
$ docker container stop [ID]
```

### Pare todos os contêineres em execução

```
$ docker stop $(docker ps -aq)
```

### Remover contêiner (não é possível remover contêineres em execução, deve parar primeiro)

```
$ docker container rm [ID]
```

### Para remover um contêiner em execução use force(-f)

```
$ docker container rm -f [ID]
```

### Remover vários contêineres

```
$ docker container rm [ID] [ID] [ID]
```

### Remova todos os contêineres

```
$ docker rm $(docker ps -aq)
```

### Obtenha registros (use nome ou ID)

```
$ docker container logs [NAME]
```

### Listar processos em execução no contêiner

```
$ docker container top [NAME]
```

#### DICA: SOBRE RECIPIENTES

Os contêineres Docker são frequentemente comparados a máquinas virtuais, mas na verdade são apenas processos em execução no sistema operacional host. No Windows/Mac, o Docker é executado em uma mini-VM, portanto, para ver os processos, você precisará se conectar diretamente a ela. No entanto, no Linux você pode executar "ps aux" e ver os processos diretamente

# COMANDOS DE IMAGEM

### Liste as imagens que extraímos

```
$ docker image ls
```

### Também podemos apenas baixar imagens

```
$ docker pull [IMAGE]
```

### Remover imagem

```
$ docker image rm [IMAGE]
```

### Remover todas as imagens

```
$ docker rmi $(docker images -a -q)
```

#### DICA: SOBRE IMAGENS

- Imagens são binários de aplicativos e dependências com metadados sobre os dados da imagem e como executá-la
- As imagens não são um sistema operacional completo. Sem kernel, módulos de kernel (drivers)
- Host fornece o kernel, grande diferença entre VM

### Alguns exemplos de criação de contêiner

NGINX:

```
$ docker container run -d -p 80:80 --name nginx nginx (-p 80:80 is optional as it runs on 80 by default)
```

APACHE:

```
$ docker container run -d -p 8080:80 --name apache httpd
```

MONGODB:

```
$ docker container run -d -p 27017:27017 --name mongo mongo
```

MySQL:

```
$ docker container run -d -p 3306:3306 --name mysql --env MYSQL_ROOT_PASSWORD=123456 mysql
```

## INFORMAÇÕES DO RECIPIENTE

### Ver informações sobre o contêiner

```
$ docker container inspect [NAME]
```

### Propriedade específica (--format)

```
$ docker container inspect --format '{{ .NetworkSettings.IPAddress }}' [NAME]
```

### Estatísticas de desempenho (cpu, mem, rede, disco, etc.)

```
$ docker container stats [NAME]
```

## ACESSAR RECIPIENTES

### Crie um novo contêiner nginx e faça bash nele

```
$ docker container run -it --name [NAME] nginx bash
```

- i = interativo Mantenha o STDIN aberto se não estiver anexado
- t = tty - Abrir prompt

**Para Git Bash, use "winpty"**

```
$ winpty docker container run -it --name [NAME] nginx bash
```

### Executar/criar contêiner Ubuntu

```
$ docker container run -it --name ubuntu ubuntu
```

**(sem bash porque o Ubuntu usa bash por padrão)**

### Você também pode fazer com que ao sair do contêiner não permaneça usando o sinalizador -rm

```
$ docker container run --rm -it --name [NAME] ubuntu
```

### Acesse um contêiner já criado, comece com -ai

```
$ docker container start -ai ubuntu
```

### Use exec para editar configurações, etc.

```
$ docker container exec -it mysql bash
```

### Alpine é uma distribuição Linux muito pequena, boa para docker

```
$ docker container run -it alpine sh
```

(use sh porque não inclui bash)
(alpine usa apk para seu gerenciador de pacotes - pode instalar o bash se quiser)

# REDE

### "bridge" ou "docker0" é a rede padrão

### Obter porta

```
$ docker container port [NAME]
```

### Listar redes

```
$ docker network ls
```

### Inspecionar rede

```
$ docker network inspect [NETWORK_NAME]
("bridge" is default)
```

### Criar rede

```
$ docker network create [NETWORK_NAME]
```

### Criar contêiner na rede

```
$ docker container run -d --name [NAME] --network [NETWORK_NAME] nginx
```

### Conecte o contêiner existente à rede

```
$ docker network connect [NETWORK_NAME] [CONTAINER_NAME]
```

### Desconectar contêiner da rede

```
$ docker network disconnect [NETWORK_NAME] [CONTAINER_NAME]
```

### Desconecte a rede do contêiner

```
$ docker network disconnect
```

# MARCAÇÃO DE IMAGEM E EMPURRAR PARA DOCKERHUB

# tags são rótulos que apontam para um ID de imagem

```
$ docker image ls
```

Você verá que cada imagem tem uma tag

### Remarcar imagem existente

```
$ docker image tag nginx btraversy/nginx
```

### Fazer upload para dockerhub

```
$ docker image push bradtraversy/nginx
```

### Se negado, faça

```
$ docker login
```

### Adicionar tag à nova imagem

```
$ docker image tag bradtraversy/nginx bradtraversy/nginx:testing
```

### PEÇAS DO DOCKERFILE

- FROM - O sistema operacional usado. Comum é alpine, debian, ubuntu
- ENV - Variáveis de ambiente
- RUN - Executa comandos/shell scripts, etc.
- EXPOSE - Portas para expor
- CMD - Comando final executado quando você inicia um novo contêiner a partir da imagem
- WORKDIR - Define o diretório de trabalho (também pode usar 'RUN cd /some/path')
- COPY # Copia arquivos do host para o contêiner

### Construa a imagem do dockerfile (reponame pode ser qualquer coisa)

### Do mesmo diretório do Dockerfile

```
$ docker image build -t [REPONAME] .
```

#### DICA: CACHE E PEDIDO

- Se você executar novamente a compilação, será rápido porque tudo estará armazenado em cache.
- Se você alterar uma linha e executar novamente, essa linha e tudo o que vem depois não serão armazenados em cache
- Mantenha as coisas que mais mudam na parte inferior do Dockerfile

#ESTENDENDO O DOCKERFILE

### Dockerfile personalizado para paqge html com nginx

```
FROM nginx:latest # Estende o nginx para que tudo incluído nessa imagem seja incluído aqui
WORKDIR /usr/share/nginx/html
COPIAR index.html index.html
```

### Construa imagem do Dockerfile

```
$ docker image build -t nginx-website
```

### Executando

```
$ docker container run -p 80:80 --rm nginx-website
```

### Marque e envie para Dockerhub

```
$ docker image tag nginx-website:latest btraversy/nginx-website:latest
```

```
$ docker image push bradtraversy/nginx-website
```

# VOLUMES

### Volume - Cria uma localização especial fora do contêiner UFS. Usado para bancos de dados

### Bind Mount -Link caminho do contêiner ao caminho do host

### Verifique os volumes

```
$ docker volume ls
```

### Limpar volumes não utilizados

```
$ docker volume prune
```

### Abra a imagem mysql para testar

```
$ docker pull mysql
```

### Inspecione e veja o volume

```
$ docker image inspect mysql
```

### Executar contêiner

```
$ docker container run -d --name mysql -e MYSQL_ALLOW_EMPTY_PASSWORD=True mysql
```

### Inspecione e veja o volume no contêiner

```
$ docker container inspect mysql
```

#### DICA: montagens

- Você também verá o volume nas montagens
- O contêiner obtém seu próprio local exclusivo no host para armazenar esses dados
- Fonte: xxx é onde ele fica no host

### Verifique os volumes

```
$ docker volume ls
```

**Não há como diferenciar os volumes, por exemplo, com 2 contêineres mysql, então usamos volumes nomeados**

### Volumes nomeados (comando Add -v)(o nome aqui é mysql-db que pode ser qualquer coisa)

```
$ docker container run -d --name mysql -e MYSQL_ALLOW_EMPTY_PASSWORD=True -v mysql-db:/var/lib/mysql mysql
```

### Inspecione o novo volume nomeado

```
$ docker volume inspect mysql-db
```

# LIGAR MONTAGENS

- Não é possível usar no Dockerfile, especificado em tempo de execução (também usa -v)
- ... execute -v /Users/brad/stuff:/path/container (mac/linux)
- ... execute -v //c/Users/brad/stuff:/path/container (windows)

**DICA: Em vez de digitar o caminho local, para o diretório de trabalho use $(pwd):/path/container - No Windows pode não funcionar, a menos que você esteja na pasta de usuários**

### Execute e seja capaz de editar o arquivo index.html (o diretório local deve ter o Dockerfile e o index.html)

```
$ docker container run  -p 80:80 -v $(pwd):/usr/share/nginx/html nginx
```

### Entre no contêiner e verifique

```
$ docker container exec -it nginx bash
$ cd /usr/share/nginx/html
$ ls -al
```

### Você pode criar um arquivo no contêiner e ele também existirá no host

```
$ touch test.txt
```

# DOCKER COMPOSIÇÃO

- Configurar relacionamentos entre contêineres
- Salve nossas configurações de execução do contêiner do docker em um arquivo fácil de ler
- 2 partes: arquivo YAML (docker.compose.yml) + ferramenta CLI (docker-compose)

### 1. docker.compose.yml - Descreve soluções para

- containers
- networks
- volumes

### 2. docker-compose CLI - usado para automação de desenvolvimento/teste local com arquivos YAML

### Exemplo de arquivo de composição (do curso de Bret Fishers)

```
versão 2'

# igual a
# docker run -p 80:4000 -v $(pwd):/site bretfisher/jekyll-serve

services:
  jekyll:
    image: bretfisher/jekyll-serve
    volumes:
      - .:/site
    ports:
      - '80:4000'
```

### Para correr

```
docker-compose up
```

### Você pode executar em segundo plano com

```
docker-compose up -d
```

###  Para Limpar

```
docker-compose down
```
