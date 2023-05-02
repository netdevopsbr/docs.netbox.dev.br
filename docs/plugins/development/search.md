# Pesquisa (Search)

!!! note Observação

    Essa função foi introduzida na versão v3.4 do NetBox.

Os plugins podem definir e registrar seus própios modelos para extender a funcionalidade nativa de pesquisa do NetBox. Normalmente, um plugin pode incluir um arquivo chamado de `search.py` que contém todos os indexes de pesquisa para seus modelos (veja o exemplo abaixo).

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

<<<<<<< HEAD
Para registrar um ou mais indexes dentro do NetBox, defina uma lista nomeada de `indexes` no final do arquivo:
=======
Para registrar um ou mais indexes no NetBox, defina uma lista nomeada de `indexes` no inal do arquivo:
>>>>>>> 10d642ea55302d0cdd3c2087600272f4406da1df

```python
indexes = [MyModelIndex]
```

!!! tip Dica

<<<<<<< HEAD
    O caminho (path) para a lista de indexes pode ser modificada pela configuração `search_indexes` na instância de `PluginConfig`.
=======
    O caminho (path) para a lista de pesquisa de indexes pode ser modificada pela configuração `search_indexes` na instância de `PluginConfig`.
>>>>>>> 10d642ea55302d0cdd3c2087600272f4406da1df

::: netbox.search.SearchIndex
