# Full Cycle 3.0 - Docker

# Iniciando com Docker

Listar os containes que estão em execução;
```bash
docker ps
```
Listar os containes em execução incluíndo os containers parados;
```bash
docker ps -a
```

Iniciar um container e na sequência descartar o processo;
```bash
docker run hello
```

Iniciar um novo container com ubuntu indicando a versão **"latest"** e já entrar em modo bash;
```bash
docker run -i -t ubuntu:latest bash
```

Iniciar e remover o container;
```bash
docker run -i -t --rm ubuntu:latest bash
```

# Executar e publicar portas com nginx
Inicialização padrão
```bash
docker run nginx
```

Iniciar o container dizendo que a porta 8080 vai direcionar na porta 80 do proceso;
```bash
docker run -p 8080:80 nginx
```

Usar o parâmetro **-d** para iniciar o container mantendo o terminal livre e manter o container em execução;
```bash
docker run -d -p 8080:80 nginx
```

# Removendo containers:

Remover um container pelo id;
```bash
docker rm 3d8eb6bb3bac
```

Remover um container pelo nome;
```bash
docker rm kind_hypatia
```

Forçar a remoção de um container;
```bash
docker rm kind_hypatia -f
```

### Acessando e alterando arquivos em um container:

Utilização do comando exec;

Listar os arquivos do container ngnix;
```bash
docker exec ngnix ls
```

É necessário rodar com modo interativo e com ttui para rodar os comando dentro do container.
Acessar via terminal o container do ngnix;
```bash
docker exec -it ngnix bash
```

# Alterar arquivo index.html do ingnix

Acessar o diretório;
```bash
cd /usr/share/nginx/html/
```

**Importante:** Tudo o que é alterado dentro de um container, depois que remover o container e subir ele novamente, o que foi feito anteriormente será perdido.

# Iniciando o container com bind mounts
### Exemplos
```bash
docker run -d --name nginx -p 8080:80 -v /Users/rodrigodittrich/Projetos/fullcycle3/docker/html/:/usr/share/nginx/html nginx
```
```bash
docker run -d --name nginx -p 8080:80 --mount type=bind,source="$(pwd)"/html,target=/usr/share/nginx/html nginx
```

# Trabalhando com volumes
Comandos com **"docker volume"**

Listar os volumes criados;
```bash
docker volume ls
```

Criar um novo volume com o nome **"meuvolume"**;
```bash
docker volume create meuvolume
```

Mostrar os dados do volume;
```bash
docker volume inspect meuvolume
```

### Executar um container em cima do volume criado:
```bash
docker run -d --name nginx -d --mount type=volume,source=meuvolume,target=/app docker exec -it nginx bash
```

**Testar a criação de um arquivo:** Acessar diretório app, criar arquivo (touch teste) e listar arquivo (ls);

```bash
docker run -d --name nginx2 -d -v meuvolume:/app nginx*
```

Matar tudo o que não é utilizado de volumes;
```bash
docker volume prune
```

# Trabalhando com imagens
**Docker hub:** https://hub.docker.com/

Listar todas as imagens existentes;
```bash
docker images
```

Remover uma imagem;
```bash
docker rmi php:latest
```

# Criando a primeira imagem com Dockerfile

O Dockerfile é um dos arquivos principais onde queremos chegar (receita do bolo). Com o arquivo docker file, é possível partir de uma imagem e incluir outras dependências.

### Criar minha primeira imagem e salvar no meu docker Hub

```bash
docker build -t rodrigodittrich/nginx-com-vim:latest .
```

**-t** -> Representa a minha tag na minha conta no docker hub;  
**.** -> Representa em qual pasta existe meu Dockerfile;

## Uma dica bem prática do Wesley willians
Se eu executar o comando **docker ps -a -q**, com a inclusão do parâmetro **-q** vai listar para mim todos os ids dos containers ativos e inativos.  
Com isso, eu posso executar o comando **docker rm $(docker ps -a -q) -f** que vai remover todos os containers ativos e inativos;

# Entrypoint x CMD
```dockerfile
FROM ubuntu:latest

ENTRYPOINT ["echo", "Hello "]

CMD [ "World"]
```

Quando for executado o comando **docker run --rm rodrigodittrich/hello** a mensagem de saída será **"Hello world"**;  
Quando for executado o comando **docker run --rm rodrigodittrich/hello Rodrigo** a mensagem de saída será **"Hello Rodrigo"**;  

O EntryPoint é sempre um comando fixo, e o CMD é um comando variável que entra como parâmetro com o Entrypoint;

# Publicando imagem no dockerHub
Para publicar uma imagem no docker hub, será necessário logar pelo terminal (docker login) e entrar com as credenciais da sua conta no docker hub.  

### Publicando uma imagem no docker hub baseado no nginx:
Dockerfile:
```dockerfile
FROM nginx:latest

COPY html /usr/share/nginx/html

ENTRYPOINT ["/docker-entrypoint.sh"]

CMD ["nginx", "-g", "daemon off;"]
```

Publicar imagem:
```bash
docker push rodrigodittrich/nginx-fullcycle
```

# Network

## tipos de network:  
O formato bridge é o formato mais comum utilizado.  

* **bridge:** formato onde dois containers se comunicam entre si;  
* **host:** Neste formato, o container e a máquina rodam na mesma rede. Não é necessário fazer o direcionamento de portas;  
* **overlay:**  
* **maclan:**  
* **none:** Não tem nenhum rede no container;

## Bridge
Utilização dos comandos **docker network**

Listar as redes disponíveis:  
```bash
docker network ls
```

Remover todas as redes não utilizadas:  
```bash
docker network prune
```

Verificar os detalhes da rede dos containers ativos
```bash
docker network inspect bridge
```

**Resolver a comunicação pelo nome do container**

Criar uma nova rede:  
```bash
docker network create --driver bridge minharede
```

Executar o container **ubuntu1** na rede criada:  
```bash
docker run -dit --name ubuntu1 --network minharede bash
```

Executar o container **ubuntu2** na rede criada:  
```bash
docker run -dit --name ubuntu2 --network minharede bash
```

Acessar o container **ubuntu1** para fazer o teste de ping:  
```bash
docker exec -it ubuntu1 bash
ping container2
```

Conectar um container que foi criado sem definir a rede:
```bash
network connect minharede ubuntu3
docker exec -it ubuntu3 bash
ping container2
```

## Host
Docker foi feito para rodar no Linux, então este tipo de rede não funciona muito bem no Mac utilizando o Docker Desktop. Utilizando o OrbStack funciona.
No Windows funciona quando tem instalado o WSL.

## Acessar o container da máquina e vice-versa
Suponhamos que temos um serviço executando na máquina local na porta 8000 e nosso docker container precisa por algum motivo acessar esta aplicação pelo container.  

**Exemplo**  
Criar um container de exemplo:  
```bash
docker run --rm -it --name ubuntu ubuntu bash
curl http://host.docker.internal:8000
```

### Instalando um framework em um container

run -it --name php php:7.4-cli bash
apt-get update
.....

## Instalar um framework em um container
vamos criar um container com o framework lavavel

**Criar o arquivo Dockerfile com todos os passos**

**Criar a imagem**
```
docker build -t rodrigodittrich/laravel:latest .
```

**Criar um container com a imagem laravel**
```
docker run -d --rm --name laravel -p 8000:8000 rodrigodittrich/laravel
```

**Verificar os logs do laravel**
```
docker logs laravel
```

**Criar um container com a porta 8001 substituindo o que tem no CMD padrão**  
Por padrão, o laravel vai subir na porta 8000 liberando o acesso para qualquer host. Como temos em nosso Dockerfile o CMD com este comando padrão, ao criar o container podemos substituir o comando padrão liberando um host específico e uma porta específica.
```
docker run --rm -d --name laravel -p 8001:8001 rodrigodittrich/laravel --host=0.0.0.0 --port=8001
```

**Publicar a imagem para o Docker hub**
```
docker push rodrigodittrich/laravel
```

## Criar uma aplicação Node.js sem o Node
Vamos executar o container no diretório do node na porta 3000

**Executar o container:**  
```
docker run --rm -it -v $(pwd)/:/usr/src/app -p 3000:3000 node:15 bash
```

**Acessar o diretório da aplicação, executar o init e install do node:**  
```shell
cd usr/src/app/
```
```
npm init
```
```
npm install express --save
```