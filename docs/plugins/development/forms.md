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

!!! tip Comentários de Campos

    Se o seu formulário tem o campo `comments`, não há necessário de listá-lo; sempre irá aparecer por último na página.

### `NetBoxModelImportForm`


Esse formulário facilita a importação em grupo de novos objetos do CSV, JSON ou dados em YAML. Como é com os formulários dos modelos, você vai precisar declarar uma subclasse `Meta` especificando o `model` e `fields` associados. NetBox também fornece vários campos disponíveis para sempre importados em tipos diferentes de CSV, listados abaixo:

**Exemplo**

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

A classe do formulário é utilizada para renderizar o formulário para expressamente filtrar a lista de objetos. Os campos devem corresponder aos filtros definidos no grupo de filtros do modelo.

| Atributo          | Descrição                                                             |
|-------------------|-----------------------------------------------------------------------|
| `model`           | O modelo do objeto sendo editado                                      | 
| `fieldsets`       | Uma tupla de tuplas dupla definindo o layout do formulário (opcional) | 

**Exemplo**

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

## Campos com Própositos Gerais

Em adição ao [campos de formulário definidos pelo Django](https://docs.djangoproject.com/en/stable/ref/forms/fields/), o NetBox fornece diferentes classes de campos para uso dentro do formulário para lidar com tipos de dados específicos. Eles podem ser importados de `utilities.forms.fields` e são documentados abaixo.

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

## Campos de Escolha (Choice Field)

::: utilities.forms.ChoiceField
    options:
      members: false

::: utilities.forms.MultipleChoiceField
    options:
      members: false

## Campos Dinâmicos de um Objeto

::: utilities.forms.DynamicModelChoiceField
    options:
      members: false

::: utilities.forms.DynamicModelMultipleChoiceField
    options:
      members: false

## Tipos de Campos de Conteúdo

::: utilities.forms.ContentTypeChoiceField
    options:
      members: false

::: utilities.forms.ContentTypeMultipleChoiceField
    options:
      members: false

## Campos de Importação de CSV

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
