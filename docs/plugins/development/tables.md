# Tabelas

O NetBox utiliza a biblioteca [`django-tables2`](https://django-tables2.readthedocs.io/) para renderizar tabelas de objetos dinâmicas. Essas tabelas mostram a lista de objetos e podem ser ordenadas e filtradas com diversos parâmetros.

## NetBoxTable

Para fornecer funcionalidades adicionais além do grupo de funções suportads pela classe `Table` dentro de `django-tables2`. O NetBox fornece a classe `NetBoxTable`. Essa classe de tabela customizada inclui o suporte de:

* Configuração do usuário para exibição de coluna e ordenamento
* Colunas de campos customizados & links customizados
* A pré-obtenção automática de objetos relacionados

Isso inclui diversas colunas padrões diversas:

* `pk` - Um checkbox para selecionar o objeto associado com cada linha de cada tabela (onde aplicável)
* `id` - O ID número do objeto do banco de dados, como um hyperlink (link) da visualização do objeto (escondida por padrão)
* `actions` - Um menu de dropdown apresentando ações específicas do objeto disponíveis ao usuário

### Exemplo

```python
# tables.py
import django_tables2 as tables
from netbox.tables import NetBoxTable
from .models import MyModel

class MyModelTable(NetBoxTable):
    name = tables.Column(
        linkify=True
    )
    ...

    class Meta(NetBoxTable.Meta):
        model = MyModel
        fields = ('pk', 'id', 'name', ...)
        default_columns = ('pk', 'name', ...)
```

### Configuração da Tabela

A classe `NetBoxTabel` oferece funções dinâmicas de configuração para permitir que os usuários mudem a exibição de suas colunas ao ordenas as preferências. Para configurar uma tabela para requisições específicas, simplesmente chame o método `configure()` e passe o objeto corrente `HTTPRequest`. Por exemplo:

```python
table = MyModelTable(data=MyModel.objects.all())
table.configure(request)
```

Automaticamente será aplicadas preferências especificadas pelo usuário para a tabela. (Se estiver utilizando uma visualização genérica (generic view) fornecida pelo NetBox, configuração da tabela é automaticamente provisionada.)

## Colunas

As classes da coluna listadas abaixo são suportadas para serem utilizadas pelos plugins. Essas classes podem ser importadas de `netbox.tables.columns`.

::: netbox.tables.BooleanColumn
    options:
      members: false

::: netbox.tables.ChoiceFieldColumn
    options:
      members: false

::: netbox.tables.ColorColumn
    options:
      members: false

::: netbox.tables.ColoredLabelColumn
    options:
      members: false

::: netbox.tables.ContentTypeColumn
    options:
      members: false

::: netbox.tables.ContentTypesColumn
    options:
      members: false

::: netbox.tables.MarkdownColumn
    options:
      members: false

::: netbox.tables.TagColumn
    options:
      members: false

::: netbox.tables.TemplateColumn
    options:
      members:
        - __init__
