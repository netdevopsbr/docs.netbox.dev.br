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

A pasta de nível maior é o root do projeto, que pode ter qualquer nome que você queira. Imediatamente dentro do root deve existir diversos items:

* `setup.py` - O script padrão de instalação usado para instalar o pacote do plugin dentro do ambiente Python.
* `README.md` - Breve introdução do plugin, como instalar e configurá-lo, onde achar ajuda e informações pertinentes. É recomendado escrever os arquivos `README` usando linguagem markdown como o **Markdown** para habilitar uma exibição facilmente lida por humanos.
* O diretório fonte do plugin (source directory) Deve ser um nome de pacote Python válido, tipicamente contendo somente letras em minúsculo, números e underline.

O diretório fonte do plugin deve conter todo o código Python e outros recursos utilizados pelo Plugin. A sua estrutura é deixada para ser definida pelo autor, no entanto é recomendado seguir boas práticas como as mostradas na [documentação do Django](https://docs.djangoproject.com/en/stable/intro/reusable-apps/). No mínimo, esse diretório **deve** conter o arquivo `__init__.py` contendo uma instância da classe `PluginConfig` do NetBox, discutida abaixo.

## PluginConfig

The `PluginConfig` class is a NetBox-specific wrapper around Django's built-in [`AppConfig`](https://docs.djangoproject.com/en/stable/ref/applications/) class. It is used to declare NetBox plugin functionality within a Python package. Each plugin should provide its own subclass, defining its name, metadata, and default and required configuration parameters. An example is below:

A classe específica `PluginConfig` do NetBox junta tudo que é nativo do Django da classse [`AppConfig`](https://docs.djangoproject.com/en/stable/ref/applications/). É utilizada para declarar as funcionalidades do plugin do NetBox dentro do pacote Python. Cad aplugin deve fornecer sua própria subclasse, definindo o nome, meta dado, e os padrões padrões e parâmetros obrigatórios. Um exemplo abaixo:

```python
from extras.plugins import PluginConfig

class FooBarConfig(PluginConfig):
    name = 'foo_bar'
    verbose_name = 'Foo Bar'
    description = 'Exemplo de plugin do NetBox'
    version = '0.1'
    author = 'Jeremy Stretch'
    author_email = 'autor@exemplo.com'
    base_url = 'foo-bar'
    required_settings = []
    default_settings = {
        'baz': True
    }
    django_apps = ["foo", "bar", "baz"]

config = FooBarConfig
```

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
    
    ```python
    from extras.plugins import get_plugin_config
    get_plugin_config('my_plugin', 'verbose_name')
    ```

#### Observações Importantes Sobre `django_apps`

Carregar apps adicionais podem causar mais malefícios do que benefícios que poderiam causar a identificação de problemas do NetBox em si mais difícil. O atributo `django_apps` é usada somente para casos avançados que exigem uma integração maior com o Django.

Os Apps dessa lista são inserios **antes** da `PluginConfig` do plugin na ordem em que são definidas. Adicionar o módulo `PluginConfig` do plugin nessa lista muda o comportamento e permite que os apps sejam carregados **depois** do plugin.

Qualquer app adicional deve ser instalado dvem estar dentro do mesmo ambiente virtual Python do NetBox ou exceções `ImproperlyConfigured` serão criadas ao carregar o plugin.

## Criando o setup.py

`setup.py` é o [script de setup](https://docs.python.org/3.8/distutils/setupscript.html) usado para empacotar e instalar nosso plugin uma vez que foi finalizado. A função primária desse script é chamar a a função da biblioteca setuptools `setup()` para criar o pacote de distribuição do Python. Nós podemos passar vários argumentos para controlar a criação do pacote, assim como fornecer meta dados sobre o plugin. Um exemplo de `setup.py` abaixo:

```python
from setuptools import find_packages, setup

setup(
    name='my-example-plugin',
    version='0.1',
    description='Um plugin de exemplo',
    url='https://github.com/jeremystretch/my-example-plugin',
    author='Jeremy Stretch',
    license='Apache 2.0',
    install_requires=[],
    packages=find_packages(),
    include_package_data=True,
    zip_safe=False,
)
```

Vários desses parâmetros são auto explicativos, verifique a [documentação do setuptools](https://setuptools.readthedocs.io/en/latest/setuptools.html).

!!! info Observação

    `zip_safe=False` é **obrigatório** pois a interação atual do plugin não é segura com zip devido a seguinte issue de python [issue19699](https://bugs.python.org/issue19699)

## Crindo um Ambiente Virtual

É extremamente recomendado criar um [ambiente virtual](https://docs.python.org/3/tutorial/venv.html) do Python para o desenvolvimento do seu plugin, oposto ao utilizar os pacotes system-wide. Irá suportar o controle completo das versões instaladas de todas as dependências para evitar o conflito com os pacotes do sistema. O ambiente pode estar onde você quiser, no entanto deve ser excluido do controlo de revisão. (Uma conveção popular é manter todos os ambientes virutais dentro do diretório do usuário, exemplo `~/.virtualenvs/`.)

```shell
python3 -m venv ~/.virtualenvs/my_plugin
```

Você pode permitir o NetBox disponível dentro do ambiente ao criar um arquivo de caminho (path) para sua localização. Isso irá adicionar o caminho (path) para ativação. (Certifique-se de ajsutar o comando abaixo para especificar o caminho de ambiente virtual, a versão do Python e a instalação do NetBox.)

```shell
echo /opt/netbox/netbox > $VENV/lib/python3.8/site-packages/netbox.pth
```

## Instalação de Desenvolvimento

Para facilitar o desenvolvimento, é recomendando seguir em frente e instalar o plugin neste ponto utilizando o modo do setuptools `develop`. Irá criar um link simbólico dentro do seu ambiente virtual para o diretório de desenvolvimento do plugin. Chame `setup.py` do diretório root do plugin com o argumento `develop` (no lugar de `install`):

```no-highlight
$ python setup.py develop
```

## Configurando o NetBox

Para habilitar o plugin do NetBox, adicione o parâmetro `PLUGINS` em `configuration.py`:

```python
PLUGINS = [
    'my_plugin',
]
```