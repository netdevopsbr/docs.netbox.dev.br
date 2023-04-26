# Instalação do Banco de Dados PostgreSQL

Essa seção detalha o processo de configuração e instalação do banco de dados PostgreSQL locamente. Se você já tem um banco de dados PostgreSQL configurado, pode passar para [a próxima seção](2-redis.md).


!!! warning "PostgreSQL 11 ou maior é necessária"
    O NetBox exige uma versão igual ou maior a PostgreSQL 11. Note que o MySQL ou outros bancos de dados relacionais **não são** suportados.

## Instalação

=== "Ubuntu"

    ```no-highlight
    sudo apt update
    sudo apt install -y postgresql
    ```

=== "CentOS"

    ```no-highlight
    sudo yum install -y postgresql-server
    sudo postgresql-setup --initdb
    ```

    CentOS configura a autenticação baseada em host com ident para o PostgreSQL por padrão. Porque o NetBox irá precisar autenticar com o usuário e senha, modifique `/var/lib/pgsql/data/pg_hba.conf` para suportar a autenticação MD5 ao mudar `ident` to `md5` seguindo as linhas abaixo:

    ```no-highlight
    host    all             all             127.0.0.1/32            md5
    host    all             all             ::1/128                 md5
    ```

Uma vez que o PostgreSQL foi instalado, inicie (start) o serviço e habilite-o para iniciar no boot:

```no-highlight
sudo systemctl start postgresql
sudo systemctl enable postgresql
```

Antes de continuar, verifique que você tenha instalado a versão 11 do PostgreSQL ou uma maior:

```no-highlight
psql -V
```

## Criação do Banco de Dados

No mínimo, nós preciamos criar um banco de dados para o NetBox e associar o usuário e senha para autenticação. Comece com o shell do PostgreSQL como o usuário de sistema do Postgres.

```no-highlight
sudo -u postgres psql
```

Dentro do shell, utilize os seguintes comandos para criar o banco de dados e usuario (função / role), substituindo seu próprio valor pela senha:

```postgresql
CREATE DATABASE netbox;
CREATE USER netbox WITH PASSWORD 'J5brHrAXFLQSif0K';
ALTER DATABASE netbox OWNER TO netbox;
```

!!! danger "Use a strong password"
    **Não use a senha desse exemplo.** Escolhe uma senha forte e aleatório para garantir uma autenticação segura do banco de dados para a instalação do seu NetBox.

Uma vez completa, pressione `\q` para sair do shell do PostgreSQL.

## Verifique o Status do Serviço

Você pode verificar que a autenticação funciona ao executar o comando `psql` passando o usuário e senha configurados. (Substitua `localhost` com o banco de dados do servidor se você estiver utilizando um banco de dados remoto.)

```no-highlight
$ psql --username netbox --password --host localhost netbox
Password for user netbox: 
psql (12.5 (Ubuntu 12.5-0ubuntu0.20.04.1))
SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, bits: 256, compression: off)
Type "help" for help.

netbox=> \conninfo
You are connected to database "netbox" as user "netbox" on host "localhost" (address "127.0.0.1") at port "5432".
SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, bits: 256, compression: off)
netbox=> \q
```

Se tiver sucesso, você pode executar `netbox` no prompt. Escreva `\conninfo` para confirmar sua conexão, ou `\q` para sair.