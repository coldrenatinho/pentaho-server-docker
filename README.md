# **Pentaho server - Etapas implantação usando docker:**

1. Enviar para o servidor o pacote
```git clone https://github.com/coldrenatinho/pentaho-server-docker/tree/master```

3. Criar imagem (build) do pentaho server 9 baseado no dockerfile 
```docker-compose build```

   Caso o servidor não tenha acesso a internet, não será possível criar a imagem, então realizar etapas adicionais antes de rodar:
   - Gerar imagem em máquina que possui acesso a internet e realizar a exportação da imagem 
	```docker save``` para um arquivo .tar
   - Carregar arquivo no servidor 
	```docker load```

4. Rodar imagem para criar o container e inicializar 
```docker-compose up```

5. Fazer upload do zip de jobs/transformações no pentaho server web (http://ip_servidor:8081/pentaho/Login)
> Usuário padrão: admin
> Senha: password

7. Ajustar as configurações de conexão de banco e controle de usuário

8. Configurar schedule para execução do JOB

**Pronto!**

_**Obs. também é possível:**_ 
 - Realizar Commit do container para gerar snapshot.
 - Armazenar a imagem em repositório docker-hub ou nexus, usando o conceito de registrar a imagem.

## Conjunto de comandos docker / compose

### build (cria imagem)
```docker build -t coldrenatinho/pentaho_server:9.4 .```

**_Usando compose:_** 
```docker-compose build```

### Verificar imagens/container existentes
```docker images```
```docker ps```
```docker inspect```
```docker logs --follow (container_id)```

### Verificar logs do Pentaho
```docker logs -f pentaho-server```

<!--TOFIX REMOVER-->
### run primeira vez (cria container)
```docker run -p 127.0.0.1:8081:8080 coldrenhatinho/pentaho_server:9.4```

**_Usando compose:_** 
```docker-compose up```

### Navega pelos arquivos do container
```docker exec -t -i pentaho-server /bin/bash```

### Rodar container existente
```docker container start pentaho-server```

**_Usando compose:_** 
```docker-compose up```

### Parar container existente
```docker container stop pentaho-server```

**_Usando compose:_** 
```docker-compose stop```

### Customizar container com confs e depois gerar uma imagem (snapshot)
```docker commit (container_id)  lpaschoal/pentaho_server_snapshot:2.0```

## Link's
(Drivers do MySQL)[https://dev.mysql.com/downloads/file/?id=546177]
(Download Pentaho Legacy Version)[https://github.com/ambientelivre/legacy-pentaho-ce?tab=readme-ov-file]

## Baixando o driver mysql
O driver mais atual pode não fucnionar devidor a imcompatibiidade do Java 8
> O drivers devem ser alocados na pasta libs, driver compativeis com o Java 8 JDBC

O comando a seguir realizar o Donwload do Driver compativel
```wget https://downloads.mysql.com/archives/get/p/3/file/mysql-connector-j-8.0.33.zip```

## Desconpacte 
```unzip mysql-connector-j-8.0.33.zip```

## Mova o driver
Procure por um arquivo com o final Jar e mova o mesmo para o diretório de libs na root do reposítório

## Remova o lixo
```rm -rf mysql-connector-j-8.0.33```
```rm mysql-connector-j-8.0.33.zip```


