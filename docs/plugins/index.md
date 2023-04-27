# Plugins

<<<<<<< HEAD
Plugins são pacotes de Django app que podem ser instalados juntos com o NetBox para fornecer funcionalidades customizadas não presentes na aplicação em si. Plugins introduzem seus próprios modelos e visualizações (views), mas não podem interferir em componentes existentes. Um usuário do NetBox pode optar por instalar os plugins criados pela comunidade ou criar o seu próprio.

Plugins são suportados desde a v2.8.

## Capacidades

A arquitetura de plugins no NetBox permite:

* **Adicionar novos modelos de dados.** Um plugin pode introduzir um ou mais modelos para armazenar dados. Um modelo é essencialmente uma tabela dentro do banco de dados SQL.
* **Adicionar novas URLs e páginas web (views).** Plugins podem registrar suas próprias URLs abaixo do caminho root (path root) `/plugins` para fornecer páginas ao usuário facilmente "pesquisáveis" (browsable).
* **Adicionar conteúdo aos templates de modelos existentes.** A classe de template de conteúdo pode ser utilizada para injetar HTML customizado dentro de uma página (view) do modelo nativo do NetBox. Esse conteúdo pode aparecer no lado esquerdo, direito ou ainda no final da página.
* **Adicionar items no menu de navegação.** Cada plugin pode registrar novos links no menu de navegação. Cada link pode ter um conjunto de botões com ações específicas, similar aos botões de navegação nativos.
* **Declarar um middleware customizado.** Um middleware Django customizado pode ser registrado por cada plugin.
* **Limitar a instalação por versão do Netbox.** Um plugin pode especificar uma versão mímima e/ou máxima a qual o NetBox é compatível.

## Limitações

Seja por políticas or por limitações técnicas, a interação dos plugins com o código nativo do NetBox é restrita de certas formas. Um plugin não pode:

* **Modificando os modelos nativos.** Plugins não podem alterar, remover ou sobrepor os modelos nativos do NetBox de forma alguma. Essa regra existe para garantir a integridade dos modelos de dados nativos.
* **Registrar URLs fora de `/plugins`.** Todas as URLs dos plugins estão restritas nesse caminho (path) para previnir colisões de caminhos com as URLs do próprio NetBox ou de outros plugins.
* **Sobrepor os templates nativos.** Plugins podem injetar conteúdo adicional onde houver suporte, mas não pode manipular ou remover qualquer conteúdo nativo.
* **Modificar as configurações nativas do NetBox.** O registro de configuração é fornecida para os plugins, mas não podem alterar ou deletar as configurações nativas.
* **Disabilitar componentes nativos.** Plugins não podem disabilitar ou esconder componentes nativos do NetBox.

## Instalando Plugins

As instruções abaixo detalham o processo de instalação e como habilitar um plugin.

### Instalando o Pacote

Fazer o Download e instale o pacote do plugin utilizando sua própria orientação de instalação. Plugins publicados via PyPI são tipicamente instalados usando pip. Certifique-se de instalar o plugin dentro do ambiente virtual do Python.
=======
!!! info

    **English (en):** This page was not translated yet!
    **Portuguese (pt-br):** Essa página não foi traduzida ainda!

Plugins are packaged [Django](https://docs.djangoproject.com/) apps that can be installed alongside NetBox to provide custom functionality not present in the core application. Plugins can introduce their own models and views, but cannot interfere with existing components. A NetBox user may opt to install plugins provided by the community or build his or her own.

Plugins are supported on NetBox v2.8 and later.

## Capabilities

The NetBox plugin architecture allows for the following:

* **Add new data models.** A plugin can introduce one or more models to hold data. (A model is essentially a table in the SQL database.)
* **Add new URLs and views.** Plugins can register URLs under the `/plugins` root path to provide browsable views for users.
* **Add content to existing model templates.** A template content class can be used to inject custom HTML content within the view of a core NetBox model. This content can appear in the left side, right side, or bottom of the page.
* **Add navigation menu items.** Each plugin can register new links in the navigation menu. Each link may have a set of buttons for specific actions, similar to the built-in navigation items.
* **Add custom middleware.** Custom Django middleware can be registered by each plugin.
* **Declare configuration parameters.** Each plugin can define required, optional, and default configuration parameters within its unique namespace. Plug configuration parameter are defined by the user under `PLUGINS_CONFIG` in `configuration.py`.
* **Limit installation by NetBox version.** A plugin can specify a minimum and/or maximum NetBox version with which it is compatible.

## Limitations

Either by policy or by technical limitation, the interaction of plugins with NetBox core is restricted in certain ways. A plugin may not:

* **Modify core models.** Plugins may not alter, remove, or override core NetBox models in any way. This rule is in place to ensure the integrity of the core data model.
* **Register URLs outside the `/plugins` root.** All plugin URLs are restricted to this path to prevent path collisions with core or other plugins.
* **Override core templates.** Plugins can inject additional content where supported, but may not manipulate or remove core content.
* **Modify core settings.** A configuration registry is provided for plugins, however they cannot alter or delete the core configuration.
* **Disable core components.** Plugins are not permitted to disable or hide core NetBox components.

## Installing Plugins

The instructions below detail the process for installing and enabling a NetBox plugin.

### Install Package

Download and install the plugin package per its installation instructions. Plugins published via PyPI are typically installed using pip. Be sure to install the plugin within NetBox's virtual environment.
>>>>>>> e06ef5523ba15ec31b7ed58bf5799b98023831bc

```no-highlight
$ source /opt/netbox/venv/bin/activate
(venv) $ pip install <package>
```

<<<<<<< HEAD
Alternativamente, você talvez queira instalar o plugin manualmente utilizando o comando `python setup.py install`. Se você estiver desenvolvendo um plugin e quiser instalar de forma temporária, utilize o comando `python setup.py develop` no lugar.

### Habilitando o Plugin

Em `configuration.py`, adicione o nome do plugin na lista `PLUGINS`:

```python
PLUGINS = [
    'nome_do_plugin',
]
```

### Configurando o Plugin

Se o plugin precisar de qualquer configuração, defina-a em `configuration.py` no parâmetro `PLUGINS_CONFIG`. Os parâmetros de configuração disponíveis devem ser detalhados no arquivo README do plugin.

```no-highlight
PLUGINS_CONFIG = {
    'nome_do_plugin': {
=======
Alternatively, you may wish to install the plugin manually by running `python setup.py install`. If you are developing a plugin and want to install it only temporarily, run `python setup.py develop` instead.

### Enable the Plugin

In `configuration.py`, add the plugin's name to the `PLUGINS` list:

```python
PLUGINS = [
    'plugin_name',
]
```

### Configure Plugin

If the plugin requires any configuration, define it in `configuration.py` under the `PLUGINS_CONFIG` parameter. The available configuration parameters should be detailed in the plugin's README file.

```no-highlight
PLUGINS_CONFIG = {
    'plugin_name': {
>>>>>>> e06ef5523ba15ec31b7ed58bf5799b98023831bc
        'foo': 'bar',
        'buzz': 'bazz'
    }
}
```

<<<<<<< HEAD
### Rodando as Migrações do Banco de Dados

Se o plugin introduz modelos novos do banco de dados, rode o esquema fornecido de migrações:
=======
### Run Database Migrations

If the plugin introduces new database models, run the provided schema migrations:
>>>>>>> e06ef5523ba15ec31b7ed58bf5799b98023831bc

```no-highlight
(venv) $ cd /opt/netbox/netbox/
(venv) $ python3 manage.py migrate
```

<<<<<<< HEAD
### Coletando Arquivos Estático

Plugins podem empacotar arquivos estáticos para serem servidos diretamente pelo front end HTTP. Garanta que esses arquivos sejam copiados para o diretório root estático (static) com o comando de gerência `collectstatic`:
=======
### Collect Static Files

Plugins may package static files to be served directly by the HTTP front end. Ensure that these are copied to the static root directory with the `collectstatic` management command:
>>>>>>> e06ef5523ba15ec31b7ed58bf5799b98023831bc

```no-highlight
(venv) $ cd /opt/netbox/netbox/
(venv) $ python3 manage.py collectstatic
```

<<<<<<< HEAD
### Reiniciando o Serviço WSGI

Reiniciando o serviço WSGI para carregar o novo plugin:
=======
### Restart WSGI Service

Restart the WSGI service to load the new plugin:
>>>>>>> e06ef5523ba15ec31b7ed58bf5799b98023831bc

```no-highlight
# sudo systemctl restart netbox
```

<<<<<<< HEAD
## Removendo os Plugins

Siga os passos para completamente remover o plugin.

### Atualizando as Configurações

Remova o plugin da lista `PLUGINS` em `configuration.py`. Também remova qualquer parâmetro de configuração que seja relevante de `PLUGINS_CONFIG`.

### Removendo o Pacote Python

Utilize o `pip` para remover o plugin instalado:
=======
## Removing Plugins

Follow these steps to completely remove a plugin.

### Update Configuration

Remove the plugin from the `PLUGINS` list in `configuration.py`. Also remove any relevant configuration parameters from `PLUGINS_CONFIG`.

### Remove the Python Package

Use `pip` to remove the installed plugin:
>>>>>>> e06ef5523ba15ec31b7ed58bf5799b98023831bc

```no-highlight
$ source /opt/netbox/venv/bin/activate
(venv) $ pip uninstall <package>
```

<<<<<<< HEAD
### Reinicando o Serviço WSGI

Para reiniciar o serviço WSGI:
=======
### Restart WSGI Service

Restart the WSGI service:
>>>>>>> e06ef5523ba15ec31b7ed58bf5799b98023831bc

```no-highlight
# sudo systemctl restart netbox
```

<<<<<<< HEAD
### Removendo (Drop) as Tabelas do Banco de Dados

!!! note Observação

    Esse passo é necessário somente se o plugin criou uma ou mais tabelas do banco de dados (geralmente através da introdução de novos modelos). Verifique a documentação do plugin se você estiver incerto.

Entre no shell do banco de dados do PostgreSQL para verificar se o plugin criou alguma tabela SQL. Substitua `pluginname` no exemplo abaixo pelo nome do plugin que está sendo removido.
=======
### Drop Database Tables

!!! note
    This step is necessary only for plugin which have created one or more database tables (generally through the introduction of new models). Check your plugin's documentation if unsure.

Enter the PostgreSQL database shell to determine if the plugin has created any SQL tables. Substitute `pluginname` in the example below for the name of the plugin being removed. (You can also run the `\dt` command without a pattern to list _all_ tables.)
>>>>>>> e06ef5523ba15ec31b7ed58bf5799b98023831bc

```no-highlight
netbox=> \dt pluginname_*
                   List of relations
                   List of relations
 Schema |       Name     | Type  | Owner
--------+----------------+-------+--------
 public | pluginname_foo | table | netbox
 public | pluginname_bar | table | netbox
(2 rows)
```

<<<<<<< HEAD
!!! warning Aviso

    Tenha extremo cuidado ao remover tabelas. Usuários são fortemente encoragados de realizar um backup do seu banco de dados imediatamente antes de tomar qualquer ação.

Drop (delete) cada tabela listada para remover do banco de dados:
=======
!!! warning
    Exercise extreme caution when removing tables. Users are strongly encouraged to perform a backup of their database immediately before taking these actions.

Drop each of the listed tables to remove it from the database:
>>>>>>> e06ef5523ba15ec31b7ed58bf5799b98023831bc

```no-highlight
netbox=> DROP TABLE pluginname_foo;
DROP TABLE
netbox=> DROP TABLE pluginname_bar;
DROP TABLE
<<<<<<< HEAD
```
=======
```
>>>>>>> e06ef5523ba15ec31b7ed58bf5799b98023831bc
