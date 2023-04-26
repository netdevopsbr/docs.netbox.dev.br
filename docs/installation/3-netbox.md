# Instalação do NetBox

## Instale os Pacotes do Sistema

Comece instalando todos os pacotes do sistema exigidos pelo NetBox e suas dependências.

!!! warning "Python 3.8 ou maior é necessário!"

    NetBox requer a versão 3.8, 3.9, 3.10 ou 3.11 do Python.

=== "Ubuntu"

    ```no-highlight
    sudo apt install -y python3 python3-pip python3-venv python3-dev build-essential libxml2-dev libxslt1-dev libffi-dev libpq-dev libssl-dev zlib1g-dev
    ```

=== "CentOS"

    ```no-highlight
    sudo yum install -y gcc libxml2-devel libxslt-devel libffi-devel libpq-devel openssl-devel redhat-rpm-config
    ```

Antes de continuar, verifique que você instalou uma versão do Python ao menos na v3.8:

```no-highlight
python3 -V
```

## Download NetBox

Essa documentação forn+ece duas opções de instalação do NetBox: pelo download de um arquivo ou de um repositório git. Instalar pelo pacote (opção A abaixo) requer um fetching (sincronização) manual e download do arquivo para todos os updates e atualizações, enquanto que a opção de instalação pelo git (opção B) permite um upgrade ininterrupto ao simplesmente dar um pull (fazer o download) novamente do ramo (branch) `master`.

### Opção A: Realizar o Download do Arquivo com a versão desejada

Faça o download dá [última versão estável](https://github.com/netbox-community/netbox/releases) do GitHub como um arquivo tarball ou ZIP e extraia-o para o caminho (local) desejado. Nesse exemplo, nós vamos usar o `/opt/netbox` como root do NetBox.

```no-highlight
sudo wget https://github.com/netbox-community/netbox/archive/refs/tags/vX.Y.Z.tar.gz
sudo tar -xzf vX.Y.Z.tar.gz -C /opt
sudo ln -s /opt/netbox-X.Y.Z/ /opt/netbox
```

!!! note Observação

    É recomendado instalr o NetBox em um diretório nomeado com seu número de versão. Por exemplo, NetBox v3.0.0 seria instalado em `/opt/netbox-3.0.0`, e um link simbólico (symlink) de `/opt/netbox` apontaria para essa localização. (Você pode verificar essa configuração com o comando `ls -l /opt | grep netbox`.) Isso permite que versões futuras possam ser instaladas em paralelo sem interromper a instalação atual. Ao mudar para uma versão nova, apenas atualize o link simbólico.

### Opção B: Clone o repositório Git

Criar um diretório base para a instalação do NetBox. Para esse guia, nós iremos utilizar `/opt/netbox`.

```no-highlight
sudo mkdir -p /opt/netbox/
cd /opt/netbox/
```

Se o `git` não estiver instalado, instale-o:

=== "Ubuntu"

    ```no-highlight
    sudo apt install -y git
    ```

=== "CentOS"

    ```no-highlight
    sudo yum install -y git
    ```

Agora, clone o ramo (branch) **master** do repositório do NetBox no GitHub para o diretório atual. (Essa branch sempre terá a versão estável atual.)

```no-highlight
sudo git clone -b master --depth 1 https://github.com/netbox-community/netbox.git .
```

!!! note Observação
    O comando `git clone` acima utiliza um "shadow clone" para obter somente o commit mais recente. Se você precisa fazer o downlaod do histórico inteiro, omita o argumento `--depth 1`.

O comando `git clone` deve gerar uma saída (output) similar conforme a seguinte:

```
Cloning into '.'...
remote: Enumerating objects: 996, done.
remote: Counting objects: 100% (996/996), done.
remote: Compressing objects: 100% (935/935), done.
remote: Total 996 (delta 148), reused 386 (delta 34), pack-reused 0
Receiving objects: 100% (996/996), 4.26 MiB | 9.81 MiB/s, done.
Resolving deltas: 100% (148/148), done.
```

!!! note Observação
    Instalação através do git também permite que você facilmente tente diferentes versões do NetBox. Para verificar [uma versão específica do NetBox](https://github.com/netbox-community/netbox/releases), utilize o comando `git checkout` com a tag de versão desejada. Por exemplo, `git checkout v3.0.8`.

## Crie o Usuário de Sistema do NetBox

Crie a conta do usuário do sistema nomeado de `netbox`. Nós iremos configurar os serviços WSGI e HTTP para rodar em cima desta conta. Nós vamos associar o usuário para ser dono do diretório media. Isso garante que o NetBox irá poder salvar os arquivos que foram feito upload ao NetBox.

=== "Ubuntu"

    ```
    sudo adduser --system --group netbox
    sudo chown --recursive netbox /opt/netbox/netbox/media/
    ```

=== "CentOS"

    ```
    sudo groupadd --system netbox
    sudo adduser --system -g netbox netbox
    sudo chown --recursive netbox /opt/netbox/netbox/media/
    ```

## Configuração

Mova o diretório de configuração do NetBox e faça uma cópia de `configuration_example.py` para `configuration.py`. Esse arquivo irá conter todos os parâmetros de configuração da sua instância local do NetBox.

```no-highlight
cd /opt/netbox/netbox/netbox/
sudo cp configuration_example.py configuration.py
```

Open `configuration.py` with your preferred editor to begin configuring NetBox. NetBox offers [many configuration parameters](../configuration/index.md), but only the following four are required for new installations:

* `ALLOWED_HOSTS`
* `DATABASE`
* `REDIS`
* `SECRET_KEY`

### ALLOWED_HOSTS


É uma lista dos hostnames válidos e endereço IP que o servidor pode ser alcançado. Você deve especificar ao menos um ou mais nomes ou endereços IP. (Observe que isso não restringe os locais que o NetBox pode ser acessado: é meramente [utilizado para a validação de host no cabeçalho do HTTP](https://docs.djangoproject.com/en/3.0/topics/security/#host-headers-virtual-hosting))

```python
ALLOWED_HOSTS = ['netbox.example.com', '192.0.2.123']
```

Se você ainda não tem certeza do nome de domínio ou endereço IP que a instalação do NetBox irá utilizar, você pode definir um wildcard (asterisco) para permitir todos os valores de host:

```python
ALLOWED_HOSTS = ['*']
```

### DATABASE

Esse parâmetro armazena os detalhes de configuração do banco de dados. Você deve definir o usuário e senha utilizados quando você configurou o PostgreSQL. Se o serviço está rodando em um host remoto, atualize os parâmetros `HOST` e `PORT` para estarem de acordo. Veja a [documentação da configuração](../configuration/required-parameters.md#database) para ter mais detalhes de parâmetros individuais.


```python
DATABASE = {
    'NAME': 'netbox',               # Database name
    'USER': 'netbox',               # PostgreSQL username
    'PASSWORD': 'J5brHrAXFLQSif0K', # PostgreSQL password
    'HOST': 'localhost',            # Database server
    'PORT': '',                     # Database port (leave blank for default)
    'CONN_MAX_AGE': 300,            # Max database connection age (seconds)
}
```

### REDIS

O Rediz armazena valores no formato chave-valor dentro da memória (e somente nela) e é utilizado pelo NetBox para caching e tasks de background enfileiradas. Redis tipicamente requer configurações mínimas; os valores abaixo são suficientes para a maioria das instalações. Acesse [a documentação da configuração](../configuration/required-parameters.md#redis) para mais detalhes sobre parâmetros individuais.

Observe que o NetBox exige uma especificação de dois bancos de dados separados do Redis: `tasks` e `caching`. Esses são ambos fornecidos pelo mesmo serviço de Redis, no entanto cada um deve ter um único ID número do banco de dados.

```python
REDIS = {
    'tasks': {
        'HOST': 'localhost',      # Servidor Redis
        'PORT': 6379,             # Porta do Redis
        'PASSWORD': '',           # Senha do Redis (opcional)
        'DATABASE': 0,            # Database ID
        'SSL': False,             # Usar SSL (opcional)
    },
    'caching': {
        'HOST': 'localhost',
        'PORT': 6379,
        'PASSWORD': '',
        'DATABASE': 1,            # ID único para o segundo banco de dados
        'SSL': False,
    }
}
```

### SECRET_KEY

This parameter must be assigned a randomly-generated key employed as a salt for hashing and related cryptographic functions. (Note, however, that it is _never_ directly used in the encryption of secret data.) This key must be unique to this installation and is recommended to be at least 50 characters long. It should not be shared outside the local system.

Esse parâmetro deve ser associado com uma chave (key) randômicamente gerada e é utilizada como um salt para hashing e funções de criptografia relacionados. (Note, no entanto, que isso _nunca_ deve ser utilizada na encriptação de dados secretos.) Essa chave deve ser única para a instalação e é recomendado que tenha ao menos 50 caracteres. Não deve ser compartilhada fora do sistema local.

Um simples script em Python com o nome de `generate_secret_key.py` é fornecido no diretório pai para auxiliar na geração desta key:

```no-highlight
python3 ../generate_secret_key.py
```

!!! warning "SECRET_KEY values must match"

    No caso de uma instalação com alta disponibilidade com diferentes servidores web, `SECRET_KEY` deve ser idêntico entre os servidores para manter uma persistência de estado da sessão do usuário.

Quando você terminar de modificar a configuração, lembre de salvar o arquivo.

## Pacotes e Dependências Opcionais

Todos os pacotes Python exigidos pelo NetBox estão listados em `requirements.txt` e serão instalados automaticamente. NetBox também suporta pacotes opcionais. Se desejar, esses pacotes devem ser listados em `local_requirements.txt` dentro do diretório root do NetBox.

### NAPALM

A integração com a [biblioteca de automação via NAPALM](../integrations/napalm.md) permite que o NetBox obtenha dados de produção e atuais dos dispositivos e retorne para o requisitor através de API REST. A configuração dos parâmetros `NAPALM_USERNAME` e `NAPALM_PASSWORD` definem as credenciais para serem utilizadas ao se conectar com o dispositivo.

```no-highlight
sudo sh -c "echo 'napalm' >> /opt/netbox/local_requirements.txt"
```

### Armazenamento de Arquivos (Storage) Remoto

Por padrão, o NetBox irá utilizar o sistema de arquivos local para realizar o upload de arquivos. Para utilizar um sistema de arquivos remoto, instale a biblioteca [`django-storages`](https://django-storages.readthedocs.io/en/stable/) e configure seu [backend de armazenamento desejado](../configuration/system.md#storage_backend) dentro de `configuration.py`.


```no-highlight
sudo sh -c "echo 'django-storages' >> /opt/netbox/local_requirements.txt"
```

## Rodando o Script de Upgrade

Uma vez que o NetBox foi configurado, estamos prontos para prosseguir com a instalação em si. Nós iremos rodar o script de upgrade (`upgrade.sh`) para realizar as seguintes ações:

* Criar um ambiente virtual do Python (virtual environment)
* Instala todos os pacote do Python necessários
* Roda as migrações de esquema (schema) do banco de dados
* Faz o build da documentação localmente (para uso offline)
* Agrega os recursos de arquivos no disco

!!! warning
    
     Se você ainda tiver um ambiente virtual do Python ativo do passo de instalação anterior, desabilite-o rodando o comando `deactivate`. Esse comando evita erros no sistema onde o `sudo` foi configurado para preservar o ambiente atual do usuário.

```no-highlight
sudo /opt/netbox/upgrade.sh
```

Observe que o **Python 3.8 ou maior é necessário** para o NetBox v3.2 ou versões maiores. Se a instalação padrão do Python no seu servidor for uma menor que a exigida, passe o caminho (path) para a instalação suportada como uma variável de ambiente com o nome de `PYTHON`. (Note que a variável de ambiente deve ser passada _depois_ depois do comando `sudo`.)

```no-highlight
sudo PYTHON=/usr/bin/python3.8 /opt/netbox/upgrade.sh
```

!!! note Observação

    Uma vez completa, o script de upgrade pode retornar um aviso que existe um ambiente virtual detectado. Como é uma instalação nova, esse aviso pode ser seguramente ignorado.

## Criar um Usuário Super

O NetBox não vem com contas de usuários pré-definidas. Você vai precisar criar um usuário super (conta administrativa) para habilitar o login ao NetBox. Primeiro, entre no ambiente virtual do Python (venv) criado pelo script de upgrade:

```no-highlight
source /opt/netbox/venv/bin/activate
```

Uma vez que o ambiente virtual foi ativado, você vai notar que o texto `(venv)` vai estar no início (prefixo) do prompt do console.

Agora, vamos criar uma conta de superuser (usuário super) utilizando o comando de gerência do Django `createsuperuser` (através do `manage.py`). Especificando um endereço de email para o usuário não é obrigatório, mas tenha certeza de usar uma senha forte.

```no-highlight
cd /opt/netbox/netbox
python3 manage.py createsuperuser
```

## Agendar uma Tarefa de Housekeeping

O NetBox inclui um comando de gerência de `housekeeping` que lida com algumas tarefas recorrentes de limpeza, como limpar as sessões velhas ou expiradas de registros de mudanças. Embora esse comando pode ser rodado manualmente, é recomendado configurar uma tarefa agendada utilizando o daemon do sistema `cron` ou uma ferramenta similar.

O script em shell que invoca esse comando está em `contrib/netbox-housekeeping.sh` Ele pode ser copiado ou linkado do seu diretório de tarefas diárias do cron, ou incluso dentro do diretório do crontab diretamente. (Se estiver instalando o NetBox em um caminho não padrão (nonstandard path), certifique-se de atualizar os caminhos (paths) do sistema dentro do script primeiro.)

```shell
sudo ln -s /opt/netbox/contrib/netbox-housekeeping.sh /etc/cron.daily/netbox-housekeeping
```

Olhe a [documentação de housekeeping](../administration/housekeeping.md) para mais detalhes.

## Testar a Aplicação

At this point, we should be able to run NetBox's development server for testing. We can check by starting a development instance locally.

Neste ponto, nós deveríamos ser capazes de rodar o servidor de desenvolvimento para testes. Nós podemos verificar ao iniciar a instância de desenvolvimento localmente. 
!!! tip Dica

    Verifique que o ambiente virtual do Python ainda está ativo antes de tentar rodar o servidor.

```no-highlight
python3 manage.py runserver 0.0.0.0:8000 --insecure
```

Se houver sucesso, você deve ter uma saída (output) similar ao:

```no-highlight
Watching for file changes with StatReloader
Performing system checks...

System check identified no issues (0 silenced).
August 30, 2021 - 18:02:23
Django version 3.2.6, using settings 'netbox.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

Agora, conecte-se ao nome ou endereço IP do servidor (como foi definido em `ALLOWED_HOSTS`) na porta 8000; por exemplo <http://127.0.0.1:8000>. Você deve ser recebido com uma mensagem de boas-vindas da página inicial do NetBox. Tente logar com o usuário e senha especificados ao criar o usuário super (superuser).

!!! note Observação

    Por padrão, distribuições baseadas no RHEL vão provavelmente bloquear suas tentativas de teste com o **firewalld**. A porta de desenvolvimento do servidor pode ser aberta com `firewall-cmd` (adicione `--permanent` se você quiser que a regra se mantenha após o servidor ser reiniciado)

    ```no-highlight
    firewall-cmd --zone=public --add-port=8000/tcp
    ```

!!! danger Não utilize em produção!

    O servidor de desenvolvimento deve ser utilizando somente para desenvolvimento e testes. Não é nem perfomático ou seguro suficiente para ser utilizado em produção. **Não utilize-o em produção!**

!!! warning Aviso

    Se o serviço de teste não funcionar, ou você não conseguiu acessar a página inicial (homepage) do NetBox, alguma coisa deu errado. Não siga com o resto guia até que a instalação tenha sido corrigida.

Escreva `Ctrl+C` para parar com o servidor de desenvolvimento.