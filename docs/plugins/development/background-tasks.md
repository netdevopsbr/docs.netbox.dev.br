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
    ...
    queues = [
        'foo',
        'bar',
    ]
```

O class `PluginConfig` acima cria duas filas (queues) customizadas com os seguintes nomes `meu_plugin.foo` e `meu_plugin.bar`. (O nome do plugin é salvo como um prefixo em cada fila para evitar conflito entre diferentes plugins.)


!!! warning "Configuring the RQ worker process"
    By default, NetBox's RQ worker process only services the high, default, and low queues. Plugins which introduce custom queues should advise users to either reconfigure the default worker, or run a dedicated worker specifying the necessary queues. For example:
    
    ```
    python manage.py rqworker my_plugin.foo my_plugin.bar
    ```
