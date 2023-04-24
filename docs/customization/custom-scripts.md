# Scrips Customizados

Scripts customizados foram introduzidos para permitir que o usuário execute uma lógica customizada dentro da interface web dentro do NetBox. Esses scripts customizados permitem que o usuário manipular diretamente e convenientemente os dados do NetBox de forma prevista. Eles podem realizar diversas tarefas, como:
- Automaticamente popular novos dispositivos e cabos em preparação para o provisionamento de um novo site
- Criar um range de prefixo reservado ou endereços IP
- Obter (fetch) de uma origem externa e importá-la para o NetBox

Scripts customizados são código Python e existem fora do código fonte do NetBox, então eles podem ser atualizados e modificados sem interferir o código de instalação em si do NetBox. E porque eles são completamente customizados, não há nenhuma limitação relacionada com oque o script pode realizar.

## Escrevendo scripts customizados

Todos os scripts customizados devem herdar a classe base `extras.scripts.Script`. Essa classe fornece as funcionalidades necessárias para gerar formulários e ativadade de log.

```python
from extras.scripts import Script

class MyScript(Script):
    ...
```

Scripts são formados basicamente por dois componentes: um set de variáveis e o método `run()`. Variáveis permitem que seu script aceite entrada (input) do usuário via uma interface web conveniente no NetBox, mas são opcionais. Se o seu script não precisa de um input do usuário, não há necessidade de definir qualquer variável.

O método `run()` é onde a lógica do script reside (Note que seu script pode ter vários métodos conforme sua necessidade: isso é peramente um ponto de invocação para o NetBox).

```
class MyScript(Script):
    var1 = StringVar(...)
    var2 = IntegerVar(...)
    var3 = ObjectVar(...)

    def run(self, data, commit):
        ...
```

O método `run()` aceita dois argumentos:
- `data` - Um dicionário (dict) contendo todas os dados da variável passadas pelo formulário na interface web.
- `commit` - Um booleano (boolean) indicando se as mudanças no banco de dados serão salvas (committed).

!!! note

    O argumento `commit` foi introduzido na versão v2.7.8 do NetBox. Compatibilidade com versões anteriores são mantidas para scritps que aceitam somente o argumento `data`, no entanto depois da versão v2.10, o NetBox irá exigir o método `run()` para todos os scripts, para aceitar ambos argumentos (Cada argumento, no entanto, pode ainda ser ignorado dentro do método)

Definir variáveis do script é opcional: Você pode criar um script somente com `run()` se o input do usuário não for necessário.

Qualquer saída (output) gerado pelo script durante a execução será mostrada abaixo de "output" dentro da interface web.

Por padrão, scripts dentro de um módulo são ordenados alfabeticamente na página que lista os scripts. Para retornar os scripts em uma ordem específica, você pode finir a variável `script_order` no final do seu módulo. A variávei `script_order` é um tuple que contém cada classe `Script` na ordem desejada. Qualquer script que for omitido da lista será posto no final da fila de listagem.

```python
from extras.scripts import Script

class MyCustomScript(Script):
    ...

class AnotherCustomScript(Script):
    ...

script_order = (MyCustomScript, AnotherCustomScript)
```

## Atributos do Módulo

### `name`

Você pode definir `name` dentro do módulo script (o arquivo Python contém um ou mais scripts) para definir o nome do módulo. Se não houver definição da variável `name`, o nome do arquivo do módulo será utilizado.

### `description`

Uma descrição sobre a função do script para ser lida pelo usuário.

### `field_order`

Por padrão, as variáveis do script serão ordenadas no formulário conforme foram definidas dentro do script. `field_order` pode ser declarada como um iterador (iterable) de nomes dos campos para determinar a ordem pela qual as variáveis serão renderizadas dentro do group default "Script Data". Qualquer campo não incluso neste iterable será listado no final do formulário. Se `fieldsets` for definido, `field_order` será ignorado. Um grupo de fieldset para "Script Execution Parameters" será adicionado ao final do formulário por padrão para o usuário.

### `fieldsets`

`fieldsets` podem ser definidos também como um iterador (iterable) para grupos de campos e seus nomes de campo determinam a ordem na qual as variávels serão agrupadas e renderizadas. Qualquer campo não incluso nesse iterador não será exibido no formulário. Se `fieldsets` for definido, `field_order` será ignorado. Um grupo fieldset para "Script Execution Parameters" será adicionado ao final do fieldset por padrão para o usuário.

Um exemplo de definição de fieldset é fornecido abaixo:

```python
class MyScript(Script):
    class Meta:
        fieldsets = (
            ('First group', ('field1', 'field2', 'field3')),
            ('Second group', ('field4', 'field5')),
        )
```

### `commit_default`

O checkbot para salvar as mudanças para o banco de dados quando o script for executado é ativado por default. Configurar `commit_default` para `False` abaixo da classe Meta do script deixa essa opção desmarcada por padrão.

### `job_timeout`

Configura o valor máximo permitido para que o script execute. Se não configurado, `RQ_DEFAULT_TIMEOUT` será utilizado.

!!! info

    Essa característica foi introduzida na versão v3.2.1 do NetBox.

## Acessando os Dados da Requisição

Detalhes da requisição HTTP corrente (a que está sendo feita pela execução do script) estão disponíveis como uma instância do atributo `self.request`. Ela pode ser utilizada para obter, por exemplo, o usuário executando o script e o endereço IP do cliente:

```python
username = self.request.user.username
ip_address = self.request.META.get('HTTP_X_FORWARDED_FOR') or \
    self.request.META.get('REMOTE_ADDR')
self.log_info(f"Running as user {username} (IP: {ip_address})...")
```

Para uma lista de completa de parâmetros da requisição disponíveis, por favor verifique a [documentação do Django](https://docs.djangoproject.com/en/stable/ref/request-response/).

## Lendos Dados de um Arquivo

A classe `Script` fornece dois métodos convenientes para ler os dados de um arquivo:

- `load_yaml`
- `load_json`

Esses dois métodos irão carregar os dados nos formatos YAML e JSON, respectivamente, dos arquivos dentro do caminho (path) local (por exemplo, `SCRIPTS_ROOT`).

## Logging

O objeto `Script` provê um conjunto de funções convenientes para registrar as mensagens de log em diferentes níveis de severidade.
- `log_debug`
- `log_success`
- `log_info`
- `log_warning`
- `log_failure`

Mensagens de log são retornadas ao usuário na hora da execução do script. Renderização em **Markdown** é suportada para as **mensagens de log**.

## Change Logging (Mudanças)

Para gerar os dados corretos do change log ao editar um objeto existente, uma snapshot (espelho) do objeto deve ser salvo antes de fazer qualquer mudança ao objeto.

```python
if obj.pk and hasattr(obj, 'snapshot'):
    obj.snapshot()

obj.property = "New Value"
obj.full_clean()
obj.save()
```

## Error Handling (Lidando com os Erros)

Ás vezes as coisas podem dar errado e o script cairá em uma `Exception`. Se isso acontecer e uma exceção não esperada for acionada pelo script customizado, a execução é abortada e um `full stack trace` é reportado.

Embora isso seja útil para debugging, em algumas siutações pode ser exigido abortar de forma limpa a execução do script customizado (porque o usuário inseriu um input errado, por exemplo) e então ter certeza que de nenhuma alteração foi feita no banco de dados. Neste caso, o script pode acionar uma exceção do tipo `AbortScript`, que irá prevenir que o stack trace seja reportado, mas ainda assim terminando a exeução do script e reportando uma mensagem de erro.

```python
from utilities.exceptions import AbortScript

if some_error:
    raise AbortScript("Some meaningful error message")
```

## Variáveis de Referência

### Opções Padrões (Default)

Todos os scripts customizados suportam as seguintes opções padrões:

- `default` - Valor padrão (default) do campo
- `description` - Uma breve descrição do campo fornecida ao usuário
- `label` - O nome do campo para ser exibido no formulário renderizado
- `required` - Indica se o campo é mandatório (obrigatório), todos os campos são mandatórios, por padrão
- `widget` - A classe que o formulário widget utiliza (para mais informações, veja a [documentação  do Django](https://docs.djangoproject.com/en/stable/ref/forms/widgets/))

### StringVar

Armazena a string de caracteres (texto). As opções são:

- `min_length` - Número mínimo de caracteres
- `max_length` - Número máximo de caracteres
- `regex` - Uma expressão regular contra qual o valor fornecido deve dar match

Note que `min_length` e `max_length` podem ser definidas para o mesmo número para o campo tenha um valor de tamanho fixo.

### TextVar

Texto arbitrário de qualquer tamanho. Renderiza um texto de múltiplas linhas do campo.

### IntegerVar

Armazena um número. As opções são:

- `min_value` - Valor mínimo
- `max_value` - Valor máximo

### BooleanVar

Valor `true/false`. Esse campo não tem opções além das opções padrões citadas.

### ChoiceVar

Um conjunto de escolhas na qual o usuário pode selecionar.

- `choices` - Uma lista de tuples (`value`, `label`) representando as escolhas disponíveis. Por exemplo:
```python
CHOICES = (
    ('n', 'North'),
    ('s', 'South'),
    ('e', 'East'),
    ('w', 'West')
)

direction = ChoiceVar(choices=CHOICES)
```

No exemplo acima, selecionar o campo definido como "North" irá submeter o valor `n`.

### MuiltiChoiceVar

Similar ao `ChoiceVar`, mas permite a seleção múltiplas escolhas.

### ObjectVar

Um objeto em particular dentro do NetBox. Cada **ObjectVar** deve esepcificar um modelo em partiular e permite que o usuário selecione uma das instâncias disponíveis. **ObjectVar** aceita diversos argumentos, que estão listadas abaixo:

- `model` - A classe do modelo
- `query_params` - Um dicionário dos parâmetros da query para usar ao obter as opções disponíveis (opcional)
- `null_option` - Uma label repesentação "null" ou escolha vazia (opcional)

Para limitar as seleções disponíveis dentro da lista, parâmetros da query adicionais podem ser passados pelo dicionário `query_params`. Por exemplo, para mostrar somente dispositivos com o status "active" (ativo):

```python
device = ObjectVar(
    model=Device,
    query_params={
        'status': 'active'
    }
)
```

Múltiplis valores podem ser especificados ao atrelar uma lista a chave do dicionário (dict key). Pode também ser possível referenciar um valor para os outros campos dentro do formulário ao colocar um sinal de dólar (dollar sign) no início do nome da variável.

```python
region = ObjectVar(
    model=Region
)
site = ObjectVar(
    model=Site,
    query_params={
        'region_id': '$region'
    }
)
```

### MultiObjectVar

Similar ao `ObjectVar`, mas permite a seleção de vários objetos.

### FileVar

Um arquivo que foi feito upload para o NetBox. Observe que os arquivos estão armazenados na memória somente durante a execução do script: não serão automaticamente salvos para uso futuro. O script é responsável para escrever o conteúdo do arquivo em disco, quando necessário.

### IPAddressVar

Um endereço IPv4 ou IPv6, sem a máscara de rede. Retorna um objeto `netaddr.IPAddress`

### IPAddressWithMaskVar

Um endereço IPv4 ou IPv6 com a máscara. Retorna um objeto `netaddr.IPNetwork` que inclui a máscara de rede.

### IPNetworkVar

Um endereço IPv4 ou IPv6 com a máscara. Retorna um objeto `netaddr.IPNetwork`. Dois atributos são disponíveis para validar a máscara fornecida:

- `min_prefix_length` - Tamanho mínimo da máscara de rede
- `max_prefix_length` - Tamanho máximo da máscara de rede

## Executando Scripts Customizados

!!! note

    Para executar um script customizado, um usuário deve atrelar a permissão de `extras.run_script`. Isso é alcançado ao atrelar a permissão do usuário (ou grupo) no objeto `Script` e especificando a ação `run` dentro da interface web de Admin como mostrado abaixo.

    ![Permissão de Rodar um Script na Admin UI](../images/admin_ui_run_permission.webp)

### Através da interface web

Scripts customiados podem rodar na interface web navegando para o script, preenchendo qualquer dados do formulário, e clicando no botão "run script". É possível agendar um script para ser executado em qualquer horário no futuro. Um script agendado pode ser cancelado ao deletar um resultado de job (tarefa) associada ao objeto.

### Através da API

Para rodar o script pela API REST, utilize uma requisição POST para o endpoint de scripts especificando os dados do formulário e o commitment. Por exemplo, para executar um script nomeado de `example.MyReport`, nós faríamos uma requisição como a seguinte:

```
curl -X POST \
-H "Authorization: Token $TOKEN" \
-H "Content-Type: application/json" \
-H "Accept: application/json; indent=4" \
http://netbox/api/extras/scripts/example.MyReport/ \
--data '{"data": {"foo": "somevalue", "bar": 123}, "commit": true}'
```

Opcionalmente, `schedule_at` pode ser passado nos dados do formulário com uma string de datetime (date e horário) para especificar o agendamento de execução de um script.

### Através da CLI

Scripts podem rodar na CLI ao utilizar o seguinte comando de gerenciamento:

```
python3 manage.py runscript [--commit] [--loglevel {debug,info,warning,error,critical}] [--data "<data>"] <module>.<script>
```

O argumento exigido `<module>.<script>` é o script a ser executado, onde `<module>` é o nome do arquivo python dentro do diretório `scripts` sem a extensão `.py` e o `<script>` é o nome da classe do script do `<module>` que será executado.

O argumento opcional `--data "<data>"` são os dados que serão enviados pelo script.

O argumento opcional `--loglevel` é exigido para que os níveis de logging sejam registrados na saída (output) da console.

O argumento opcional `--commit` irá realizar o commit (salvar) das mudanças do script no banco de dados.

## Exemplo

Abaixo está um exemplo de script que cria novos objetos para o site planejado. O usuário precisa preencher as três variáveis:
- O nome do novo site (local)
- O modelo do dispositivo (uma lista filtrada dos tipos de dispositivos definidos)
- O número de switches de acesso para serem criados

Essas variáveis são apresentadas no formulário web para serem completadas pelo usuário. Uma vez preenchida, o método `run()` do script é chamado para criar os objetos apropriados.

```python
from django.utils.text import slugify

from dcim.choices import DeviceStatusChoices, SiteStatusChoices
from dcim.models import Device, DeviceRole, DeviceType, Manufacturer, Site
from extras.scripts import *


class NewBranchScript(Script):

    class Meta:
        name = "New Branch"
        description = "Provision a new branch site"
        field_order = ['site_name', 'switch_count', 'switch_model']

    site_name = StringVar(
        description="Name of the new site"
    )
    switch_count = IntegerVar(
        description="Number of access switches to create"
    )
    manufacturer = ObjectVar(
        model=Manufacturer,
        required=False
    )
    switch_model = ObjectVar(
        description="Access switch model",
        model=DeviceType,
        query_params={
            'manufacturer_id': '$manufacturer'
        }
    )

    def run(self, data, commit):

        # Create the new site
        site = Site(
            name=data['site_name'],
            slug=slugify(data['site_name']),
            status=SiteStatusChoices.STATUS_PLANNED
        )
        site.save()
        self.log_success(f"Created new site: {site}")

        # Create access switches
        switch_role = DeviceRole.objects.get(name='Access Switch')
        for i in range(1, data['switch_count'] + 1):
            switch = Device(
                device_type=data['switch_model'],
                name=f'{site.slug}-switch{i}',
                site=site,
                status=DeviceStatusChoices.STATUS_PLANNED,
                device_role=switch_role
            )
            switch.save()
            self.log_success(f"Created new switch: {switch}")

        # Generate a CSV table of new devices
        output = [
            'name,make,model'
        ]
        for switch in Device.objects.filter(site=site):
            attrs = [
                switch.name,
                switch.device_type.manufacturer.name,
                switch.device_type.model
            ]
            output.append(','.join(attrs))

        return '\n'.join(output)
```