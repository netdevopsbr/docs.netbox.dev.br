---
title: Passo 01 - Setup Inicial
description: 
published: true
date: 2022-05-11T02:15:34.861Z
tags: 
editor: markdown
dateCreated: 2022-05-10T20:04:52.428Z
---

# Passo 1: Configuração Inicial (REVISAR A TRADUÇÃO)

Antes de começarmos à trabalhar no plugin em si, primeiro temos que garantir que temos um ambiente de desenvolvimento adequado para o plugin.

<br>

<div align=center>
 
## Criando o Ambiente de Desenvolvimento
</div>

### Instalação do Netbox

O desenvolvimento de plugins exige uma instalação local do Netbox. Se você ainda não tem o Netbox instalado, pode consultar a [instruções de instalação](https://netbox.readthedocs.io/en/feature/installation/).

> **Dica:** Se a instalação será utilizada somente para **desenvolvimento**, é necessário seguir somente até o passo três do **Guia de Instalação** para ter um servidor de desenvolvimento do Netbox rodando corretamente (`manage.py runserver`).
{.is-info}

> **AVISO:** Esse guia exige uma versão do Netbox maior ou igual a v3.2. Tentar usar uma versão mais recente **não irá funcionar**.
{.is-warning}

<br>
 
### Clonar o Repositório git

Agora, nós iremos clonar a versão demo do repositório git do GitHub. Primeiro, utilize o comando `cd` na pasta de sua preferência (o repositório base do seu usuário é suficiente), e então clonar o repositório com o comando `git clone`. Nós iremos clonar o repositório já na branch `step00-empty`, que vai nos permitir iniciar do zero. 

```bash
$ git clone --branch step00-empty https://github.com/netbox-community/netbox-plugin-demo
Cloning into 'netbox-plugin-demo'...
remote: Enumerating objects: 58, done.
remote: Counting objects: 100% (58/58), done.
remote: Compressing objects: 100% (42/42), done.
remote: Total 58 (delta 12), reused 58 (delta 12), pack-reused 0
Unpacking objects: 100% (58/58), done.
```

> **Observação:** Não é estritamente necessário clonar o repositório demo. Mas vai te permitir facilmente verificar as versões (snapshots) do codigo enquanto progredimos e resolvemos os problemas.
{.is-info}

<br>

<div align=center>

## Configuração do Plugin
</div>

### Criar o arquivo `__init__.py`

Na classe `PluginConfig` está toda a informação necessária sobre o plugin para instalá-lo. 
Primeiro, teremos que criar um sub-diretório onde estará todo o código do plugin, assim como o arquivo `__init__.py` que terá definição da classe `PluginConfig`.

```bash
mkdir netbox_access_lists
touch netbox_access_lists/__init__.py
```

Agora, abra o arquivo `__init__.py` no editor de texto de sua preferência e importe a classe `PluginConfig` do Netbox no topo do arquivo.

```python
from extras.plugins import PluginConfig
```

<br>

### Criar a classe `PluginConfig`

Nós criaremos uma nova classe chamada `NetBoxAccessListsConfig` criando uma subclasse de `PluginConfig`. Isso irá definir todos os parâmetros necessários para controlar a configuração do nosso plugin uma vez que seja instalado. Existe muitos [parâmetros opcionais](https://netbox.readthedocs.io/en/feature/plugins/development/#pluginconfig-attributes) que podem ser configurados aqui, mas por enquanto vamos precisar definir alguns somente.

```python
class NetBoxAccessListsConfig(PluginConfig):
    name = 'netbox_access_lists'
    verbose_name = ' NetBox Access Lists'
    description = 'Manage simple ACLs in NetBox'
    version = '0.1'
    base_url = 'access-lists'
```

Somente com isso já conseguimos instalar o plugin posteriormente. Finalmente, precisamos expor essa classe como `config` para permitir que o Netbox detecte isso. Para isso, adicione a seguinte linha de código no fim do arquivo:

```python
config = NetBoxAccessListsConfig
```

<br>

<div align=center>

## Criar um README
</div>

É considerado uma boa prática sempre incluir um arquivo `README` para qualquer código que você publique. **Serve como um pequeno pedaço de documentação para explicar o propósito do seu projeto, como instalá-lo e utilizar, onde achar ajuda, etc.** Como esse projeto é somente para aprendizado, nós não temos muita coisa para dizer sobre nosso plugin, mas criaremos o arquivo de qualquer forma.

De volta à pasta root do projeto (um nível acima de `__init__.py`), crie um arquivo chamado `README.md` e coloque o seguinte conteúdo:

```markdown
## netbox-access-lists

Manage simple access control lists in NetBox
```

> Note que definimos ao arquivo `README` a extensão `md`. Isso informa as ferramentas que suportam esse tipo de arquivo para que renderize como **Markdown**.
{.is-info}

<br>

<div align=center>

## Instalar o Plugin
</div>

### Criar o arquivo`setup.py`

Para permitir a instalação do nosso plugin no ambiente virtual que criamos acima, iremos criar um simples script Python. No diretório root do projeto (pasta inicial), crie um arquivo chamado `setup.py` e insira o código abaixo.

```python
from setuptools import find_packages, setup

setup(
    name='netbox-access-lists',
    version='0.1',
    description='An example NetBox plugin',
    install_requires=[],
    packages=find_packages(),
    include_package_data=True,
    zip_safe=False,
)
```

> **AVISO:** Certifique-se de criar o `setup.py` na **pasta root do projeto** e _não_ dentro do diretório `netbox_access_lists`.
{.is-warning}

Nesse arquivo nós iremos chamar a função `setup()` fornecida pela biblioteca [`setuptools`](https://packaging.python.org/en/latest/guides/distributing-packages-using-setuptools/) do Python para instalar nosso código. Existe diversos argumentos adicionais que podem ser informados, mas para o nosso exemplo já é suficiente os que iremos utilizar.

> Há várias maneiras alternativas para instalar um código Python que funciona tão bem quanto o **setuptools**. Não há problema caso queria utilizar outro método, mas fique ciente que esse guia assume que você está utilizando o `setuptools` e ajustado conforme orientado.
{.is-info}

<br>

### Ativar o Ambiente Virtual

Para garantir que o plugin seja acessível pela instalação do Netbox, nós primeiro precisamos ativar o [ambiente virtual do Python](https://docs.python.org/3/library/venv.html) que foi criado quando instalamos o Netbox. Para isso, determine o caminho (path) para o ambiente virtual (que por padrão é `/opt/netbox/venv/`, caso tenha utilizado os passos informados no guia de instalação) e ative-o:

```bash
source /opt/netbox/venv/bin/activate
```

<br>

### Rode o `setup.py`

We can now install our plugin by running `setup.py`. First, make sure the virtual environment is still active, then run the following command from the project's root. The `develop` argument tells `setuptools` to create a link to our local development path instead of copying files into the virtual environment. This avoids the need to re-install the plugin every time we make a change.

Nós agora podemos instalar nosso plugin utilizando o `setup.py`. Primeiro, certifique-se que o ambiente virtual está ativo ainda e, então, rode o seguinte comando na pasta root do projeto (plugin).

```bash
$ python3 setup.py develop
running develop
running egg_info
creating netbox_access_lists.egg-info
writing manifest file 'netbox_access_lists.egg-info/SOURCES.txt'
writing manifest file 'netbox_access_lists.egg-info/SOURCES.txt'
running build_ext
```

O argumento `develop` diz ao `setuptools` que crie um **link** para o **pasta/caminho (path) local de desenvolvimento** ao invés de copiar os arquivos para o ambiente virtual. Isso evita que seja necessário instalar o plugin novamente toda vez que uma mudança é feita.

<br>

### Configurar o NetBox

Finalmente, nós precisamos configurar o Netbox para habilitar o nosso novo plugin. Vá até a pasta (path) onde o Netbox foi instalado, abra o arquivo `netbox/netbox/configuration.py` e procure pelo parâmetro `PLUGINS`; que por padrão é uma lista vazia. (Se o parâmetro não estiver definido ainda, você pode criá-lo). Adicione o nome do nosso plugin nesta lista:

```python
# configuration.py
PLUGINS = [
    'netbox_access_lists',
]
```

Salve o arquivo e rode o servidor de desenvolvimento do Netbox (caso ainda não esteja rodando):

```bash
$ python netbox/manage.py runserver
```

O servidor de desenvolvimento é pra ter iniciado corretamente. Abra o Netbox em uma nova janela do navegador, **logue como superuser** e navegue para a interface admin do Netbox (admin UI). Em **System > Installed Plugins** você deve ver o nosso plugin listado.

![Django admin UI: Plugins list](/netbox/plugins/netbox-plugin-tutorial/step01-initial-setup/step01-django-admin-plugins.png)

> Você pode verificar seu trabalho no fim de cada passo (step) do tutorial rodando o comando `git diff` em relação à branch correspondente. Por exemplo, quando terminar o **Passo 01**, rode o comando `git diff step01-initial-setup` para comparar seu trabalho (código) com o exemplo já feito previamente. Isso irá ajudar identificar se por acaso esqueceu de realizar um dos passos do tutorial.
{.is-info}

Com isso, terminamos nosso setup inicial. Agora iremos para a parte boa!
<br>

<div align="center">

  
- [Passo 02: Models](./netbox-plugin-tutorial/step02-models.md) :arrow_right:
{.links-list}
</div>
