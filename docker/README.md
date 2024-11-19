# Full Cycle 3.0 - Docker

**Iniciano com Docker**

**docker ps** -> lista os containes que estão em execução;

**docker ps -a** -> lista os containes em execução incluíndo os containers parados;

**docker run hello** -> Inicia o container e na sequência o processo é descartado;

**docker run -i -t ubuntu:latest bash** -> Inicia um novo container com ubuntu indicando a versão "latest" e já entra em modo bash;

**docker run -i -t --rm ubuntu:latest bash** -> Inicia e remove o container;
