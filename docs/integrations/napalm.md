# NAPALM

O NetBox suporta integração com a biblioteca de [automação NAPALM](https://github.com/napalm-automation/napalm). O NAPALM permite que o NetBox sirva como um proxy de dados operacionais, obtendo os dados "ao vivo" (live data) dos dispositivos da rede, retornando ao requisitor via API REST. Observe que o NetBox não suporta qualquer dados NAPALM localmente.

O NetBox UI (interface web) mostra tabs para o status, vizinhos LLDP e a configuração abaixo a visualização do dispositivo se as seguintes condições estiverem em conformidade:

* O status do Dispositivo está como "Active"
* Um endereço IP primário foi atribuído ao dispositivo
* Uma plataforma com driver NAPALM foi atribuído
* O usuário está autenticado com a permissão `dcim.napalm_read_device`

!!! note Observação

    Para habilitar essa integração, a biblioteca do NAPALM deve estar instalada. Verifique o [processo de instalação](../../installation/3-netbox/#napalm) para mais informações.

Abaixo está um exemplo de requisição via API REST e sua resposta:

```no-highlight
GET /api/dcim/devices/1/napalm/?method=get_environment

{
    "get_environment": {
        ...
    }
}
```

!!! note Observação

    Para realizar uma requisição NAPALM utilizando a API REST, um usuário do NetBox deve ter atrelado a permissão de ação `napalm_read` para o tipo de dispositivo. 

## Autenticação (Authentication)

Por padrão, os parâmetros de configuração [`NAPALM_USERNAME`](../configuration/napalm.md#napalm_username) e [`NAPALM_PASSWORD`](../configuration/napalm.md#napalm_password) serão usados para autenticação do NAPALM. Eles podem sobrepor uma chamada de API individual especificando os cabeçalhos `X-NAPALM-Username` e `X-NAPALM-Password`.

```
$ curl "http://localhost/api/dcim/devices/1/napalm/?method=get_environment" \
-H "Authorization: Token $TOKEN" \
-H "Content-Type: application/json" \
-H "Accept: application/json; indent=4" \
-H "X-NAPALM-Username: foo" \
-H "X-NAPALM-Password: bar"
```

## Suporte de Método (Method Support)

A lista de métodos NAPALM suportados dependem do [driver do NAPALM](https://napalm.readthedocs.io/en/latest/support/index.html#general-support-matrix) configurados para a plataforma (platform) do dispositivo. Porque não existe um mecanismo granular para limitar requisições com potenciais disruptivos (que podem prejudicar a rede), o NetBox suporta somente método do tipo [get](https://napalm.readthedocs.io/en/latest/support/index.html#getters-support-matrix) (somente leitura / read-only).

## Múltiplos Métodos

É possível requisitar a saída (output) de múltiplos métodos NAPALM em uma única requisição API ao passar diversos parâmetros `method`. Por exemplo:

```no-highlight
GET /api/dcim/devices/1/napalm/?method=get_ntp_servers&method=get_ntp_peers

{
    "get_ntp_servers": {
        ...
    },
    "get_ntp_peers": {
        ...
    }
}
```

## Argumentos Opcionais

O comportamento dos drivers do NAPALM podem ser justados de acordo os [argumentos opcionais](https://napalm.readthedocs.io/en/latest/support/index.html#optional-arguments). NetBox expõe esses argumentos utilizando os cabeçalhos prefixados com `X-NAPALM-`. Por exemplo, a porta SSH é mudada para 2222 nesta chamada API:

```
$ curl "http://localhost/api/dcim/devices/1/napalm/?method=get_environment" \
-H "Authorization: Token $TOKEN" \
-H "Content-Type: application/json" \
-H "Accept: application/json; indent=4" \
-H "X-NAPALM-port: 2222"
```