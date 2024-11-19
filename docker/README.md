# Full Cycle 3.0 - Docker

**Iniciano com Docker**

**docker ps** -> lista os containes que estão em execução;

**docker ps -a** -> lista os containes em execução incluíndo os containers parados;

**docker run hello** -> Inicia o container e na sequência o processo é descartado;

**docker run -i -t ubuntu:latest bash** -> Inicia um novo container com ubuntu indicando a versão "latest" e já entra em modo bash;

**docker run -i -t --rm ubuntu:latest bash** -> Inicia e remove o container;

# Executando e publicando portas com nginx

docker run nginx

**docker run -p 8080:80 nginx** -> Inicia o container dizendo que a porta 8080 vai direcionar na porta 80 do proceso;

**docker run -d -p 8080:80 nginx** -> Com o parâmetro -d, inicia o container mantendo o terminal livre e o container continua em execução;

# Removendo containers:

**docker rm 3d8eb6bb3bac** -> Remove um container pelo id;

**docker rm kind_hypatia** -> Remove um container pelo nome;

**docker rm kind_hypatia -f** -> Força a remoção de um container;

### Acessando e alterando arquivos em um container:

Utilização do comando exec;

**docker exec ngnix ls** -> Lista os arquivos do container ngnix;

É necessário rodar com modo interativo e com ttui para rodar os comando dentro do container.
**docker exec -it ngnix bash** -> Acessado via terminal o container do ngnix;
