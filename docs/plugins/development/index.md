# Plugins Development

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

The top level is the project root, which can have any name that you like. Immediately within the root should exist several items:

* `setup.py` - This is a standard installation script used to install the plugin package within the Python environment.
* `README.md` - A brief introduction to your plugin, how to install and configure it, where to find help, and any other pertinent information. It is recommended to write `README` files using a markup language such as Markdown to enable human-friendly display.
* The plugin source directory. This must be a valid Python package name, typically comprising only lowercase letters, numbers, and underscores.

The plugin source directory contains all the actual Python code and other resources used by your plugin. Its structure is left to the author's discretion, however it is recommended to follow best practices as outlined in the [Django documentation](https://docs.djangoproject.com/en/stable/intro/reusable-apps/). At a minimum, this directory **must** contain an `__init__.py` file containing an instance of NetBox's `PluginConfig` class, discussed below.

## PluginConfig

The `PluginConfig` class is a NetBox-specific wrapper around Django's built-in [`AppConfig`](https://docs.djangoproject.com/en/stable/ref/applications/) class. It is used to declare NetBox plugin functionality within a Python package. Each plugin should provide its own subclass, defining its name, metadata, and default and required configuration parameters. An example is below:

```python
from extras.plugins import PluginConfig

class FooBarConfig(PluginConfig):
    name = 'foo_bar'
    verbose_name = 'Foo Bar'
    description = 'An example NetBox plugin'
    version = '0.1'
    author = 'Jeremy Stretch'
    author_email = 'author@example.com'
    base_url = 'foo-bar'
    required_settings = []
    default_settings = {
        'baz': True
    }
    django_apps = ["foo", "bar", "baz"]

config = FooBarConfig
```

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
    
    ```python
    from extras.plugins import get_plugin_config
    get_plugin_config('my_plugin', 'verbose_name')
    ```

#### Important Notes About `django_apps`

Loading additional apps may cause more harm than good and could make identifying problems within NetBox itself more difficult. The `django_apps` attribute is intended only for advanced use cases that require a deeper Django integration.

Apps from this list are inserted *before* the plugin's `PluginConfig` in the order defined. Adding the plugin's `PluginConfig` module to this list changes this behavior and allows for apps to be loaded *after* the plugin.

Any additional apps must be installed within the same Python environment as NetBox or `ImproperlyConfigured` exceptions will be raised when loading the plugin.

## Create setup.py

`setup.py` is the [setup script](https://docs.python.org/3.8/distutils/setupscript.html) used to package and install our plugin once it's finished. The primary function of this script is to call the setuptools library's `setup()` function to create a Python distribution package. We can pass a number of keyword arguments to control the package creation as well as to provide metadata about the plugin. An example `setup.py` is below:

```python
from setuptools import find_packages, setup

setup(
    name='my-example-plugin',
    version='0.1',
    description='An example NetBox plugin',
    url='https://github.com/jeremystretch/my-example-plugin',
    author='Jeremy Stretch',
    license='Apache 2.0',
    install_requires=[],
    packages=find_packages(),
    include_package_data=True,
    zip_safe=False,
)
```

Many of these are self-explanatory, but for more information, see the [setuptools documentation](https://setuptools.readthedocs.io/en/latest/setuptools.html).

!!! info
    `zip_safe=False` is **required** as the current plugin iteration is not zip safe due to upstream python issue [issue19699](https://bugs.python.org/issue19699)  

## Create a Virtual Environment

It is strongly recommended to create a Python [virtual environment](https://docs.python.org/3/tutorial/venv.html) for the development of your plugin, as opposed to using system-wide packages. This will afford you complete control over the installed versions of all dependencies and avoid conflict with system packages. This environment can live wherever you'd like, however it should be excluded from revision control. (A popular convention is to keep all virtual environments in the user's home directory, e.g. `~/.virtualenvs/`.)

```shell
python3 -m venv ~/.virtualenvs/my_plugin
```

You can make NetBox available within this environment by creating a path file pointing to its location. This will add NetBox to the Python path upon activation. (Be sure to adjust the command below to specify your actual virtual environment path, Python version, and NetBox installation.)

```shell
echo /opt/netbox/netbox > $VENV/lib/python3.8/site-packages/netbox.pth
```

## Development Installation

To ease development, it is recommended to go ahead and install the plugin at this point using setuptools' `develop` mode. This will create symbolic links within your Python environment to the plugin development directory. Call `setup.py` from the plugin's root directory with the `develop` argument (instead of `install`):

```no-highlight
$ python setup.py develop
```

## Configure NetBox

To enable the plugin in NetBox, add it to the `PLUGINS` parameter in `configuration.py`:

```python
PLUGINS = [
    'my_plugin',
]
```
