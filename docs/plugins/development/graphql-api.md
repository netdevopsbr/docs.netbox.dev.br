<<<<<<< HEAD
# GraphQL API

## Definindo o Esquema da Classe (Schema Class)

Um plugin pode extender a API do GraphQL do NetBox registrando sua própria classe de esquema (schema class). Por padrão, NetBox irá tentar importar o `graphql.schena` do plugin, se existir. O caminho pode ser sobreposto definindo o `graphql_schema` na instância de PluginConfig como uma caminho pontilhado (dotted path) para a classe desejada do Python. Essa classe deve ser uma subclasse de `graphene.ObjectType`.
A plugin can extend NetBox's GraphQL API by registering its own schema class. By default, NetBox will attempt to import `graphql.schema` from the plugin, if it exists. This path can be overridden by defining `graphql_schema` on the PluginConfig instance as the dotted path to the desired Python class. This class must be a subclass of `graphene.ObjectType`.

### Exemplo
=======
# API GraphQL

!!! info

    **English (en):** This page was not translated yet!
    **Portuguese (pt-br):** Essa página não foi traduzida ainda!

## Defining the Schema Class

A plugin can extend NetBox's GraphQL API by registering its own schema class. By default, NetBox will attempt to import `graphql.schema` from the plugin, if it exists. This path can be overridden by defining `graphql_schema` on the PluginConfig instance as the dotted path to the desired Python class. This class must be a subclass of `graphene.ObjectType`.

### Example
>>>>>>> e06ef5523ba15ec31b7ed58bf5799b98023831bc

```python
# graphql.py
import graphene
from netbox.graphql.types import NetBoxObjectType
from netbox.graphql.fields import ObjectField, ObjectListField
from . import filtersets, models

class MyModelType(NetBoxObjectType):

    class Meta:
        model = models.MyModel
        fields = '__all__'
        filterset_class = filtersets.MyModelFilterSet

class MyQuery(graphene.ObjectType):
    mymodel = ObjectField(MyModelType)
    mymodel_list = ObjectListField(MyModelType)

schema = MyQuery
```

<<<<<<< HEAD
## Objetos GraphQL

O NetBox fornece dois tipos de classes de objeto para serem utilizadas pelos plugins:
=======
## GraphQL Objects

NetBox provides two object type classes for use by plugins.
>>>>>>> e06ef5523ba15ec31b7ed58bf5799b98023831bc

::: netbox.graphql.types.BaseObjectType
    options:
      members: false

::: netbox.graphql.types.NetBoxObjectType
    options:
      members: false

<<<<<<< HEAD
## Campos GraphQL

O NetBox fornece duas classes de campos para serem utilizadas pelos plugins:
=======
## GraphQL Fields

NetBox provides two field classes for use by plugins.
>>>>>>> e06ef5523ba15ec31b7ed58bf5799b98023831bc

::: netbox.graphql.fields.ObjectField
    options:
      members: false

::: netbox.graphql.fields.ObjectListField
    options:
      members: false
