# Validação Customizada

NetBox valida valida todo objeto antes de ser escrito no banco de dados para validar a integridade de dados. Essa validação inclui coisas como verificar a formatação correta e se as referências ao objeto são válidas. No entanto, você pode querer complementar a validação com algumas regras de sua escolha. Por exemplo, talvez você queira que todos os nomes de Site (Locais) estejam em conformidade com um padrão específico. Isso pode ser usado através de regras de custom validation (validação customizada).

## Regras de Validação Customizada

Regras de validação customizada podem ser expressadas como um mapa de atributos do modelo que definem quais atributos devem estar em conformidades. Por exemplo:

```json
{
    "name": {
        "min_length": 5,
        "max_length": 30
    }
}
```

Isso define um validador customizado que verifica se o tamanho do atributo `name` de um objeto tem ao menos 05 caracteres e não é maior que 30 caracteres. Essa validação é executada depois que o NetBox fez suas próprias validações internas.

A classe `CustomValidator` diversos tipos de validação:
- `min`: Valor mínimo
- `max`: Valor máximo
- `min_length`: Tamanho mínimo do texto (string)
- `max_length`: Tamanho máximo do texto (string)
- `regex`: Aplicação de uma [expressão regular](https://en.wikipedia.org/wiki/Regular_expression)
- `required`: Um valor deve ser **obrigatoriamente** definido.
- `prohibited`: Um valor **não deve** ser especificado.

O tipos `min`e `max` devem ser definidos como valores numéricos, enquanto que `min_length`, `max_length` e `regex` suportam valores de texto (string). Os validadores `required` e `prohibited` podem ser usados em qualquer campo e os valores devem ser passados como `True`.

!!! warning

    Tenha em mente que esses validadores meramente complementam as validações próprias do NetBox. Os validadores não irão sobrepor estas internas. Por exemplo, se certo campo do modelo é obrigatório pelo NetBox, configurar o validador para `{'prohibited': True}` não irão funcionar.

## Lógica de Validação Customizada

Pode haver casos em que os tipos de validação fornecidos são insuficientes. NetBox fornece a classe `CustomValidator` que pode ser extendida para suportar validações arbitrárias ao sobrepor o método `validate()`, e chamando o método `fail()` quando uma condição não satisfatória for detectada.

```python
from extras.validators import CustomValidator

class MyValidator(CustomValidator):

    def validate(self, instance):
        if instance.status == 'active' and not instance.description:
            self.fail("Active sites must have a description set!", field='status')
```

O método `fail()` pode opcionalmente definir um campo no qual pode associar a mensagem de erro fornecida. Se especificado, a mensagem de erro irá aparecer ao usuário associada ao campo definido. Se omitido, a mensagem de erro não será associada com nenhum campo.

## Atrelando Validadores Customizados

Validadores customizadas são associados com modelos específicos do NetBox com a configuração de parâmetro [CUSTOM_VALIDATORS](https://docs.netbox.dev/en/stable/configuration/data-validation/#custom_validators). Há três maneiras pela qual as regras de validação podem ser definidas.

1. Mapeamento via JSON (sem lógica customizada)
2. Caminho tracejado (dotted) para uma classe de validação customizada
3. Referência direta para uma classe de validação customizada

# Dados "Puro" (Plain Data)

Para casos que a lógica customizada não é necessária, é suficiente passar regras de validação como objetos compatíveis com JSON puro (plain). Essa abordagem tipicamente permite uma portabilidade da sua configuração. Por exemplo:

```python
CUSTOM_VALIDATORS = {
    "dcim.site": [
        {
            "name": {
                "min_length": 5,
                "max_length": 30,
            }
        }
    ],
    "dcim.device": [
        {
            "platform": {
                "required": True,
            }
        }
    ]
}
```

## Caminho Tracejado (Dotted Path)

Em instâncias que a classe da validação customizada é necessária, ela pode ser referenciada pelo seu Python path (relativo ao diretório de produção do NetBox)

```python
CUSTOM_VALIDATORS = {
    'dcim.site': (
        'my_validators.Validator1',
        'my_validators.Validator2',
    ),
    'dcim.device': (
        'my_validators.Validator3',
    )
}
```

Essa abordagem exige que a classe sendo instanciada seja importada diretamente pelo arquivo de configuração do Python.

```python
from my_validators import Validator1, Validator2, Validator3

CUSTOM_VALIDATORS = {
    'dcim.site': (
        Validator1(),
        Validator2(),
    ),
    'dcim.device': (
        Validator3(),
    )
}
```

!!! note

    Mesmo que seja definido apenas um validador, deve ser utilizado como um iterable.
