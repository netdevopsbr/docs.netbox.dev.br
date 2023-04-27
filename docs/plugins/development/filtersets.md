<<<<<<< HEAD
# Filtros e Grupos de Filtro (Filter Sets)

Grupos de filtros definem os mecanismos disponíveis para filtrar ou pesquisar um grupo de objetos no NetBox. Por exemplo, sites (locais) podem ser filtradas pela região ou grupo pai (parent), status, ID da instalação (facility) e por aí vai. O mesmo grupo de filtro deve ser usado de forma consistente para um modelo seja em uma requisição feita pela interface web, API REST, ou uma API GraphQL. NetBox utiliza a biblioteca [django-filters2](https://django-tables2.readthedocs.io/en/latest/) para definir os grupos de filtros.

## Classes de FilterSets (Grupos de Filtro)

Para suportar funcionalidades adicionais às padrões dos modelos do NetBox, como a associação de tag e suporte a campos customizadas, a classe `NetBoxModelFilterSe` está disponível para uso dos plugins. Isso deve ser usado pela classe base de grupos de filtros para os modelos (models) do plugin que herdam `NetBoxModel`. Dentro dessa clase, filtros individuais podem ser declaradas diretamente conforme documentação do `django-filters`. Um exemplo abaixo é fornecido.

=======
# Filtros & Grupos de Filtro

!!! info

    **English (en):** This page was not translated yet!
    **Portuguese (pt-br):** Essa página não foi traduzida ainda!

Filter sets define the mechanisms available for filtering or searching through a set of objects in NetBox. For instance, sites can be filtered by their parent region or group, status, facility ID, and so on. The same filter set is used consistently for a model whether the request is made via the UI, REST API, or GraphQL API. NetBox employs the [django-filters2](https://django-tables2.readthedocs.io/en/latest/) library to define filter sets.

## FilterSet Classes

To support additional functionality standard to NetBox models, such as tag assignment and custom field support, the `NetBoxModelFilterSet` class is available for use by plugins. This should be used as the base filter set class for plugin models which inherit from `NetBoxModel`. Within this class, individual filters can be declared as directed by the `django-filters` documentation. An example is provided below.
>>>>>>> e06ef5523ba15ec31b7ed58bf5799b98023831bc

```python
# filtersets.py
import django_filters
from netbox.filtersets import NetBoxModelFilterSet
from .models import MyModel

class MyFilterSet(NetBoxModelFilterSet):
    status = django_filters.MultipleChoiceFilter(
        choices=(
            ('foo', 'Foo'),
            ('bar', 'Bar'),
            ('baz', 'Baz'),
        ),
        null_value=None
    )

    class Meta:
        model = MyModel
        fields = ('some', 'other', 'fields')
```

<<<<<<< HEAD
### Declarando Grupos de Filtro (Filter Sets)

Para utilizar grupos de filtro em uma subclasse de uma visualização genérica do NetBox (como `ObjectListView` ou `BulkEditView`), defina o atributo `filterset` na classe de visualização (view class):
=======
### Declaring Filter Sets

To utilize a filter set in a subclass of one of NetBox's generic views (such as `ObjectListView` or `BulkEditView`), define the `filterset` attribute on the view class:
>>>>>>> e06ef5523ba15ec31b7ed58bf5799b98023831bc

```python
# views.py
from netbox.views.generic import ObjectListView
from .filtersets import MyModelFilterSet
from .models import MyModel

class MyModelListView(ObjectListView):
    queryset = MyModel.objects.all()
    filterset = MyModelFilterSet
```

<<<<<<< HEAD
Para habilitar um grupo de filtro no endpoint da API REST, configure o atributo `filterset_class` na visualização (view) da API:
=======
To enable a filter set on a  REST API endpoint, set the `filterset_class` attribute on the API view:
>>>>>>> e06ef5523ba15ec31b7ed58bf5799b98023831bc

```python
# api/views.py
from myplugin import models, filtersets
from . import serializers

class MyModelViewSet(...):
    queryset = models.MyModel.objects.all()
    serializer_class = serializers.MyModelSerializer
    filterset_class = filtersets.MyModelFilterSet
```

<<<<<<< HEAD
## Classes de Filtro

### TagFilter

A classe `TagFilter` está disponível para todosos modelos que suportam associação de tag (os que herdam de `NetBoxModel` ou `TagsMixin`). Esse filtro cria uma subclasse do `ModelMultipleChoiceFilter` para trabalhar com a classe `TaggedItem`.
=======
## Filter Classes

### TagFilter

The `TagFilter` class is available for all models which support tag assignment (those which inherit from `NetBoxModel` or `TagsMixin`). This filter subclasses django-filter's `ModelMultipleChoiceFilter` to work with NetBox's `TaggedItem` class.
>>>>>>> e06ef5523ba15ec31b7ed58bf5799b98023831bc

```python
from django_filters import FilterSet
from extras.filters import TagFilter

class MyModelFilterSet(FilterSet):
    tag = TagFilter()
```
