# REST API

Os plugins podem declarar endpoints customizados da API REST do NetBox para obter e manipular modelos ou outros dados. Esse com compartamento é bem similar às visualizações (views), exceto que ao invés de renderizar um conteúdo arbitrário usando um template, os dados são retornados no formato JSON utilizando um serializador (serializer).

Falando de forma geral, não há muitos componentes específicos do NetBox para impletar a funcionalidade de API REST e mum Plugin. O NetBox utiliza o [Django REST Framework](https://www.django-rest-framework.org/) (DRF) para sua API REST, enquanto que os autores de plugin podem replicar facilmente os padrões encontrados na implementação do NetBox. Alguns breves exemplos são incluídos abaixo para referência.

## Layout do Código

A abordagem recomendada é separar a API dos serializadores (serializers), visualizações (views), e URLs em módulos separados sobre o diretório `/api` para manter as coisas limpas, particularmente para projetos maiores. O arquivo `api/__init__.py` pode importar componentes relevantes de cada submódulo para permitir todos os componentes doa API diretamente de qualquer lugar. No entanto, isso é meramente uma conveção e não é estritamente obrigatória.

```no-highlight
project-name/
  - plugin_name/
    - api/
      - __init__.py
      - serializers.py
      - urls.py
      - views.py
    ...
```

## Serializador (Serializers)

### Serializadores de Modelos (Model Serializers)

Serializadores são responsáveis por converter objetos Python em dados JSON de forma conveniente aos consumidores (usuários) e vice-versa. O NetBox fornece a classe `NetBoxModelSerializer` para ser utilizada pelo plugin para lidar com a associação de tags e dados de campos customizados. (As características e funções disponíveis podem ser inclusas da mesma forma nas classes `CustomFieldModelSerializer` e `TaggableModelSerializer`.)

#### Exemplo

Para criar um serializador (serializer) para o modelo do plugin, faça uma subclasse do `NetBoxModelSerializer` em `api/serializer.py`. Especifique a classe do modelo e os campos a serem inclusos dentro da classe de serializador `Meta`. É geralmente indicado a incluir o atributo `url` em cada serializador. Irá renderizar o link diretamente para acessar o objeto sendo renderizado.

```python
# api/serializers.py
from rest_framework import serializers
from netbox.api.serializers import NetBoxModelSerializer
from my_plugin.models import MyModel

class MyModelSerializer(NetBoxModelSerializer):
    url = serializers.HyperlinkedIdentityField(
        view_name='plugins-api:myplugin-api:mymodel-detail'
    )

    class Meta:
        model = MyModel
        fields = ('id', 'foo', 'bar', 'baz')
```

### Serializadores Aninhados (Nested Serializer)

Há dois casos que torna-se geralmente desejado mostrar apenas uma mínima representação do objeto:

1. Ao mostrar um objeto relacionado ao que está sendo visto (por exemplo, a região que o site está associado)
2. Ao listar vários objetos utilizando o modo "brief" (resumido)

Para acomodá-los, é recomendado criar um serializador aninhado acompanhado do serializador completo de cada modelo. O NetBox fornece a classe `WritableNestedSerializer` para somente esse propósito. Essa classe aceita o valor de primary key na escrita, mas mostra a representação do objeto para ser lido pelas requisições. Inclui também uma tributo de leitura chamado `display` que expressa a representação do objeto.

#### Exemplo

```python
# api/serializers.py
from rest_framework import serializers
from netbox.api.serializers import WritableNestedSerializer
from my_plugin.models import MyModel

class NestedMyModelSerializer(WritableNestedSerializer):
    url = serializers.HyperlinkedIdentityField(
        view_name='plugins-api:myplugin-api:mymodel-detail'
    )

    class Meta:
        model = MyModel
        fields = ('id', 'display', 'foo')
```

## Viewsets (Grupos de Visualizações)

Como é na interface do usuário, a visualização de uma API REST lida com a lógica de mostrar e interagir com os objetos NetBox. O NetBox fornece a classe `NetBoxModelViewSet` que extende a classe `ModelViewSet` nativa da DRF para lidar com operações em grupo (bulk) e validação de objeto.

Diferente da interface do usuário, normalmente apenas um grupo de visualização (view set) é exigida por modelo: Essa visualização lida com todos os tipos de requisição (`GET`, `POST`, `DELETE`, etc.).

### Exemplo

Para criar um grupo de visualização (viewset) para o modelo de um plugin, crie uma subclasse de `NetBoxModelViewSet` em `api/views.py` e defina os atributos `queryset` e `serializer_class`.

```python
# api/views.py
from netbox.api.viewsets import ModelViewSet
from my_plugin.models import MyModel
from .serializers import MyModelSerializer

class MyModelViewSet(ModelViewSet):
    queryset = MyModel.objects.all()
    serializer_class = MyModelSerializer
```

## Roteadores (Routers)

Roteadores mapeiam a URL com as visualizações da API REST (endpoints). O NetBox não fornece qualquer componente customizado para isso; a classe [`DefaultRouter`](https://www.django-rest-framework.org/api-guide/routers/#defaultrouter) fornecida pelo DRF deve ser suficiente para a maioria dos casos.

Roteadores podem ser expostas em `api/urls.py`. Esse arquivo **deve** definir uma variável nomeada de `urlpatterns`.

### Exemplo

```python
# api/urls.py
from netbox.api.routers import NetBoxRouter
from .views import MyModelViewSet

router = NetBoxRouter()
router.register('my-model', MyModelViewSet)
urlpatterns = router.urls
```

Isso irá fazer a visualização (view) do plugin acessível por `/api/plugins/my-plugin/my-model/`.

!!! warning Aviso

    Os exemplos fornecidos aqui tem a intenção de servir uma referência mínima de implementação. A documentação não inclui autenticação, performance, ou uma variedade de outras preocupações que o autor de um plugin pode ter que resolver.
