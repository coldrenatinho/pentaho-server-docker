# Pentaho Server Docker

> Implementação dockerizada do Pentaho Server 9.4 para facilitar o deployment e gerenciamento de ETL/BI.

[![Docker](https://img.shields.io/badge/Docker-20.10+-blue.svg)](https://www.docker.com/)
[![Pentaho](https://img.shields.io/badge/Pentaho-9.4-orange.svg)](https://www.hitachivantara.com/en-us/products/pentaho-platform.html)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

## 📋 Índice

- [Sobre o Projeto](#sobre-o-projeto)
- [Pré-requisitos](#pré-requisitos)
- [Instalação](#instalação)
- [Configuração](#configuração)
- [Uso](#uso)
- [Comandos Docker](#comandos-docker)
- [Configuração MySQL](#configuração-mysql)
- [Links Úteis](#links-úteis)

## 🎯 Sobre o Projeto

Este projeto fornece uma implementação containerizada do Pentaho Server usando Docker e Docker Compose, facilitando o deployment, gerenciamento e versionamento de ambientes ETL/BI.

### Características

- ✅ Pentaho Server 9.4
- ✅ Suporte a MySQL com drivers compatíveis (Java 8)
- ✅ Persistência de dados via volumes Docker
- ✅ Configuração simplificada via Docker Compose
- ✅ Suporte a ambientes offline

## 📦 Pré-requisitos

- Docker 20.10+
- Docker Compose 1.29+
- 4GB RAM mínimo
- 10GB espaço em disco

## 🚀 Instalação

### 1. Clonar o repositório

```bash
git clone https://github.com/coldrenatinho/pentaho-server-docker.git
cd pentaho-server-docker
```

### 2. Instalar driver MySQL (opcional)

O projeto já inclui suporte ao MySQL. Para atualizar o driver:

```bash
# Download do driver compatível com Java 8
wget https://downloads.mysql.com/archives/get/p/3/file/mysql-connector-j-8.0.33.zip

# Descompactar
unzip mysql-connector-j-8.0.33.zip

# Mover o driver .jar para a pasta lib
mv mysql-connector-j-8.0.33/mysql-connector-j-8.0.33.jar lib/

# Limpar arquivos temporários
rm -rf mysql-connector-j-8.0.33 mysql-connector-j-8.0.33.zip
```

### 3. Build da imagem

```bash
docker-compose build
```

### 4. Iniciar o container

```bash
docker-compose up -d
```

O servidor estará disponível em: **http://localhost:8081/pentaho/Login**

**Credenciais padrão:**
- Usuário: `admin`
- Senha: `password`

## 🔧 Configuração

### Deployment em Ambiente sem Internet

Se o servidor não possui acesso à internet:

```bash
# 1. Em uma máquina com internet, gere a imagem
docker-compose build

# 2. Exporte a imagem para arquivo
docker save -o pentaho-server.tar coldrenatinho/pentaho_server:9.4

# 3. Transfira o arquivo para o servidor de destino

# 4. No servidor de destino, carregue a imagem
docker load -i pentaho-server.tar

# 5. Inicie o container
docker-compose up -d
```

### Upload de Jobs/Transformações

1. Acesse a interface web
2. Faça login com as credenciais padrão
3. Navegue até **File → Import**
4. Selecione o arquivo ZIP com jobs/transformações
5. Configure os parâmetros de execução

### Configuração de Agendamento

Configure schedules diretamente na interface web do Pentaho em **Tools → Schedules**.

## 🐳 Comandos Docker

### Gerenciamento Básico

```bash
# Iniciar containers
docker-compose up -d

# Parar containers
docker-compose stop

# Reiniciar containers
docker-compose restart

# Ver logs em tempo real
docker-compose logs -f

# Ver logs do Pentaho
docker logs -f pentaho-server

# Acessar bash do container
docker exec -it pentaho-server /bin/bash
```

### Build Manual

```bash
# Construir imagem
docker build -t coldrenatinho/pentaho_server:9.4 .

# Rodar container manualmente
docker run -d \
  --name pentaho-server \
  -p 8081:8080 \
  -v $(pwd)/data:/biserver-ce/pentaho-server/pentaho-solutions \
  coldrenatinho/pentaho_server:9.4
```

### Inspeção e Debug

```bash
# Listar imagens
docker images

# Listar containers
docker ps -a

# Inspecionar container
docker inspect pentaho-server

# Ver uso de recursos
docker stats pentaho-server
```

### Criação de Snapshots

Para salvar configurações customizadas:

```bash
# Commit do container
docker commit pentaho-server coldrenatinho/pentaho_server_snapshot:2.0

# Exportar snapshot
docker save -o pentaho-snapshot.tar coldrenatinho/pentaho_server_snapshot:2.0

# Push para registry (opcional)
docker tag coldrenatinho/pentaho_server_snapshot:2.0 registry.exemplo.com/pentaho:2.0
docker push registry.exemplo.com/pentaho:2.0
```

## 🔌 Configuração MySQL

### Criando Conexão no Pentaho

1. No Pentaho, crie uma nova conexão de banco de dados
2. **Database Type**: Selecione "Generic database"
3. Preencha os campos:

| Campo | Valor |
|-------|-------|
| **Connection Name** | Nome da sua conexão |
| **Connection URL** | `jdbc:mysql://[host]:[porta]/[database]` |
| **Driver Class Name** | `com.mysql.cj.jdbc.Driver` |
| **User Name** | Seu usuário MySQL |
| **Password** | Sua senha MySQL |

4. Clique em **Test** para validar
5. Deve aparecer "Connection Successful"

### Exemplo de URL de Conexão

```
jdbc:mysql://mysql-server:3306/pentaho_db?useSSL=false&serverTimezone=UTC
```

### Troubleshooting MySQL

**Erro de timezone:**
```
Adicione à URL: ?serverTimezone=UTC
```

**Erro de SSL:**
```
Adicione à URL: ?useSSL=false
```

**Driver não encontrado:**
```bash
# Verifique se o driver está na pasta lib
ls -la lib/mysql-connector-j-*.jar

# Reconstrua a imagem
docker-compose build --no-cache
```

## 📚 Links Úteis

- [Pentaho Legacy Downloads](https://github.com/ambientelivre/legacy-pentaho-ce)
- [MySQL JDBC Drivers](https://dev.mysql.com/downloads/connector/j/)
- [Documentação Pentaho](https://help.hitachivantara.com/Documentation/Pentaho)
- [Docker Hub](https://hub.docker.com/)

## 🤝 Contribuindo

Contribuições são bem-vindas! Sinta-se à vontade para:

1. Fazer fork do projeto
2. Criar uma branch para sua feature (`git checkout -b feature/MinhaFeature`)
3. Commit suas mudanças (`git commit -m 'Adiciona MinhaFeature'`)
4. Push para a branch (`git push origin feature/MinhaFeature`)
5. Abrir um Pull Request

## 📝 Licença

Este projeto está sob a licença MIT. Veja o arquivo [LICENSE](LICENSE) para mais detalhes.

## ✨ Autor

**coldrenatinho**

- GitHub: [@coldrenatinho](https://github.com/coldrenatinho)

---

⭐ Se este projeto foi útil, considere dar uma estrela!
