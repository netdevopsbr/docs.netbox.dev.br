<<<<<<< HEAD
# Plugins Development

!!! tip Tutorial de Desenvolvimento de Plugins

    Começando agora com Plugins? Olhe nosso [**Tutorial de Plugins do NetBox**](https://github.com/netbox-community/netbox-plugin-tutorial) no GitHub! Esse guia irá guia-lo de forma aprofundada o processo de criação de um plugin inteiro do início. Inclui também o [demo plugin repo](https://github.com/netbox-community/netbox-plugin-demo) para garantir que você possa pular para qualquer passo ao longo do caminho. Isso irá permitir que tenha um projeto up e rodando com plugins quase que instantaneamente!

O NetBox pode extender o suporte adicional aos modelos de dados e funcionalidades através do uso de plugins. Um plugin é essencialmente um [Django app](https://docs.djangoproject.com/en/stable/) contido em si mesmo que pode ser instalado junto com o NetBox para fornecer funcionalidades customizadas. Vários plugins podem ser instalados em uma instância de NetBox, e cada plugin pode ser habilitado para ser configurado independente.


!!! info Desenvolvimento em Django

    O Django é um framework Python ao qual o NetBox é feito. O Django em si é bem documentando, essa documentação cobre apenas os aspectos do desenvolvimento de plugins que são únicas ao NetBox.

Plugins podem fazer muitas coisas, incluido:

* Criando modelos Django para armazenar os dados no banco de dados
* Fornecer suas próprias "páginas" (views) na interface web do usuário
* Injetar templates de conteúdo e links de navegação
* Extender a API REST e GraphQL APIs do NetBox
* Carregar apps Django adicionais
* Adicionar middlewares customizados de requisição/resposta (request/response)

No entanto, tenha em mente que cada peça de funcionalidade é inteiramente opcional. Por exemplo, se o seu plugin meramente adiciona um middleware oum endpoint de API para os dados existentes.

!!! warning Aviso


    Enquanto que muito poderoso, a API dos Plugins do NetBox é ncessáriamente limitada em seu escopo. A API dos Plugins é discutida aqui completamente: qualquer parte do código do código base do NetBox não documentada aqui _não_ é parte da API de plugins suportadas, e não deve ser empregada por plugins. Elementos internos do NetBox são sujeitos a mudança a qualquer momento sem aviso prévio. Autores de Plugins são **fortemente** encorajados para desenvolverem plugins usando somente os componentes suportados oficialmente aqui e os fornecidos pelo framework do Django para evitar mudanças que quebram em futuras versões.

## Estrutura do Plugin

Embora a estrutura específica do plugin é deixada para ser definida pelos seus autores, um plugin do NetBox normalmente vai ter uma estrutura parecida com:
=======
# Desenvolvimento de Plugins

!!! info

    **English (en):** This page was not translated yet!
    **Portuguese (pt-br):** Essa página não foi traduzida ainda!

!!! tip "Plugins Development Tutorial"
    Just getting started with plugins? Check out our [**NetBox Plugin Tutorial**](https://github.com/netbox-community/netbox-plugin-tutorial) on GitHub! This in-depth guide will walk you through the process of creating an entire plugin from scratch. It even includes a companion [demo plugin repo](https://github.com/netbox-community/netbox-plugin-demo) to ensure you can jump in at any step along the way. This will get you up and running with plugins in no time!

NetBox can be extended to support additional data models and functionality through the use of plugins. A plugin is essentially a self-contained [Django app](https://docs.djangoproject.com/en/stable/) which gets installed alongside NetBox to provide custom functionality. Multiple plugins can be installed in a single NetBox instance, and each plugin can be enabled and configured independently.

!!! info "Django Development"
    Django is the Python framework on which NetBox is built. As Django itself is very well-documented, this documentation covers only the aspects of plugin development which are unique to NetBox.

Plugins can do a lot, including:

* Create Django models to store data in the database
* Provide their own "pages" (views) in the web user interface
* Inject template content and navigation links
* Extend NetBox's REST and GraphQL APIs
* Load additional Django apps
* Add custom request/response middleware

However, keep in mind that each piece of functionality is entirely optional. For example, if your plugin merely adds a piece of middleware or an API endpoint for existing data, there's no need to define any new models.

!!! warning
    While very powerful, the NetBox plugins API is necessarily limited in its scope. The plugins API is discussed here in its entirety: Any part of the NetBox code base not documented here is _not_ part of the supported plugins API, and should not be employed by a plugin. Internal elements of NetBox are subject to change at any time and without warning. Plugin authors are **strongly** encouraged to develop plugins using only the officially supported components discussed here and those provided by the underlying Django framework to avoid breaking changes in future releases.

## Plugin Structure

Although the specific structure of a plugin is largely left to the discretion of its authors, a typical NetBox plugin might look something like this:
>>>>>>> e06ef5523ba15ec31b7ed58bf5799b98023831bc

```no-highlight
project-name/
  - plugin_name/
    - api/
      - __init__.py
      - serializers.py
      - urls.py
      - views.py
    - migrations/
      - __init__.py
      - 0001_initial.py
      - ...
    - templates/
      - plugin_name/
        - *.html
    - __init__.py
    - filtersets.py
    - graphql.py
    - models.py
    - middleware.py
    - navigation.py
    - signals.py
    - tables.py
    - template_content.py
    - urls.py
    - views.py
  - README.md
  - setup.py
```

<<<<<<< HEAD
A pasta de nível maior é o root do projeto, que pode ter qualquer nome que você queira. Imediatamente dentro do root deve existir diversos items:

* `setup.py` - O script padrão de instalação usado para instalar o pacote do plugin dentro do ambiente Python.
* `README.md` - Breve introdução do plugin, como instalar e configurá-lo, onde achar ajuda e informações pertinentes. É recomendado escrever os arquivos `README` usando linguagem markdown como o **Markdown** para habilitar uma exibição facilmente lida por humanos.
* O diretório fonte do plugin (source directory) Deve ser um nome de pacote Python válido, tipicamente contendo somente letras em minúsculo, números e underline.

O diretório fonte do plugin deve conter todo o código Python e outros recursos utilizados pelo Plugin. A sua estrutura é deixada para ser definida pelo autor, no entanto é recomendado seguir boas práticas como as mostradas na [documentação do Django](https://docs.djangoproject.com/en/stable/intro/reusable-apps/). No mínimo, esse diretório **deve** conter o arquivo `__init__.py` contendo uma instância da classe `PluginConfig` do NetBox, discutida abaixo.
=======
The top level is the project root, which can have any name that you like. Immediately within the root should exist several items:

* `setup.py` - This is a standard installation script used to install the plugin package within the Python environment.
* `README.md` - A brief introduction to your plugin, how to install and configure it, where to find help, and any other pertinent information. It is recommended to write `README` files using a markup language such as Markdown to enable human-friendly display.
* The plugin source directory. This must be a valid Python package name, typically comprising only lowercase letters, numbers, and underscores.

The plugin source directory contains all the actual Python code and other resources used by your plugin. Its structure is left to the author's discretion, however it is recommended to follow best practices as outlined in the [Django documentation](https://docs.djangoproject.com/en/stable/intro/reusable-apps/). At a minimum, this directory **must** contain an `__init__.py` file containing an instance of NetBox's `PluginConfig` class, discussed below.
>>>>>>> e06ef5523ba15ec31b7ed58bf5799b98023831bc

## PluginConfig

The `PluginConfig` class is a NetBox-specific wrapper around Django's built-in [`AppConfig`](https://docs.djangoproject.com/en/stable/ref/applications/) class. It is used to declare NetBox plugin functionality within a Python package. Each plugin should provide its own subclass, defining its name, metadata, and default and required configuration parameters. An example is below:

<<<<<<< HEAD
A classe específica `PluginConfig` do NetBox junta tudo que é nativo do Django da classse [`AppConfig`](https://docs.djangoproject.com/en/stable/ref/applications/). É utilizada para declarar as funcionalidades do plugin do NetBox dentro do pacote Python. Cad aplugin deve fornecer sua própria subclasse, definindo o nome, meta dado, e os padrões padrões e parâmetros obrigatórios. Um exemplo abaixo:

=======
>>>>>>> e06ef5523ba15ec31b7ed58bf5799b98023831bc
```python
from extras.plugins import PluginConfig

class FooBarConfig(PluginConfig):
    name = 'foo_bar'
    verbose_name = 'Foo Bar'
<<<<<<< HEAD
    description = 'Exemplo de plugin do NetBox'
    version = '0.1'
    author = 'Jeremy Stretch'
    author_email = 'autor@exemplo.com'
=======
    description = 'An example NetBox plugin'
    version = '0.1'
    author = 'Jeremy Stretch'
    author_email = 'author@example.com'
>>>>>>> e06ef5523ba15ec31b7ed58bf5799b98023831bc
    base_url = 'foo-bar'
    required_settings = []
    default_settings = {
        'baz': True
    }
    django_apps = ["foo", "bar", "baz"]

config = FooBarConfig
```

<<<<<<< HEAD
O NetBox procura pela variável `config` dentro do arquivo `__init__.py` do plugin para carregar sua configuração. Normalmente, a classe PluginConfig será definida, mas você pode querer gerar uma classe PluginConfig dinamicamente baseada nas variáveis de ambiente ou outros fatores.

### Atributos de PluginConfig 

| Nome                  | Descrição                                                                                                                        |
|-----------------------|----------------------------------------------------------------------------------------------------------------------------------|
| `name`                | Nome do plugin;  mesmo que o diretório fonte                                                                                     | 
| `verbose_name`        | Nome fo plugin a ser lido por humanos                                                                                            | 
| `version`             | Versão atual. [Semântica de versionamento](https://semver.org/) é encorajada para ser utilizada.                                 | 
| `description`         | Breve descrição do próposito do plugin                                                                                           | 
| `author`              | Nome do autor do plugin                                                                                                          | 
| `author_email`        | Endereço de email público do autor                                                                                               | 
| `base_url`            | Caminho do diretório base para ser utilizado pela URL do plugin (opcional). Se não especificada, o nome do projeto (`name`) será utilizado                        | 
| `required_settings`   | Lista de qualquer parâmetro de configuração que **deve** ser definido pelo usuário                                               | 
| `default_settings`    | Dicionário (dict) com os parâmetros de configuração e seus valores padrões                                                       | 
| `django_apps`         | Lista de Django apps adicionais para serem utilizados junto com o plugin                                                         | 
| `min_version`         | Versão mínima do NetBox que o plugin é compatível                                                                                |
| `max_version`         | Versão máxima do NetBox que o plugin é compatível                                                                                |
| `middleware`          | Lista de classes de middleware para serem adicionadas após os middlewares nativos do NetBox                                      |
| `queues`              | Lista de tarefas de fundo (background taks) de filas para serem criadas                                                          |
| `search_extensions`   | Caminho tracejado (dotted path) para a lista de classes de search index (padrão: `search.indexes`)                               |
| `template_extensions` | Caminho tracejado (dotted path) para a lista de classes de extensão de template (padrão: `template_content.template_extensions`) |
| `menu_items`          | Caminho tracejado (dotted path) para a lista de items do menu fornecidos pelo plugin (padrão: `navigation.menu_items`)           |
| `graphql_schema`      | Caminho tracejado (dotted path) para a classe de esquema do plugin de GraphQL, se houver uma (padrão: `graphql.schema`)          |
| `user_preferences`    | Caminho tracejado (dotted path) para o dicionário (dict) de mapeamento das preferências do usuário definidas pelo plugin (padrão: `preferences.preferences`) |

Todas as configurações obrigatórios devem ser configuradas pelo usuário. Se houver um parâmetro de configuração listada tanto em `required_settings` e `default_settings`, a configuração padrão será ignorada.

!!! tip Acessando os Parâmetros de Configuração

    Os parâmetros de configuração do Plugin podem ser acessados usando a função `get_plugin_config()`. Por exemplo:
=======
NetBox looks for the `config` variable within a plugin's `__init__.py` to load its configuration. Typically, this will be set to the PluginConfig subclass, but you may wish to dynamically generate a PluginConfig class based on environment variables or other factors.

### PluginConfig Attributes

| Name                  | Description                                                                                                              |
|-----------------------|--------------------------------------------------------------------------------------------------------------------------|
| `name`                | Raw plugin name; same as the plugin's source directory                                                                   |
| `verbose_name`        | Human-friendly name for the plugin                                                                                       |
| `version`             | Current release ([semantic versioning](https://semver.org/) is encouraged)                                               |
| `description`         | Brief description of the plugin's purpose                                                                                |
| `author`              | Name of plugin's author                                                                                                  |
| `author_email`        | Author's public email address                                                                                            |
| `base_url`            | Base path to use for plugin URLs (optional). If not specified, the project's `name` will be used.                        |
| `required_settings`   | A list of any configuration parameters that **must** be defined by the user                                              |
| `default_settings`    | A dictionary of configuration parameters and their default values                                                        |
| `django_apps`         | A list of additional Django apps to load alongside the plugin                                                            |
| `min_version`         | Minimum version of NetBox with which the plugin is compatible                                                            |
| `max_version`         | Maximum version of NetBox with which the plugin is compatible                                                            |
| `middleware`          | A list of middleware classes to append after NetBox's build-in middleware                                                |
| `queues`              | A list of custom background task queues to create                                                                        |
| `search_extensions`   | The dotted path to the list of search index classes (default: `search.indexes`)                                          |
| `template_extensions` | The dotted path to the list of template extension classes (default: `template_content.template_extensions`)              |
| `menu_items`          | The dotted path to the list of menu items provided by the plugin (default: `navigation.menu_items`)                      |
| `graphql_schema`      | The dotted path to the plugin's GraphQL schema class, if any (default: `graphql.schema`)                                 |
| `user_preferences`    | The dotted path to the dictionary mapping of user preferences defined by the plugin (default: `preferences.preferences`) |

All required settings must be configured by the user. If a configuration parameter is listed in both `required_settings` and `default_settings`, the default setting will be ignored.

!!! tip "Accessing Config Parameters"
    Plugin configuration parameters can be accessed using the `get_plugin_config()` function. For example:
>>>>>>> e06ef5523ba15ec31b7ed58bf5799b98023831bc
    
    ```python
    from extras.plugins import get_plugin_config
    get_plugin_config('my_plugin', 'verbose_name')
    ```

<<<<<<< HEAD
#### Observações Importantes Sobre `django_apps`

Carregar apps adicionais podem causar mais malefícios do que benefícios que poderiam causar a identificação de problemas do NetBox em si mais difícil. O atributo `django_apps` é usada somente para casos avançados que exigem uma integração maior com o Django.

Os Apps dessa lista são inserios **antes** da `PluginConfig` do plugin na ordem em que são definidas. Adicionar o módulo `PluginConfig` do plugin nessa lista muda o comportamento e permite que os apps sejam carregados **depois** do plugin.

Qualquer app adicional deve ser instalado dvem estar dentro do mesmo ambiente virtual Python do NetBox ou exceções `ImproperlyConfigured` serão criadas ao carregar o plugin.

## Criando o setup.py

`setup.py` é o [script de setup](https://docs.python.org/3.8/distutils/setupscript.html) usado para empacotar e instalar nosso plugin uma vez que foi finalizado. A função primária desse script é chamar a a função da biblioteca setuptools `setup()` para criar o pacote de distribuição do Python. Nós podemos passar vários argumentos para controlar a criação do pacote, assim como fornecer meta dados sobre o plugin. Um exemplo de `setup.py` abaixo:
=======
#### Important Notes About `django_apps`

Loading additional apps may cause more harm than good and could make identifying problems within NetBox itself more difficult. The `django_apps` attribute is intended only for advanced use cases that require a deeper Django integration.

Apps from this list are inserted *before* the plugin's `PluginConfig` in the order defined. Adding the plugin's `PluginConfig` module to this list changes this behavior and allows for apps to be loaded *after* the plugin.

Any additional apps must be installed within the same Python environment as NetBox or `ImproperlyConfigured` exceptions will be raised when loading the plugin.

## Create setup.py

`setup.py` is the [setup script](https://docs.python.org/3.8/distutils/setupscript.html) used to package and install our plugin once it's finished. The primary function of this script is to call the setuptools library's `setup()` function to create a Python distribution package. We can pass a number of keyword arguments to control the package creation as well as to provide metadata about the plugin. An example `setup.py` is below:
>>>>>>> e06ef5523ba15ec31b7ed58bf5799b98023831bc

```python
from setuptools import find_packages, setup

setup(
    name='my-example-plugin',
    version='0.1',
<<<<<<< HEAD
    description='Um plugin de exemplo',
=======
    description='An example NetBox plugin',
>>>>>>> e06ef5523ba15ec31b7ed58bf5799b98023831bc
    url='https://github.com/jeremystretch/my-example-plugin',
    author='Jeremy Stretch',
    license='Apache 2.0',
    install_requires=[],
    packages=find_packages(),
    include_package_data=True,
    zip_safe=False,
)
```

<<<<<<< HEAD
Vários desses parâmetros são auto explicativos, verifique a [documentação do setuptools](https://setuptools.readthedocs.io/en/latest/setuptools.html).

!!! info Observação

    `zip_safe=False` é **obrigatório** pois a interação atual do plugin não é segura com zip devido a seguinte issue de python [issue19699](https://bugs.python.org/issue19699)

## Crindo um Ambiente Virtual

É extremamente recomendado criar um [ambiente virtual](https://docs.python.org/3/tutorial/venv.html) do Python para o desenvolvimento do seu plugin, oposto ao utilizar os pacotes system-wide. Irá suportar o controle completo das versões instaladas de todas as dependências para evitar o conflito com os pacotes do sistema. O ambiente pode estar onde você quiser, no entanto deve ser excluido do controlo de revisão. (Uma conveção popular é manter todos os ambientes virutais dentro do diretório do usuário, exemplo `~/.virtualenvs/`.)
=======
Many of these are self-explanatory, but for more information, see the [setuptools documentation](https://setuptools.readthedocs.io/en/latest/setuptools.html).

!!! info
    `zip_safe=False` is **required** as the current plugin iteration is not zip safe due to upstream python issue [issue19699](https://bugs.python.org/issue19699)  

## Create a Virtual Environment

It is strongly recommended to create a Python [virtual environment](https://docs.python.org/3/tutorial/venv.html) for the development of your plugin, as opposed to using system-wide packages. This will afford you complete control over the installed versions of all dependencies and avoid conflict with system packages. This environment can live wherever you'd like, however it should be excluded from revision control. (A popular convention is to keep all virtual environments in the user's home directory, e.g. `~/.virtualenvs/`.)
>>>>>>> e06ef5523ba15ec31b7ed58bf5799b98023831bc

```shell
python3 -m venv ~/.virtualenvs/my_plugin
```

<<<<<<< HEAD
Você pode permitir o NetBox disponível dentro do ambiente ao criar um arquivo de caminho (path) para sua localização. Isso irá adicionar o caminho (path) para ativação. (Certifique-se de ajsutar o comando abaixo para especificar o caminho de ambiente virtual, a versão do Python e a instalação do NetBox.)
=======
You can make NetBox available within this environment by creating a path file pointing to its location. This will add NetBox to the Python path upon activation. (Be sure to adjust the command below to specify your actual virtual environment path, Python version, and NetBox installation.)
>>>>>>> e06ef5523ba15ec31b7ed58bf5799b98023831bc

```shell
echo /opt/netbox/netbox > $VENV/lib/python3.8/site-packages/netbox.pth
```

<<<<<<< HEAD
## Instalação de Desenvolvimento

Para facilitar o desenvolvimento, é recomendando seguir em frente e instalar o plugin neste ponto utilizando o modo do setuptools `develop`. Irá criar um link simbólico dentro do seu ambiente virtual para o diretório de desenvolvimento do plugin. Chame `setup.py` do diretório root do plugin com o argumento `develop` (no lugar de `install`):
=======
## Development Installation

To ease development, it is recommended to go ahead and install the plugin at this point using setuptools' `develop` mode. This will create symbolic links within your Python environment to the plugin development directory. Call `setup.py` from the plugin's root directory with the `develop` argument (instead of `install`):
>>>>>>> e06ef5523ba15ec31b7ed58bf5799b98023831bc

```no-highlight
$ python setup.py develop
```

<<<<<<< HEAD
## Configurando o NetBox

Para habilitar o plugin do NetBox, adicione o parâmetro `PLUGINS` em `configuration.py`:
=======
## Configure NetBox

To enable the plugin in NetBox, add it to the `PLUGINS` parameter in `configuration.py`:
>>>>>>> e06ef5523ba15ec31b7ed58bf5799b98023831bc

```python
PLUGINS = [
    'my_plugin',
]
```
