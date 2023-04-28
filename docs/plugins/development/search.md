# Pesquisa (Search)

!!! note Observaçõ

    Essa função foi introduzida na versão v3.4 do NetBox.

Plugins podem definir e registrar seus próprios modelos para extender a funcionalidade nativa do NetBox de pesquisa. Normalmente, um plugin pode incluir um arquivo nomeado de `search.py` que armazena todos os indexes dos modelos (veja o exemplo abaixo).

```python
# search.py
from netbox.search import SearchIndex
from .models import MyModel

class MyModelIndex(SearchIndex):
    model = MyModel
    fields = (
        ('name', 100),
        ('description', 500),
        ('comments', 5000),
    )
```

Para registrar um ou mais indexes no NetBox, defina uma lista nomeada de `indexes` no inal do arquivo:

```python
indexes = [MyModelIndex]
```

!!! tip Dica

    O caminho (path) para a lista de pesquisa de indexes pode ser modificada pela configuração `search_indexes` na instância de `PluginConfig`.

::: netbox.search.SearchIndex
