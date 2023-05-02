# Visualizações (Views)

## Escrevendo Visualizações (Views)

Se o seu plugin fornece sua própria página dentro da interface web do NetBox, você precisa definir visualizações (views). Uma view é um pedaço lógico que performa uma ação e/ou renderiza uma página quando a requisição é feita para uma URL particular. O conteúdo HTML é renderizado usando [template](./templates.md). As visualizações são normalmente definidas em `views.py`, e os padrões da URl estão em `urls.py`.

Por exemplo, vamos escrever uma visualização (view) que exibe um animal randômico e o som que o mesmo faz. Nós iremos usar uma classe genérica do Django `View` para minimizar a quantidade de código "desnecessário" para ser utilizado.

```python
from django.shortcuts import render
from django.views.generic import View
from .models import Animal

class RandomAnimalView(View):
    """
    Mostra um animal randômicamente selecionado.
    """
    def get(self, request):
        animal = Animal.objects.order_by('?').first()
        return render(request, 'netbox_animal_sounds/animal.html', {
            'animal': animal,
        })
```

A visualização retorna uma instância randômica de `Animal` do banco de dados e passa como uma variável de contexto ao renderizar um template nomeado de `animal.html`. As requisições HTTP do tipo `GET` são lidadas pelo método de visualização (views) `get()`, enquanto que requisições `POST` são lidadas pelo método `post()`.

Nosso exemplo acima é extremamente simples, mas visualizações podem ser sobre qualquer coisa. Elas são utilizadas geralmente nos lugares onde a funcionalidade do plugin irá estar. Visualizações não estão limitadas para retornar somente conteúdo HTML: Uma visualização (view) pode retornar um arquivo CSV ou imagem, por exemplo. Para mais informações sobre visualização, veja a [documentação do Django](https://docs.djangoproject.com/en/stable/topics/class-based-views/).

### Registrando a URL

Para fazer uma visualização acessível aos usuários, nós precisamos registrar uma URL para isso. Nós fazemos isso em `urls.py` ao definir uma variável em `urlpatterns` contendo uma lista de caminhos (paths).

```python
from django.urls import path
from . import views

urlpatterns = [
    path('random/', views.RandomAnimalView.as_view(), name='random_animal'),
]
```

O padrão da URL (pattern) tem três componentes:

* `route` - A porção única da URL dedicada à essa visualização
* `view` - A visualização em si
* `name` - Um breve nome utilizado para identificar o caminho (path) da URL internamente

Isso permite que nossa visualização seja acessível na URL `/plugins/animal-sounds/random/`. (Lembre, nossa classe `AnimalSoundsConfig` do nossa URL base do plugin é definida para `animal-sounds`.). Visualizar a URL deve mostrar o template base do NetBox com nosso conteúdo customizado dentro disto.

### Classes de Visualização (View Classes)

O NetBox fornece uma classe de visualização genérica (generic view) documentada abaixa para facilitar as operações comuns, como criar, visualizar, modificar e deletar objetos. Os plugins podem criar uma subclasse dessas visualizações para seu próprio uso.

| Classe da Visualização           | Descrição                                                     |
|----------------------|---------------------------------------------------------------------------|
| `ObjectView`         | Mostrar um objeto único                                                   |
| `ObjectEditView`     | Criar ou editar um objeto único                                           |
| `ObjectDeleteView`   | Deletar um objeto único                                                   |
| `ObjectChildrenView` | Uma lista de objetos filhos (child) dentro do contexto de um pai (parent) |
| `ObjectListView`     | Visualizar uma lista de objetos                                           |
| `BulkImportView`     | Importar um grupo (set) de novos objetos                                  |
| `BulkEditView`       | Editar múltiplos objetos                                                  |
| `BulkDeleteView`     | Deletar múltiplos objetos                                                 |

!!! warning Aviso

    Note que somente a classe que aparece na documentação é a suportada atualmente. Embora outras classes podem estar presentes dentro do módulo `views.generic`, elas não são mais suportadas para serem utilizadas pelos plugins.

#### Exemplo de Uso

```python
# views.py
from netbox.views.generic import ObjectEditView
from .models import Thing

class ThingEditView(ObjectEditView):
    queryset = Thing.objects.all()
    template_name = 'myplugin/thing.html'
    ...
```
## Visualizações do Objeto (Object Views)

Abaixo estão as definições da classe para a visualização do objeto no NetBox (object view). Essas visualizações lidam com as ações de CRUD para objetos individuais. A visualização, adição/modificação e remoção da visualização de cada visualização herda de `BaseObjectView`, que não tem a inteção de ser utilizada diretamente.

::: netbox.views.generic.base.BaseObjectView
    options:
      members:
        - get_queryset
        - get_object
        - get_extra_context

::: netbox.views.generic.ObjectView
    options:
      members:
        - get_template_name

::: netbox.views.generic.ObjectEditView
    options:
      members:
        - alter_object

::: netbox.views.generic.ObjectDeleteView
    options:
      members: false

::: netbox.views.generic.ObjectChildrenView
    options:
      members:
        - get_children
        - prep_table_data

## Visualizações de Objetos Múltiplos (Multi-Object Views)

Abaixo estão as definições da classe de visualização de múltiplos objetos do NetBox. Essa visualização lida com ações simultaneamente para grupos de objetos. A lista, importação, edição e remoção de visualização herda individualmente de `BaseMultiObjectView`, que não tem a inteção de ser utilizada diretamente.

::: netbox.views.generic.base.BaseMultiObjectView
    options:
      members:
        - get_queryset
        - get_extra_context

::: netbox.views.generic.ObjectListView
    options:
      members:
        - get_table
        - export_table
        - export_template

::: netbox.views.generic.BulkImportView
    options:
      members:
        - save_object

::: netbox.views.generic.BulkEditView
    options:
      members: false

::: netbox.views.generic.BulkDeleteView
    options:
      members:
        - get_form

## Características das Visualizações (Views)

Essas visualizações são fornecidades para habilitar ou aumentar certas características e funções de modelo do NetBox, como o registro de logs e journaling. Normalmente não é necessário ter uma subclasse: Elas podem ser utilizadas diretamente, por exemplo no caminho (path) da URL.

::: netbox.views.generic.ObjectChangeLogView
    options:
      members:
        - get_form

::: netbox.views.generic.ObjectJournalView
    options:
      members:
        - get_form

## Extendendo as Visualizações Nativas

### Tabs Adicionais

!!! note Observação

    Essa característica foi introduzida na versão v3.4 do NetBox

Os plugins podem "atribuit" (attach) uma visualização customizada ao modelo nativo do NetBox ao registrá-lo com `register_model_view()`. Incluir uma tab para essa visualização dentro da interface web do NetBox, declare uma instância de `TabView` nomeada de `tab`:

```python
from dcim.models import Site
from myplugin.models import Stuff
from netbox.views import generic
from utilities.views import ViewTab, register_model_view

@register_model_view(Site, name='myview', path='some-other-stuff')
class MyView(generic.ObjectView):
    ...
    tab = ViewTab(
        label='Other Stuff',
        badge=lambda obj: Stuff.objects.filter(site=obj).count(),
        permission='myplugin.view_stuff'
    )
```

::: utilities.views.register_model_view

::: utilities.views.ViewTab

### Conteúdo Extra do Template

Os plugins podem injetar conteúdo customizado em certas áreas a visualização nativa do NetBox. Ela pode ser realizada ao criar uma subclasse de `PluginTemplateExtension`, designando um modelo particular do NetBox e métodos pretendidos para renderizar conteúdo customizado. Cinco métodos estão disponíveis:

| Método              | Visualização                         | Descrição                                                |
|---------------------|--------------------------------------|----------------------------------------------------------|
| `left_page()`       | Visualização do Objeto (Object View) | Injeta conteúdo na parte esqueda da página               |
| `right_page()`      | Visualização do Objeto (Object View) | Injeta conteúdo na parte direita da página               |
| `full_width_page()` | Visualização do Objeto (Object View) | Injeta conteúdo sobre a parte inferior inteira da página |
| `buttons()`         | Visualização do Objeto (Object View) | Adiciona botões ao topo da página                        | 
| `list_buttons()`    | Visualização da Lista (ListView  )   | Adiciona botões ao topo da página                        |

Adicionalmente, um método `render()` é disponível para conveniência. Esse método aceita o nome de um template para ser renderizado e qualquer dados de contexto adicionais podem ser pasados. Isso tem o uso opcional, no entanto.

Quando a `PluginTemplateExtension` é instanciada, os dados de contexto é atrelado à `self.context`. Os dados disponíveis incluem:

* `object` - O objeto sendo visualizado (somente a visualização de objeto)
* `model` - O modelo da lista de visualização (visualização de lista somente)
* `request` - TA requisição corrente (atual)
* `settings` - Configurações Globais do NetBox
* `config` - Parâmetros de Configuração específicas do Plugin

Por exemplo, acessar `{{ request.user }}` dentro do template irá retornar o usuário atual.

Subclasses declaradas devem ser postas dentro de uma lista ou tuple para integração co mo NetBox. Por padrão, o NetBox procura por um iterável nomeado de `template_extensions` dentro do arquivo `template_content.py`. (Pode ser sobreposto pela configuração `template_extensions` para um valor customizado do `PluginConfig` do seu plugin.). Existe um exemplo abaixo.

```python
from extras.plugins import PluginTemplateExtension
from .models import Animal

class SiteAnimalCount(PluginTemplateExtension):
    model = 'dcim.site'

    def right_page(self):
        return self.render('netbox_animal_sounds/inc/animal_count.html', extra_context={
            'animal_count': Animal.objects.count(),
        })

template_extensions = [SiteAnimalCount]
```