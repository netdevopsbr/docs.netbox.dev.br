<<<<<<< HEAD
# Forms

## Classes de Formulários

O NetBox fornece várias classes formulário base para serem utilizadas pelos plugins.

| Classes de Formulários     | Propósito                                       |
|----------------------------|-------------------------------------------------|
| `NetBoxModelForm`          | Criar/editar objetos individuais                |
| `NetBoxModelImportForm`    | Importar objetos de dados CSV em grupo          | 
| `NetBoxModelBulkEditForm`  | Editar múltiplos objetos simultaneamente        | 
| `NetBoxModelFilterSetForm` | Filtrar objetos dentro da visualização de lista | 

### `NetBoxModelForm`

É o formulário base para criar e editar modelos do NetBox. Ele extende o `ModelForm` do Django para adicionar suporte às tags e campos customizados.

| Atributo    | Descrição                                                              |
|-------------|------------------------------------------------------------------------|
| `fieldsets` | Uma tuple de tuplas duplas definindo o layout do formulário (opcional) | 

**Exemplo**
=======
# Formulários

!!! info

    **English (en):** This page was not translated yet!
    **Portuguese (pt-br):** Essa página não foi traduzida ainda!

## Form Classes

NetBox provides several base form classes for use by plugins.

| Form Class                 | Purpose                              |
|----------------------------|--------------------------------------|
| `NetBoxModelForm`          | Create/edit individual objects       |
| `NetBoxModelImportForm`    | Bulk import objects from CSV data    |
| `NetBoxModelBulkEditForm`  | Edit multiple objects simultaneously |
| `NetBoxModelFilterSetForm` | Filter objects within a list view   |

### `NetBoxModelForm`

This is the base form for creating and editing NetBox models. It extends Django's ModelForm to add support for tags and custom fields.

| Attribute   | Description                                                 |
|-------------|-------------------------------------------------------------|
| `fieldsets` | A tuple of two-tuples defining the form's layout (optional) |

**Example**
>>>>>>> e06ef5523ba15ec31b7ed58bf5799b98023831bc

```python
from dcim.models import Site
from netbox.forms import NetBoxModelForm
from utilities.forms.fields import CommentField, DynamicModelChoiceField
from .models import MyModel

class MyModelForm(NetBoxModelForm):
    site = DynamicModelChoiceField(
        queryset=Site.objects.all()
    )
    comments = CommentField()
    fieldsets = (
        ('Model Stuff', ('name', 'status', 'site', 'tags')),
        ('Tenancy', ('tenant_group', 'tenant')),
    )

    class Meta:
        model = MyModel
        fields = ('name', 'status', 'site', 'comments', 'tags')
```

<<<<<<< HEAD
!!! tip Comentários de Campos

    Se o seu formulário tem o campo `comments`, não há necessário de listá-lo; sempre irá aparecer por último na página.

### `NetBoxModelImportForm`


Esse formulário facilita a importação em grupo de novos objetos do CSV, JSON ou dados em YAML. Como é com os formulários dos modelos, você vai precisar declarar uma subclasse `Meta` especificando o `model` e `fields` associados. NetBox também fornece vários campos disponíveis para sempre importados em tipos diferentes de CSV, listados abaixo:

**Exemplo**
=======
!!! tip "Comment fields"
    If your form has a `comments` field, there's no need to list it; this will always appear last on the page.

### `NetBoxModelImportForm`

This form facilitates the bulk import of new objects from CSV, JSON, or YAML data. As with model forms, you'll need to declare a `Meta` subclass specifying the associated `model` and `fields`. NetBox also provides several form fields suitable for import various types of CSV data, listed below.

**Example**
>>>>>>> e06ef5523ba15ec31b7ed58bf5799b98023831bc

```python
from dcim.models import Site
from netbox.forms import NetBoxModelImportForm
from utilities.forms import CSVModelChoiceField
from .models import MyModel


class MyModelImportForm(NetBoxModelImportForm):
    site = CSVModelChoiceField(
        queryset=Site.objects.all(),
        to_field_name='name',
        help_text='Assigned site'
    )

    class Meta:
        model = MyModel
        fields = ('name', 'status', 'site', 'comments')
```

<<<<<<< HEAD
!!! note NetBoxModelCSVForm anterior

    A classe do formulário foi previamente nomeada de `NetBoxModelCSVForm`. Foi renomeada para na versão v3.4 do NetBox para suportar os formatos JSON e YAML em adição ao CSV. A classe `NetBoxModelCSVForm` foi mantida para compatibilidade com versões anteriores e funções iguais a `NetBoxModelImportForm`. No entanto, autores de plugins devem estar cientes que compatibilidade com versões anterior serão removidas na versão v3.5. do NetBox.

### `NetBoxModelBulkEditForm`

Esse formulário (form) facilita a edição de múltiplos objetos em grupo (bulk). Diferente do modelo do formulário, esse formulário não tem uma classe filha chamada de `Meta`, e deve explicitamente definir cada campo. Todos os campos os campos dentro de um grupo de edição são geralmente declarados com `required=False`.

This form facilitates editing multiple objects in bulk. Unlike a model form, this form does not have a child `Meta` class, and must explicitly define each field. All fields in a bulk edit form are generally declared with `required=False`.

| Atributo          | Descrição                                                                                                              |
|-------------------|------------------------------------------------------------------------------------------------------------------------|
| `model`           | O medelo do objeto sendo editado                                                                                       |
| `fieldsets`       | Uma tupla de tuplas duplas definindo o layout do formulário (opcional)                                                 | 
| `nullable_fields` | Uma tupla de campos que podem ser anulados (definidos como `empty`) usando a edição de formulários em grupo (opcional) | 

**Exemplo**
=======
!!! note "Previously NetBoxModelCSVForm"
    This form class was previously named `NetBoxModelCSVForm`. It was renamed in NetBox v3.4 to convey support for JSON and YAML formats in addition to CSV. The `NetBoxModelCSVForm` class has been retained for backward compatibility and functions exactly the same as `NetBoxModelImportForm`. However, plugin authors should be aware that this backward compatability will be removed in NetBox v3.5.

### `NetBoxModelBulkEditForm`

This form facilitates editing multiple objects in bulk. Unlike a model form, this form does not have a child `Meta` class, and must explicitly define each field. All fields in a bulk edit form are generally declared with `required=False`.

| Attribute         | Description                                                                                 |
|-------------------|---------------------------------------------------------------------------------------------|
| `model`           | The model of object being edited                                                            |
| `fieldsets`       | A tuple of two-tuples defining the form's layout (optional)                                 |
| `nullable_fields` | A tuple of fields which can be nullified (set to empty) using the bulk edit form (optional) |

**Example**
>>>>>>> e06ef5523ba15ec31b7ed58bf5799b98023831bc

```python
from django import forms
from dcim.models import Site
from netbox.forms import NetBoxModelImportForm
from utilities.forms import CommentField, DynamicModelChoiceField
from .models import MyModel, MyModelStatusChoices


class MyModelEditForm(NetBoxModelImportForm):
    name = forms.CharField(
        required=False
    )
    status = forms.ChoiceField(
        choices=MyModelStatusChoices,
        required=False
    )
    site = DynamicModelChoiceField(
        queryset=Site.objects.all(),
        required=False
    )
    comments = CommentField()

    model = MyModel
    fieldsets = (
        ('Model Stuff', ('name', 'status', 'site')),
    )
    nullable_fields = ('site', 'comments')
```

### `NetBoxModelFilterSetForm`

<<<<<<< HEAD
A classe do formulário é utilizada para renderizar o formulário para expressamente filtrar a lista de objetos. Os campos devem corresponder aos filtros definidos no grupo de filtros do modelo.

| Atributo          | Descrição                                                             |
|-------------------|-----------------------------------------------------------------------|
| `model`           | O modelo do objeto sendo editado                                      | 
| `fieldsets`       | Uma tupla de tuplas dupla definindo o layout do formulário (opcional) | 

**Exemplo**
=======
This form class is used to render a form expressly for filtering a list of objects. Its fields should correspond to filters defined on the model's filter set.

| Attribute         | Description                                                 |
|-------------------|-------------------------------------------------------------|
| `model`           | The model of object being edited                            |
| `fieldsets`       | A tuple of two-tuples defining the form's layout (optional) |

**Example**
>>>>>>> e06ef5523ba15ec31b7ed58bf5799b98023831bc

```python
from dcim.models import Site
from netbox.forms import NetBoxModelFilterSetForm
from utilities.forms import DynamicModelMultipleChoiceField, MultipleChoiceField
from .models import MyModel, MyModelStatusChoices

class MyModelFilterForm(NetBoxModelFilterSetForm):
    site_id = DynamicModelMultipleChoiceField(
        queryset=Site.objects.all(),
        required=False
    )
    status = MultipleChoiceField(
        choices=MyModelStatusChoices,
        required=False
    )

    model = MyModel
```

<<<<<<< HEAD
## Campos com Própositos Gerais

Em adição ao [campos de formulário definidos pelo Django](https://docs.djangoproject.com/en/stable/ref/forms/fields/), o NetBox fornece diferentes classes de campos para uso dentro do formulário para lidar com tipos de dados específicos. Eles podem ser importados de `utilities.forms.fields` e são documentados abaixo.
=======
## General Purpose Fields

In addition to the [form fields provided by Django](https://docs.djangoproject.com/en/stable/ref/forms/fields/), NetBox provides several field classes for use within forms to handle specific types of data. These can be imported from `utilities.forms.fields` and are documented below.
>>>>>>> e06ef5523ba15ec31b7ed58bf5799b98023831bc

::: utilities.forms.ColorField
    options:
      members: false

::: utilities.forms.CommentField
    options:
      members: false

::: utilities.forms.JSONField
    options:
      members: false

::: utilities.forms.MACAddressField
    options:
      members: false

::: utilities.forms.SlugField
    options:
      members: false

<<<<<<< HEAD
## Campos de Escolha (Choice Field)
=======
## Choice Fields
>>>>>>> e06ef5523ba15ec31b7ed58bf5799b98023831bc

::: utilities.forms.ChoiceField
    options:
      members: false

::: utilities.forms.MultipleChoiceField
    options:
      members: false

<<<<<<< HEAD
## Campos Dinâmicos de um Objeto
=======
## Dynamic Object Fields
>>>>>>> e06ef5523ba15ec31b7ed58bf5799b98023831bc

::: utilities.forms.DynamicModelChoiceField
    options:
      members: false

::: utilities.forms.DynamicModelMultipleChoiceField
    options:
      members: false

<<<<<<< HEAD
## Tipos de Campos de Conteúdo
=======
## Content Type Fields
>>>>>>> e06ef5523ba15ec31b7ed58bf5799b98023831bc

::: utilities.forms.ContentTypeChoiceField
    options:
      members: false

::: utilities.forms.ContentTypeMultipleChoiceField
    options:
      members: false

<<<<<<< HEAD
## Campos de Importação de CSV
=======
## CSV Import Fields
>>>>>>> e06ef5523ba15ec31b7ed58bf5799b98023831bc

::: utilities.forms.CSVChoiceField
    options:
      members: false

::: utilities.forms.CSVMultipleChoiceField
    options:
      members: false

::: utilities.forms.CSVModelChoiceField
    options:
      members: false

::: utilities.forms.CSVContentTypeField
    options:
      members: false

::: utilities.forms.CSVMultipleContentTypeField
    options:
      members: false
