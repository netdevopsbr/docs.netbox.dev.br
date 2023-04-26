# Tables

NetBox employs the [`django-tables2`](https://django-tables2.readthedocs.io/) library for rendering dynamic object tables. These tables display lists of objects, and can be sorted and filtered by various parameters.

## NetBoxTable

To provide additional functionality beyond what is supported by the stock `Table` class in `django-tables2`, NetBox provides the `NetBoxTable` class. This custom table class includes support for:

* User-configurable column display and ordering
* Custom field & custom link columns
* Automatic prefetching of related objects

It also includes several default columns:

* `pk` - A checkbox for selecting the object associated with each table row (where applicable)
* `id` - The object's numeric database ID, as a hyperlink to the object's view (hidden by default)
* `actions` - A dropdown menu presenting object-specific actions available to the user

### Example

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

### Table Configuration

The NetBoxTable class features dynamic configuration to allow users to change their column display and ordering preferences. To configure a table for a specific request, simply call its `configure()` method and pass the current HTTPRequest object. For example:

```python
table = MyModelTable(data=MyModel.objects.all())
table.configure(request)
```

This will automatically apply any user-specific preferences for the table. (If using a generic view provided by NetBox, table configuration is handled automatically.)

## Columns

The table column classes listed below are supported for use in plugins. These classes can be imported from `netbox.tables.columns`.

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
