# Relatórios do NetBox (Reports)

O mecanismo de relatórios do NetBox é utilizado na validação de dados dentro do NetBox. Utilizar um report permite que o usuário verifque que os objetos dentro do NetBox estão de acordo com condições configuradas pelo usuário. Por exemplo, você pode escrever reports que verificam;

- Se todos os switches top-of-racks possuem uma conexão via console
- Todo o roteador tem uma interface loopback com um endereço IP configurado
- Cada descrição de interface está em conformidade com um formato específico
- Todo site tem um mínimo de VLANs configuradas
- Todos os endereços IP têm um prefixo pai (parent prefix)

...e por aí vai. Reports são completamente customizáveis, então praticamente não existe limite do que você pode testar.

## Escrevendo Reports

Reports devem ser salvados como arquivos no path (caminho) definido em `REPORTS_PATH` (que por padrão é `netbox/reports/`). Cada arquivo criado dentro desta pasta deve ser considerado como um módulo individual. Cada modulo contém um ou mais reports (classes Python) que cumpre uma função específica. A lógica para cada report é quebrada em métodos de teste (test methods) que aplicam uma pequena parcela de lógica que compõem o teste geral (overall test).

!!! warning

    O caminho (path) dos reports possuem um arquivo nomeado de `__init__.py` que registra o path como um módulo (module) Python. **Não delete esse arquivo.**

Por exemplo, nós podemos criar um módulo nomeado de `devices.py` para conter todos os reports relacionados aos dispositivos dentro do NetBox. Dentro desse módulo, nós podemos dfinir diversos reports. Cada report é definido como uma classe Python que herda (inherit) `extras.reports.Report`.

```python
from extras.reports import Report

class DeviceConnectionsReport(Report):
    description = "Validate the minimum physical connections for each device"

class DeviceIPsReport(Report):
    description = "Check that every device has a primary IP address assigned"
```

Dentro de cada classe de report, nós criamos um número de métodos de teste para executar a lógica do report. Dentro de `DeviceConnectionsReport`, por exemplo, nós queremos garantir que todos os dispositivos em produção tenham uma conexão console, uma conexão de gerência out-out-band, e duas conexões de energia.

```python
from dcim.choices import DeviceStatusChoices
from dcim.models import ConsolePort, Device, PowerPort
from extras.reports import Report


class DeviceConnectionsReport(Report):
    description = "Validate the minimum physical connections for each device"

    def test_console_connection(self):

        # Check that every console port for every active device has a connection defined.
        active = DeviceStatusChoices.STATUS_ACTIVE
        for console_port in ConsolePort.objects.prefetch_related('device').filter(device__status=active):
            if not console_port.connected_endpoints:
                self.log_failure(
                    console_port.device,
                    "No console connection defined for {}".format(console_port.name)
                )
            elif not console_port.connection_status:
                self.log_warning(
                    console_port.device,
                    "Console connection for {} marked as planned".format(console_port.name)
                )
            else:
                self.log_success(console_port.device)

    def test_power_connections(self):

        # Check that every active device has at least two connected power supplies.
        for device in Device.objects.filter(status=DeviceStatusChoices.STATUS_ACTIVE):
            connected_ports = 0
            for power_port in PowerPort.objects.filter(device=device):
                if power_port.connected_endpoints:
                    connected_ports += 1
                    if not power_port.path.is_active:
                        self.log_warning(
                            device,
                            "Power connection for {} marked as planned".format(power_port.name)
                        )
            if connected_ports < 2:
                self.log_failure(
                    device,
                    "{} connected power supplies found (2 needed)".format(connected_ports)
                )
            else:
                self.log_success(device)
```

Como você pode ver, reports são completamente customizáveis. A lógica de validação pode ser simples ou complexa, conforme sua necessidade. Note também que o atributo `description` suporta a sintaxe **markdown**, que será renderizada dentro da página de lista do report.

!!! warning

    Reports não devem alterar dados: se você utiliza métodos como `create()`, `save()`, `update()` e `delete()`, você deveria parar e reavaliar o que você está tentando fazer. Percebe que não há nenhuma garantia para alterações acidentais ou destruição de dados.


## Atributos do Report

### `description`

Uma descrição do que o report faz para ser lida por humanos.

### `job_timeout`

Defina o tempo máximo permitido de execução de um report. Se não definido, `RQ_DEFAULT_TIMEOUT` será utilizado.

!!! info

    **Essa característica foi introduzida na versão v3.2.1 do NetBox.**

## Logging

Os métodos abaixo estão disponívels para realizar o log dos resultados dentro de um report (relatório):

- log(message)
- log_success(object, message=None)
- log_info(object, message)
- log_warning(object, message)
- log_failure(object, message)

Esse registro de uma ou mais mensagens de falha será automaticamente reportada como uma falha do report em si. É recomendado realizar o log de sucesso para cada objeto que esteja sendo utilizado para que os resultados reflitam como os objetos estão sendo registrados. (A inclusão de uma mensagem de log é opcional para quando houver sucesso) Mensagens registradas com `log()` irão aparecer no resultado de um report, mas não são associadas com um objeto em particular ou status. Mensagens de log também suportam sintaxe markdown e serão renderizadas na página de resultados de um report.

Para realizar tarefas adicionais, como enviar um email ou chamar um webook, antes ou depois que um report for executado, extenda os métodos `pre_run()` e/ou `post_run()`, respectivamente. O status de report finalizado é disponível em `self.failed` e o resultado do objeto em `self.result`.

```python
from extras.reports import Report

class DeviceConnectionsReport(Report)
    pass

class DeviceIPsReport(Report)
    pass

report_order = (DeviceIPsReport, DeviceConnectionsReport)
```

## Executando Reports

!!! note

    Para executar (run) um report, o usuário deve atribuit a permissão `extras.run_report`. Isso é feito ao atrelar um usuário (ou grupo) com uma permissão dentro do objeto `Report` e especificando a ação `run` dentro da interface admin, como abaixo:

    ![Admin UI Run Report](../images/admin_ui_run_permission.png)

### Através da interface Web (Web UI)

Reports podem ser executados através da interface web navegando até **report** e clicando no botão "run report" na parte superior direita. Uma vez que o report foi executado, os resultados associados serão incluidos na visualização do relatório (report). É possível agendar um report para ser executado em um horário específico no futuro. Um reportt agendado pode ser cancelado ao deletar o o resultado da job (tarefa) associada.

### Através da API

Para executar um report utilizando a API, simplesmente execute uma requisição do tipo `POST` para seu endpoint `run`. Reports são identificados pelo seu módulo e seu nome de classe.

```
    POST /api/extras/reports/<module>.<name>/run/
```

Nosso exemplo de report acima será chamado como:


```
    POST /api/extras/reports/devices.DeviceConnectionsReport/run/
```

Opcionalmente, `schedule_at` pode ser fornecido no formulário de dados (form data) com um `datetime` no formato de `string` para agendar um script em uma data e horário especificada pelo usuário.

### Através da CLI

Reports podem ser executados pela CLI ao utilizar o seguinte comando de gerência:

```
python3 manage.py runreport <module>
```

Onde `<module>` é o nome do arquivo python no diretório `reports` sem a extensão `.py`. Um ou mais módulos de report podem ser especificados pelo comando.