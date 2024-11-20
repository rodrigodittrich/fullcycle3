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
Comandos com "docker volume"

**docker volume ls** -> Lista os volumes criados;

**docker volume create meuvolume** -> Cria um novo volume com o nome "meuvolume";

**docker volume inspect meuvolume** -> Mostra os dados do volume;

### Executar um container em cima do volume criado:
**docker run -d --name nginx -d --mount type=volume,source=meuvolume,target=/app docker exec -it nginx bash**   
**Testar a criação de um arquivo:** Acessar diretório app, criar arquivo (touch teste) e listar arquivo (ls);

**docker run -d --name nginx2 -d -v meuvolume:/app nginx**

**docker volume prune** -> Mata tudo o que não é utilizado de volumes;

# Trabalhando com imagens
**Docker hub:** https://hub.docker.com/

**docker images** -> Lista todas as imagens existentes;
**docker rmi php:latest** -> Remove uma imagem;

# Criando a primeira imagem com Dockerfile

O Dockerfile é um dos arquivos principais onde queremos chegar (receita do bolo). Com o arquivo docker file, é possível partir de uma imagem e incluir outras dependências.

### Criar minha primeira imagem e salvar no meu docker Hub

**docker build -t rodrigodittrich/nginx-com-vim:latest .**

**-t** -> Representa a minha tag na minha contya no docker hub;  
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
