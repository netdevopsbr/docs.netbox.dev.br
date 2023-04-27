<<<<<<< HEAD
# Tarefas de Fundo (Background Taks)

O NetBox suporta o enfileiramento de tarefas que precisam ser realizadas no background, desacoplada do ciclo de request-response (requisição-reposta), usando a biblioteca [Python RQ](https://python-rq.org/). Três tarefas de queue (fila) de diferentes prioridades são definidas por padrão:

* Alta (High)
* Padrão (Default)
* Baixa (Low)

Qualquer tarefa na fila de prioridade "alta" (high) são completadas antes da fila "padrão" (default), e qualquer tarefa na fila padrão é completada antes da fila "baixa" (low)

Plugins podem também adicionar filas customizadas conforme sua própria necessidade utilizando o atributo `queues` abaixo da classe `PluginConfig`. Um exemplo está incluso abaixo:

```python
class MyPluginConfig(PluginConfig):
    name = 'meu_plugin'
=======
# Tarefas de Background

!!! info

    **English (en):** This page was not translated yet!
    **Portuguese (pt-br):** Essa página não foi traduzida ainda!

NetBox supports the queuing of tasks that need to be performed in the background, decoupled from the request-response cycle, using the [Python RQ](https://python-rq.org/) library. Three task queues of differing priority are defined by default:

* High
* Default
* Low

Any tasks in the "high" queue are completed before the default queue is checked, and any tasks in the default queue are completed before those in the "low" queue.

Plugins can also add custom queues for their own needs by setting the `queues` attribute under the PluginConfig class. An example is included below:

```python
class MyPluginConfig(PluginConfig):
    name = 'myplugin'
>>>>>>> e06ef5523ba15ec31b7ed58bf5799b98023831bc
    ...
    queues = [
        'foo',
        'bar',
    ]
```

<<<<<<< HEAD
A classe `PluginConfig` acima cria duas filas (queues) customizadas com os seguintes nomes `meu_plugin.foo` e `meu_plugin.bar`. (O nome do plugin é salvo como um prefixo em cada fila para evitar conflito entre diferentes plugins.)


!!! warning Configurando processo de RQ workers

    Por padrão, os processos RQ worker do Netbox apenas servem as filas high, default e low. Plugins que queiram introduzir filas customizadas devem orientar seus usuários para tanto reconfigurar o worker padrão, ou odar um worker dedicado especificando as filas necessárias. Por exemplo:

=======
The PluginConfig above creates two custom queues with the following names `my_plugin.foo` and `my_plugin.bar`. (The plugin's name is prepended to each queue to avoid conflicts between plugins.)

!!! warning "Configuring the RQ worker process"
    By default, NetBox's RQ worker process only services the high, default, and low queues. Plugins which introduce custom queues should advise users to either reconfigure the default worker, or run a dedicated worker specifying the necessary queues. For example:
    
>>>>>>> e06ef5523ba15ec31b7ed58bf5799b98023831bc
    ```
    python manage.py rqworker my_plugin.foo my_plugin.bar
    ```
