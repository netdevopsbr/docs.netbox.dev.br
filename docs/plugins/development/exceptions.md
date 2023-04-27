<<<<<<< HEAD
# Exceptions

As classes de exceção listadas aqui podem ser utilizadas pelos plugins para alterar o comportamento padrão do NetBox em vários cenários.

## `AbortRequest`

NetBox fornece [váras visualizações genéricas](./views.md) e [REST API viewsets](./rest-api.md) que facilitam a criação, modificação e remoção dos objetos, seja individualmente ou em grupos. Em certas condições, pode ser desejado que os plugins interrompam essas ações e abortem a requisição de forma limpa (clean), reportando uma mensagem de erro ao usuário final ou ao usuário da API.

Por exemplo, um plugin pode proibiar a criação de um site com um nome proíbido conectando um sinal de recebedor (receiver) do Django `pre_save` para o modelo de Site:
=======
# Exceções

!!! info

    **English (en):** This page was not translated yet!
    **Portuguese (pt-br):** Essa página não foi traduzida ainda!

The exception classes listed here may be raised by a plugin to alter NetBox's default behavior in various scenarios.

## `AbortRequest`

NetBox provides several [generic views](./views.md) and [REST API viewsets](./rest-api.md) which facilitate the creation, modification, and deletion of objects, either individually or in bulk. Under certain conditions, it may be desirable for a plugin to interrupt these actions and cleanly abort the request, reporting an error message to the end user or API consumer.

For example, a plugin may prohibit the creation of a site with a prohibited name by connecting a receiver to Django's `pre_save` signal for the Site model:
>>>>>>> e06ef5523ba15ec31b7ed58bf5799b98023831bc

```python
from django.db.models.signals import pre_save
from django.dispatch import receiver
from dcim.models import Site
from utilities.exceptions import AbortRequest

PROHIBITED_NAMES = ('foo', 'bar', 'baz')

@receiver(pre_save, sender=Site)
def test_abort_request(instance, **kwargs):
    if instance.name.lower() in PROHIBITED_NAMES:
        raise AbortRequest(f"Site name can't be {instance.name}!")
```

<<<<<<< HEAD
Um erro de mensagem deve ser fornecido para criar uma `AbortRequest`.  Isso pode ser transmitido ao usuário e deve ter uma razão explicada de forma clara pela qual a requisição foi abortada, assim como uma possível solução para essa exceção.

!!! tip Considere utilizar regras customizadas de validação

    Essa exceção tem a inteção de ser utilizada para lidar com uma lógica complexa de verificação e deve ser usada com moderação. Para a validação simples de um objeto (como no exemplo acima) considere utilizar uma [regra de validação customizada](../../customization/custom-validation.md) no lugar.
=======
An error message must be supplied when raising `AbortRequest`. This will be conveyed to the user and should clearly explain the reason for which the request was aborted, as well as any potential remedy.

!!! tip "Consider custom validation rules"
    This exception is intended to be used for handling complex evaluation logic and should be used sparingly. For simple object validation (such as the contrived example above), consider using [custom validation rules](../../customization/custom-validation.md) instead.
>>>>>>> e06ef5523ba15ec31b7ed58bf5799b98023831bc
