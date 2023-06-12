# Labs Eve-ng
Neste repositório está alocado todos os Labs que construí para testar e resolver problemas relacionados a rede e servidores.

### Realizando Upload das imagens para Eve-ng utilizando FTP Server
Em junho de 2023 tive alguns probleminhas para transferir as imagens dos roteadores para o Eve-ng, que estava rodando no meu linux, da forma tradicional, que seria utilizando o VNC. Então tive a ideia de utilizar um servidor FTP. Mas queria uma solução rápida e que não precisasse alterar e nem instalar pacotes no meu linux. Foi aí que tive a ideia de utilizar uma imagem docker, pois o docker já faz parte do meu sistema e da minha rotina.

Eu precisava de uma imagem docker que já criasse o usuário de acesso ao servidor e realizar o mapeamento do diretório onde estavam as imagens que baixei, nesse caso, o diretório **Downloads**. Foi aí que achei a imagem [**delfer/alpine-ftp-server**](https://hub.docker.com/r/delfer/alpine-ftp-server)

A execução é bem simples, crie um **docker-compose.yml** (pode executar direto na CLI), e insira o seguinte código:

```yml
version: '3.8'
services:
  ftp-server:
    image: delfer/alpine-ftp-server
    environment:
      USERS: "ftpuser|1234"
      ADDRESS: '172.17.0.1'
    ports:
      - "21:21"
      - "20:20"
      - "21000-21010:21000-21010"
    volumes:
      - ~/Downloads:/ftp/ftpuser
```

#### Atenção
É preciso alterar os valores das seguintes chaves:
- Variável **USERS**: Você pode alterar o usuário e senha que será utilizado para se conectar com o seu servidor FTP
- Variável **ADDRESS**: Aqui você ira colocar o IP da interface de rede do docker. Você pode obter utilizando o comando ```ifconfig docker0```.
- Volume: Aqui e onde ocorre a mágica. O Docker ira realizar um mapeamento da pasta remota ```/ftp/ftpuser/``` para a pasta local onde os arquivos de imagens do Eve-ng está alocado, neste caso ~/Downloads.

Agora, execute o comando
```shell
$ docker compose up
```

No shell do Eve-ng, iremos executar um curl para o FTP Server
