# Instalação do Redis

!!! info

    **English (en):** This page was not translated yet!
    **Portuguese (pt-br):** Essa página não foi traduzida ainda!

## Instalar o Redis

[Redis](https://redis.io/) é armazena os valores no formato chave-valor na memória (in-memory), onde o NetBox utiliza para fins de cache e queuing (enfileiramento). Essa seção descreve a configuração e instalação de uma instância local do Redis. Se você tiver um serviço Redis configurado, pode pular para [a próxima seção](3-netbox.md).

!!! warning "Redis v4.0 or later required"
    NetBox v2.9.0 ou maior requer o Redis v4.0 ou maior. Se sua distribuição não oferece uma versão recente o suficiente, você irá precisar fazer o build do Redis utilizando o código fonte (source code). Verifique [a documentação de instalação do Redis](https://github.com/redis/redis) para mais detalhes.

=== "Ubuntu"

    ```no-highlight
    sudo apt install -y redis-server
    ```

=== "CentOS"

    ```no-highlight
    sudo yum install -y redis
    sudo systemctl start redis
    sudo systemctl enable redis
    ```

Antes de continuar, verifique que você instalou ao menos a versão do Redis v4.0:

```no-highlight
redis-server -v
```

Você talvez queira modificar a configuração do Redis em `/etc/redis.conf` ou `/etc/redis/redis.conf`, no entanto, na maioria dos casos, a configuração padrão é suficiente.

## Verifique o Status do Serviço

Utilize o utilitário `redis-cli` para garantir que o serviço Redis é funcional:

```no-highlight
redis-cli ping
```

Se houver sucesso, você deve receber uma resposta `PONG` do servidor.