# Filtros e Grupos de Filtro (Filter Sets)

Grupos de filtros definem os mecanismos disponíveis para filtrar ou pesquisar um grupo de objetos no NetBox. Por exemplo, sites (locais) podem ser filtradas pela região ou grupo pai (parent), status, ID da instalação (facility) e por aí vai. O mesmo grupo de filtro deve ser usado de forma consistente para um modelo seja em uma requisição feita pela interface web, API REST, ou uma API GraphQL. NetBox utiliza a biblioteca [django-filters2](https://django-tables2.readthedocs.io/en/latest/) para definir os grupos de filtros.

## Classes de FilterSets (Grupos de Filtro)

Para suportar funcionalidades adicionais às padrões dos modelos do NetBox, como a associação de tag e suporte a campos customizadas, a classe `NetBoxModelFilterSe` está disponível para uso dos plugins. Isso deve ser usado pela classe base de grupos de filtros para os modelos (models) do plugin que herdam `NetBoxModel`. Dentro dessa clase, filtros individuais podem ser declaradas diretamente conforme documentação do `django-filters`. Um exemplo abaixo é fornecido.


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

### Declarando Grupos de Filtro (Filter Sets)

Para utilizar grupos de filtro em uma subclasse de uma visualização genérica do NetBox (como `ObjectListView` ou `BulkEditView`), defina o atributo `filterset` na classe de visualização (view class):

```python
# views.py
from netbox.views.generic import ObjectListView
from .filtersets import MyModelFilterSet
from .models import MyModel

class MyModelListView(ObjectListView):
    queryset = MyModel.objects.all()
    filterset = MyModelFilterSet
```

Para habilitar um grupo de filtro no endpoint da API REST, configure o atributo `filterset_class` na visualização (view) da API:

```python
# api/views.py
from myplugin import models, filtersets
from . import serializers

class MyModelViewSet(...):
    queryset = models.MyModel.objects.all()
    serializer_class = serializers.MyModelSerializer
    filterset_class = filtersets.MyModelFilterSet
```

## Classes de Filtro

### TagFilter

A classe `TagFilter` está disponível para todosos modelos que suportam associação de tag (os que herdam de `NetBoxModel` ou `TagsMixin`). Esse filtro cria uma subclasse do `ModelMultipleChoiceFilter` para trabalhar com a classe `TaggedItem`.

```python
from django_filters import FilterSet
from extras.filters import TagFilter

class MyModelFilterSet(FilterSet):
    tag = TagFilter()
```
