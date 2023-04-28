# Modelos da Banco de Dados

## Criando Modelos (Models)

Se o seu plugin for introduzir um novo tipo de objet ono NetBox, você provável vai querer criar um [modelo Django](https://docs.djangoproject.com/en/stable/topics/db/models/) para isso. Um modelo é essencialmente a representação de uma tabela do banco de dados, com atributos que representam colunas individuais. Instâncias do modelo (objetos) podem ser criados, manipulados e deletados utilizando [queries](https://docs.djangoproject.com/en/stable/topics/db/queries/). Modelos devem ser definidos dentro do arquivo chamado de `models.py`.

Abaxo está um exemplo do arquivo `models.py` contendo o modelo com dois campos de texto (caracteres):

```python
from django.db import models

class MyModel(models.Model):
    foo = models.CharField(max_length=50)
    bar = models.CharField(max_length=50)

    def __str__(self):
        return f'{self.foo} {self.bar}'
```

Todo modelo inclui por padrão uma chave primária numérica (primary key). Esse valor é gerado automaticamente pelo banco de dados, e pode ser referenciado como `pk` ou `id`.

## Habilitando as Funções do NetBox

Os podemos do plugin podem fazer utilizar certas características e funções do NetBox ao herdar a classe `NetBoxModel` do NetBox. Essa classe extende o modelo do plugin para habilitar essas características únicas ao NetBox, incluindo:

* Registro de Log (Change Logging)
* Campos Customizados
* Links Customizados
* Validação Customizada
* Exportação de Templates
* Journaling
* Tags
* Webhooks

Essa classe performa duas funções cruciais:

1. Aplica qualquer campo, método e/ou atributos necessários para a operação destas funções e características.
2. Registra o modelo no NetBox para utilizar essas funções e características.

Simplesmente crie uma subclasse do `NetBoxModel` quando for definir um modelo dentro do seu plugin:

```python
# models.py
from django.db import models
from netbox.models import NetBoxModel

class MyModel(NetBoxModel):
    foo = models.CharField()
    ...
```

### Propriedades do NetBoxModel

#### `docs_url`

Esse atributo especifica a URL pela a qual a documentação deste modelo pode ser alcançada. Por padrão, ela retornará `/static/docs/models/<app_label>/<model_name>/`. Modelos de Plugin podem sobrepor isso com uma URL customizada. Por exemplo, você pode querer direcionar o usuário para a sua própria documentação hospedada em [ReadTheDocs](https://readthedocs.org/).

### Habilitando as Funções e Características Individualmente

Se você preferir, ao invés, habilitar somente um subgrupo dessas características do modelo do plugim o NetBox fornece uma classe "mix-in" para cada característica. Você pode criar uma subclasse de cada uma individualmente ao definir um modelo. (Seu modelo também precisará herdar a classe nativa do Django `Model`.)

Por exemplo, se você quiser suportar somente tags e templates de exportação, nós herdamos as classes `ExportTemplateMixin` e `TagsMixin`, e classe `Model` do Django. (Herdar __todas__ os mixins é mesma coisa que criar uma subclasse de `NetBoxModel`.)


```python
# models.py
from django.db import models
from netbox.models.features import ExportTemplatesMixin, TagsMixin

class MyModel(ExportTemplatesMixin, TagsMixin, models.Model):
    foo = models.CharField()
    ...
```

## Migrações do Banco de Dados

Uma vez que você tenha completado de definir os modelos do seu plugin, você precisará criar um esquema de migração do banco de dados. Um arquivo de migração é essencialmente um grupo de instruções para manipular o banco de dados do PostgreSQL para suportar seu novo modelo, ou altera um modelo existente. Criar migrações podem normalmente serem feitas automaticamente pelo comando de gerência `makemigrations` do Django. (Certifique-se de seu plugin foi instalado e habilite-o, de outra forma, não será encontrado.)

!!! note Habilitando o Modo de Desenvolvedor

    O NetBox força uma medidade segurança sobre o comando `makemigrations` para proteger que os usuários regulares (normais) criem um esquema de migração errado. Para habilitar esse comando no desenvolvimento de plugins, configure `DEVELOPER=True` no arquivo do NetBox `configuration.py`.

```no-highlight
$ ./manage.py makemigrations my_plugin 
Migrations for 'my_plugin':
  /home/jstretch/animal_sounds/my_plugin/migrations/0001_initial.py
    - Create model MyModel
```

Agora, nós podemos aplicação a mgiração do banco de dados com o comando `migrate`:

```no-highlight
$ ./manage.py migrate my_plugin
Operations to perform:
  Apply all migrations: my_plugin
Running migrations:
  Applying my_plugin.0001_initial... OK
```

Para mais informações sobre a migração do banco de dados, veja a [documentação do Django](https://docs.djangoproject.com/en/stable/topics/migrations/).

## Referência das Funções dos Mixins

!!! warning Aviso

    Note que somente a classe que aparece na documentação que é suportada. Embora outras classes possam estar presentes dentro do módulo `features`, eles não são suportados para uso dos plugins.

::: netbox.models.features.ChangeLoggingMixin

::: netbox.models.features.CloningMixin

::: netbox.models.features.CustomLinksMixin

::: netbox.models.features.CustomFieldsMixin

::: netbox.models.features.CustomValidationMixin

::: netbox.models.features.ExportTemplatesMixin

::: netbox.models.features.JournalingMixin

::: netbox.models.features.TagsMixin

::: netbox.models.features.WebhooksMixin

## Grupos de Escolha (Choice Sets)

Para que cada campo do modelo de seção suporte um ou mais valores com uma lista de escolhas pré-definidas, NetBox fornece a classe de utilidades `ChoiceSet`. Pode ser usado no lugar de um tuple de escolhas regular para fornecer funcionalidades melhoradas, como configuração dinâmica e coloração. (Veja a [documentação do Django](https://docs.djangoproject.com/en/stable/ref/models/fields/#choices) sobre o parâmetro `choices` para os campos de modelo suportados.)

To define choices for a model field, subclass `ChoiceSet` and define a tuple named `CHOICES`, of which each member is a two- or three-element tuple. These elements are:
Para definir as escolhas de um campo do modelo, crie uma subclasse de `ChoiceSet` e defina uma tuple com o nome de `CHOICES`, na qual cada membro é uma tupla de dois ou três elementos. Esses elementos são:

* O valor do banco de dados
* Uma tag (label) correspondente para ser lida por humanos
* Uma cor associada (opcional)

Um exemplo completo é fornecido abaixo.

!!! note Observação

    Autores podem achar útil declarar cada um dos valores do banco de dados como uma constante dentro da classe e referenciá-las dentro dos membros de `CHOICES`. Essa convenção permite que os valores sejam referenciados fora da classe, no entanto não é estritamente obrigatório.

### Configuração Dinâmica

Algumas escolhas de campos do mudelo podem ser configurados pelo administrador. Por exemplo, os valores padrões do campo do modelo do Site `status` pode ser substituído ou suplementado com escolhas customizadas. Para habilitar a configuração dinâmica para a subclasse de um ChoiceSet, defina a `key` como um texto (string) especificando o modelo e o nome do campo ao qual se aplica. Por exemplo:

```python
from utilities.choices import ChoiceSet

class StatusChoices(ChoiceSet):
    key = 'MyModel.status'
```

Para extender ou substituir os valores padrões para o grupo de escolhas, o administrador do NetBox pode referenciá-lo no parâmetro de configuração em [`FIELD_CHOICES`](../../configuration/data-validation.md#field_choices). Por exemplo, o campo de `status` no `MyModel` dentro de `my_plugin` iria ser referenciado como:

```python
FIELD_CHOICES = {
    'my_plugin.MyModel.status': (
        # Escolhas customizadas
    )
}
```

### Exemplo

```python
# choices.py
from utilities.choices import ChoiceSet

class StatusChoices(ChoiceSet):
    key = 'MyModel.status'

    STATUS_FOO = 'foo'
    STATUS_BAR = 'bar'
    STATUS_BAZ = 'baz'

    CHOICES = [
        (STATUS_FOO, 'Foo', 'red'),
        (STATUS_BAR, 'Bar', 'green'),
        (STATUS_BAZ, 'Baz', 'blue'),
    ]
```

!!! warning Aviso

    Para configuração dinâmica funcionar corretamente, `CHOICES` devem ser uma lista mutáveis, no lugar de uma tuple.

```python
# models.py
from django.db import models
from .choices import StatusChoices

class MyModel(models.Model):
    status = models.CharField(
        max_length=50,
        choices=StatusChoices,
        default=StatusChoices.STATUS_FOO
    )
```