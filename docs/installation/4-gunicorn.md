# Gunicorn

Como a maioria das aplicações Django, o NetBox roda como uma [aplicação WSGI](https://en.wikipedia.org/wiki/Web_Server_Gateway_Interface). Essa documentação mostra como instalar e configurar o [gunicorn](https://gunicorn.org/) (que é automaticamente instalado com o NetBox) para ese papel, no entanto outros servidores WSGI estão disponíveis e devem funcionar de forma similar. [uWSGI](https://uwsgi-docs.readthedocs.io/en/latest/) é alternativa mais popular.

## Configuração

NetBox possui um arquivo de configuração padrão para o gunicorn. Para utilizá-lo, copie `/opt/netbox/contrib/gunicorn.py` para `/opt/netbox/gunicorn.py`. (Nós fazemos uma cópia desse arquivo além de apenas apontar para o diretório diretamente para garantir que qualquer mudança local feita não seja sobscrita por um upgrade futuro.)

```no-highlight
sudo cp /opt/netbox/contrib/gunicorn.py /opt/netbox/gunicorn.py
```

Enquanto que a configuração fornecida é suficiente para a maioria das instalações inicias, você pode querer editar esse arquivo e alterar o endereço IP e número de porta do Gunicorn, ou ainda realizar ajustes relacionados com a performance. Veja a [documentação do Gunicorn](https://docs.gunicorn.org/en/stable/configure.html) para os parâmetros de configuração disponíveis.

## Configuração do systemd

We'll use systemd to control both gunicorn and NetBox's background worker process. First, copy `contrib/netbox.service` and `contrib/netbox-rq.service` to the `/etc/systemd/system/` directory and reload the systemd daemon.

Nós iremos utilizar o systemd para controlar tanto o gunicorn quanto os processos de fundo (background) do NetBox, copie `contrib/netbox.service` e `contrib/netbox-rq.service` para o diretório `/etc/systemd/system` e recarregue o deamon do systemd.

!!! warning Verifique o usuário e a associação do grupo

    Os arquivos de configuração de serviço empacotados com o NetBox assumem que o serviço irá rodar com tanto o usuário e grupo nomeado de `netbox`. Se isso difere da sua instalação, certifique de atualizar os arquivos de serviço para estarem em conformidade com seu ambiente.

```no-highlight
sudo cp -v /opt/netbox/contrib/*.service /etc/systemd/system/
sudo systemctl daemon-reload
```

Então, inicie os serviços `netbox` e o `netbox-rq` e habilite-os para initiar no momento do boot:

```no-highlight
sudo systemctl start netbox netbox-rq
sudo systemctl enable netbox netbox-rq
```

Pode pode utilizar o comando `systemctl status netbox`para verificar que o serviço WSGI está rodando:

```no-highlight
systemctl status netbox.service
```

Você deve ver uma saída (output) similar a:

```no-highlight
● netbox.service - NetBox WSGI Service
     Loaded: loaded (/etc/systemd/system/netbox.service; enabled; vendor preset: enabled)
     Active: active (running) since Mon 2021-08-30 04:02:36 UTC; 14h ago
       Docs: https://docs.netbox.dev/
   Main PID: 1140492 (gunicorn)
      Tasks: 19 (limit: 4683)
     Memory: 666.2M
     CGroup: /system.slice/netbox.service
             ├─1140492 /opt/netbox/venv/bin/python3 /opt/netbox/venv/bin/gunicorn --pid /va>
             ├─1140513 /opt/netbox/venv/bin/python3 /opt/netbox/venv/bin/gunicorn --pid /va>
             ├─1140514 /opt/netbox/venv/bin/python3 /opt/netbox/venv/bin/gunicorn --pid /va>
...
```

!!! note Obseervação

    Se o serviço do NetBox falhar para iniciar, utilize o comando `journalctl -eu netbox` para verificar as mensagens de log que indicam qual é o problema.

Uma vez que você tenha verificado que os workers do WSGI estão online e rodando corretamente, prossiga para a configuração do servidor HTTP.