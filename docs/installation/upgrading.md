# Fazendo Upgrade do NetBox para Última Versão

Fazer o upgrade do NetBox para uma nova versão é bem simples, no entanto usuários devem ter cautela para sempre analisar as notas das versões e salvar o backup do seu ambiente atual de produção antes de iniciar o upgrade.

NetBox can generally be upgraded directly to any newer release with no interim steps, with the one exception being incrementing major versions. This can be done only from the most recent _minor_ release of the major version. For example, NetBox v2.11.8 can be upgraded to version 3.3.2 following the steps below. However, a deployment of NetBox v2.10.10 or earlier must first be upgraded to any v2.11 release, and then to any v3.x release. (This is to accommodate the consolidation of database schema migrations effected by a major version change).

O NetBox geralmente pode ser atualizado diretamente para qualquer nova versão sem passos intermediários, com uma exceção sendo quando versões incremetais **major**. Essa atualização direta pode ser feita da versão __minor__ mais recente para a versão major. Por exemplo, NetBox v2.11.8 pode ser atualizado para a versão 3.3.2 seguindo os passos abaixo. No entanto, o deployment de uma versão v2.10.10 do NetBoux ou mais atngai deve primeiro ser atualizada para qualqeur versão v2.11 e então para qualquer versão v3.x. (Isso é para acomodar a consolidação do esquema de migrações do banco de dados que foram afetadas por uma mudançã de versão grande (major)).

[![Caminhos (paths) de Upgrade](../media/installation/upgrade_paths.png)](../media/installation/upgrade_paths.png)

!!! warning Realizando o Backup

    Sempre tenha certeza de ser um backup salvo do seu NetBox de produção atual antes de começar o processo de backup.

## 1. Avalisando as Notas de Versões (Release Notes)

Antes de realizar o upgrade da sua instância do NetBox, certifique-se de cuidadosamente ler todos os [release notes](../release-notes/index.md) que foram publicados desde sua versão atual. Embora o processo de upgrade normalmente não envolva trabalho adicional, certas versões podem introduzir mudanças que quebram alguma coisa ou que removem compatibilidade com versões muito antigas. Essas mudanças são registradas no release notes abaixo da versão em que ela afeta.

## 2. Atualizando as Dependências para as Versões Exigidas

NetBox v3.0 ou maior exige as seguintes versões de dependências:

| Dependência | Versão Mínima |
|------------|-----------------|
| Python     | 3.8             |
| PostgreSQL | 11              |
| Redis      | 4.0             |

## 3. Instalando a Versão mais Atual

Assim como na instalação inicial, você pode fazer o upgrade do NetBox seja fazendo o download do pacote com a última versão ou fazendo o clone do ramo (branch) `master` do repositório git.

!!! warning Atenção

    Utilize o mesmo método como você usou para instalar o NetBox originalmente

Se você não tem certeza de que como instalou o NetBox, verifique com esse comando:

```
ls -ld /opt/netbox /opt/netbox/.git
```

Se você instalou de um pacote, então `/opt/netbox` será um link simbólico apontando para a versão atual e `/opt/netbox/.git` não irá existir. Se você instalou do git, então `/opt/netbox` e `/opt/netbox/.git` existirão como diretórios normais.

### Opção A: Fazendo o Download da Versão (Release)

Faça o download da [última versão estável](https://github.com/netbox-community/netbox/releases) do GitHub como um arquivo tarball ou ZIP. Extraia-o no caminho (path) desejado. Nesse exemplo, utilizaremos `/opt/netbox`.

Fazendo o download e extraindo para a última versão:

```no-highlight
wget https://github.com/netbox-community/netbox/archive/vX.Y.Z.tar.gz
sudo tar -xzf vX.Y.Z.tar.gz -C /opt
sudo ln -sfn /opt/netbox-X.Y.Z/ /opt/netbox
```

Copie `local_requirements.txt`, `configuration.py` e `ldap_config.py` (se existir) da instalação atual para a nova versão:

```no-highlight
sudo cp /opt/netbox-X.Y.Z/local_requirements.txt /opt/netbox/
sudo cp /opt/netbox-X.Y.Z/netbox/netbox/configuration.py /opt/netbox/netbox/netbox/
sudo cp /opt/netbox-X.Y.Z/netbox/netbox/ldap_config.py /opt/netbox/netbox/netbox/
```

Tenha certeza de replicar as mídias que subiu ao sistema também. (A ação necessária irá depender de como você escolheu armazenar as mídias, mas em geral mover ou copiar os arquivos do diretório de media (mídia) é suficiente).

```no-highlight
sudo cp -pr /opt/netbox-X.Y.Z/netbox/media/ /opt/netbox/netbox/
```

Também certifique-se de copiar ou fazer o link de qualquer script customizado que você tenha feito. Observe que se estiverem armazenados fora do root do projeto, você não precisar copiá-los. (Verifique os parâmetros `SCRIPTS_ROOT` e `REPORTS_ROOT` no arquivo de configuração acima se estiver incerto.)

```no-highlight
sudo cp -r /opt/netbox-X.Y.Z/netbox/scripts /opt/netbox/netbox/
sudo cp -r /opt/netbox-X.Y.Z/netbox/reports /opt/netbox/netbox/
```

Se você seguiu o guia de instalação original para configurar o gunicor, tenha certeza de copiar sua configuraçao também:

```no-highlight
sudo cp /opt/netbox-X.Y.Z/gunicorn.py /opt/netbox/
```

### Opção B: Clonando o repositório Git

Esse guia assume que o NetBox foi instalado em `/opt/netbox`. Puxe (pull) a interação mais recente do ramo (branch0) master:

```no-highlight
cd /opt/netbox
sudo git checkout master
sudo git pull origin master
```

!!! info Verificando uma Versão Antiga

    Se você precisa fazer o upgrade de uma versão mais antiga ao invés da versão atual estável, você pode verificar por qualquer [tag do git](https://github.com/netbox-community/netbox/tags) válida, cada uma representa uma versão. Por exemplo, para mudar (checkout) o código para a versão v2.11.11 do NetBox, use:

        sudo git checkout v2.11.11

## 4. Rodando o Script de Upgrade

Uma vez que novo código esteja no lugar, verifique qualquer pacote Python opcional exigido pelo seu deployment em específico (como `napalm` ou `django-auth-ldap`) estão listados dentro de `local_requirements.txt`.

```no-highlight
sudo ./upgrade.sh
```

!!! warning Aviso

    Se a versão padrão do Python não for ao menos a 3.8, você precisar passar o caminho (path) para uma versão Python suportada como uma variável de ambiente quando for utilizar o script de upgrade.

    ```no-highlight
    sudo PYTHON=/usr/bin/python3.8 ./upgrade.sh
    ```

Esse script realiza as seguintes ações:

* Destrói e rebuilda o ambiente virtual do Python (venv)
* Instala todos os pacotes necessários do Python (listados em `requirements.txt`)
* Instala qualquer pacote Python adicional listado em `local_requirements.txt`
* Aplica qualquer migração do banco de dados inclusa nessa versão
* Faz o build da documentação localmente (para uso em offline)
* Coleta (collects) todos os arquivos estáticos (static files) para serem servidos pelo serviço HTTP
* Deleta tipos de conteúdo velhos (stales) do banco de dados
* Deleta todas as sessões de usuário expiradas do banco de dados.

!!! note Observação

    Se o script de upgrade alertar sobre migrações do banco de dados não realizadas, isso indica que algumas mudanças foram feitas no seu banco de dados local e deve ser investigado. Nunca tente criar novas migrações ao menos que você intencionalmente esteja modificando o esquema do banco de dados.

## 5. Reiniciando o Serviço do NetBox

!!! warning Aviso

    Se você estiver fazendo o upgrade de uma instalação que não usa o ambiente virtual do Python (venv), ou seja, qualquer versão anterior a v2.7.9, você precisará atualizar os arquivos de serviço do systemd para referenciar os executáveis novos do Python e do gunicorn antes de reiniciar os serviços. Eles estão localizados em `/opt/netbox/venv/bin`. Veja o arquivo de serviço em `/opt/netbox/contrib` como referência.

Finalmente, reinicie os serviços do gunicorn e RQ:

```no-highlight
sudo systemctl restart netbox netbox-rq
```

## 6. Verificando o Agendamento de Housekeeping (Limpeza)

Se estiver atualizando de uma versão anterior a v3.0, verifique a tarefa do cron (ou um processo agendado similar) que foi configurado para rodar o comando noturno de housekeeping. Um shell script que utiliza o comando está incluso em `contrib/netbox-housekeeping.sh`. Ele pode ser linkado do seu diretório de tarefas diárias, ou configurado dentro do diretório do crontab diretamente. (Se o NetBox foi instalado em um caminho (path) não padrão (nonstandard), ceritique-se de atualizar os caminhos (paths) do sistema dentro do script primeiro.)

```shell
sudo ln -s /opt/netbox/contrib/netbox-housekeeping.sh /etc/cron.daily/netbox-housekeeping
```

Verifique a [documentação de housekeeping](../administration/housekeeping.md) para mais detalhes.