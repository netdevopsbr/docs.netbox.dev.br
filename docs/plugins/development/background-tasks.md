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

A classe `PluginConfig` acima cria duas filas (queues) customizadas com os seguintes nomes `meu_plugin.foo` e `meu_plugin.bar`. (O nome do plugin é salvo como um prefixo em cada fila para evitar conflito entre diferentes plugins.)


!!! warning Configurando processo de RQ workers

    Por padrão, os processos RQ worker do Netbox apenas servem as filas high, default e low. Plugins que queiram introduzir filas customizadas devem orientar seus usuários para tanto reconfigurar o worker padrão, ou odar um worker dedicado especificando as filas necessárias. Por exemplo:

    ```
    python manage.py rqworker my_plugin.foo my_plugin.bar
    ```
