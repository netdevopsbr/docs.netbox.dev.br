<<<<<<< HEAD
# Search
=======
# Pesquisa (Search)

!!! info

    **English (en):** This page was not translated yet!
    **Portuguese (pt-br):** Essa página não foi traduzida ainda!
>>>>>>> e06ef5523ba15ec31b7ed58bf5799b98023831bc

!!! note
    This feature was introduced in NetBox v3.4.

Plugins can define and register their own models to extend NetBox's core search functionality. Typically, a plugin will include a file named `search.py`, which holds all search indexes for its models (see the example below).

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

To register one or more indexes with NetBox, define a list named `indexes` at the end of this file:

```python
indexes = [MyModelIndex]
```

!!! tip
    The path to the list of search indexes can be modified by setting `search_indexes` in the PluginConfig instance.

::: netbox.search.SearchIndex
