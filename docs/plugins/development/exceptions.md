# Exceptions

As classes de exceção listadas aqui podem ser utilizadas pelos plugins para alterar o comportamento padrão do NetBox em vários cenários.

## `AbortRequest`

NetBox fornece [váras visualizações genéricas](./views.md) e [REST API viewsets](./rest-api.md) que facilitam a criação, modificação e remoção dos objetos, seja individualmente ou em grupos. Em certas condições, pode ser desejado que os plugins interrompam essas ações e abortem a requisição de forma limpa (clean), reportando uma mensagem de erro ao usuário final ou ao usuário da API.

Por exemplo, um plugin pode proibiar a criação de um site com um nome proíbido conectando um sinal de recebedor (receiver) do Django `pre_save` para o modelo de Site:

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

Um erro de mensagem deve ser fornecido para criar uma `AbortRequest`.  Isso pode ser transmitido ao usuário e deve ter uma razão explicada de forma clara pela qual a requisição foi abortada, assim como uma possível solução para essa exceção.

!!! tip Considere utilizar regras customizadas de validação

    Essa exceção tem a inteção de ser utilizada para lidar com uma lógica complexa de verificação e deve ser usada com moderação. Para a validação simples de um objeto (como no exemplo acima) considere utilizar uma [regra de validação customizada](../../customization/custom-validation.md) no lugar.