# Configuração do LDAP

Esse guia explica como implementar a autenticação via LDAP utilizando um servidor externo. Autenticação do usuário irá ser via usuários do Django no caso do LDAP retornar falha.

## Instalando os Pacotes necessários

### Instalando Pacote do Sistema

No Ubuntu:

```no-highlight
sudo apt install -y libldap2-dev libsasl2-dev libssl-dev
```

No CentOS:

```no-highlight
sudo yum install -y openldap-devel
```

### Instalando django-auth-ldap

Ative o ambiente virtual do Python e instale o pacote `django-auth-ldap` utilizando o pip:

```no-highlight
source /opt/netbox/venv/bin/activate
pip3 install django-auth-ldap
```
 
Uma vez instalado, adicione os pacotes em `local_requirements.txt` para garantir que seja re-instalado durante rebuilds futuros (como Upgrades) do ambiente virutal:
Once installed, add the package to `local_requirements.txt` to ensure it is re-installed during future rebuilds of the virtual environment:

```no-highlight
sudo sh -c "echo 'django-auth-ldap' >> /opt/netbox/local_requirements.txt"
```

## Configuração

Primeiro, habilite o backend de autenticação do LDAP em `configuration.py`. (Tenha certeza de não sobescrever essa definição caso já tenha configurado o `RemoteUserBackend`.)

```python
REMOTE_AUTH_BACKEND = 'netbox.authentication.LDAPBackend'
```

Agora, crie o arquivo no mesmo diretório do `configuration.py` (normalmente `/opt/netbox/netbox/netbox/`) com o nome de `ldap_config.py`. Defina todos os parâmetros necessários abaixo em `ldap_config.py`. Documentação completa de todas as opções de configuração do `django-auth-ldap` estão inclusos na [documentação oficial do projeto](https://django-auth-ldap.readthedocs.io/).

### Configuração Geral do Servidor

!!! info Observação

    When using Active Directory you may need to specify a port on `AUTH_LDAP_SERVER_URI` to authenticate users from all domains in the forest. Use `3269` for secure, or `3268` for non-secure access to the GC (Global Catalog).

    Quando o Active Directory for utilizado, você talvez queria especificar a porta em `AUTH_LDAP_SERVER_URI` para autenticar os usuários de todos os domínios na forest (floresta). Use a porta `3269` para uso seguro, ou `3268` para acesso não seguro ao GC (Global Catalog).

```python
import ldap

# URI do Servidor
AUTH_LDAP_SERVER_URI = "ldaps://ad.example.com"

# A configuração abaixo pode ser necessária se você estiver associando com o Active Directory.
AUTH_LDAP_CONNECTION_OPTIONS = {
    ldap.OPT_REFERRALS: 0
}

# Configure a senha e DN para conta de serviço do NetBox
AUTH_LDAP_BIND_DN = "CN=NETBOXSA, OU=Service Accounts,DC=example,DC=com"
AUTH_LDAP_BIND_PASSWORD = "demo"

# Inclua essa configuração se você quiser ignorar os erros de certificado. Isso talvez seja necessário para aceitar arquivos self-signed (auto-assinados).
# Observe que isso é uma configuração específica do NetBox que define:
#     ldap.set_option(ldap.OPT_X_TLS_REQUIRE_CERT, ldap.OPT_X_TLS_NEVER)
LDAP_IGNORE_CERT_ERRORS = True

# Inclua essa configuração se você quiser validar os certificados do servidor LDAP contra o diretório de certificado CA no seu servidor
# Observe que isso é uma configuração específica do NetBox que define:
#     ldap.set_option(ldap.OPT_X_TLS_CACERTDIR, LDAP_CA_CERT_DIR)
LDAP_CA_CERT_DIR = '/etc/ssl/certs'

# Inclua essa configuração se você quiser validar os certificados do servidor LDAP contra sua própria CA (Certificate Authority).
# Observe que isso é uma configuração específica do NetBox que define:
#     ldap.set_option(ldap.OPT_X_TLS_CACERTFILE, LDAP_CA_CERT_FILE)
LDAP_CA_CERT_FILE = '/path/to/example-CA.crt'
```

STARTTLS pode ser configurado pela configuração `AUTH_LDAP_START_TLS = True` e usando o esquema da URI `ldap://`.

### Autenticação do Usuário

!!! info Observação

    Quando estiver utilizando um Windows Server 2012+, `AUTH_LDAP_USER_DN_TEMPLATE` deve ser `None`.

```python
from django_auth_ldap.config import LDAPSearch

# This search matches users with the sAMAccountName equal to the provided username. This is required if the user's
# username is not in their DN (Active Directory).
AUTH_LDAP_USER_SEARCH = LDAPSearch("ou=Users,dc=example,dc=com",
                                    ldap.SCOPE_SUBTREE,
                                    "(sAMAccountName=%(user)s)")

# If a user's DN is producible from their username, we don't need to search.
AUTH_LDAP_USER_DN_TEMPLATE = "uid=%(user)s,ou=users,dc=example,dc=com"

# You can map user attributes to Django attributes as so.
AUTH_LDAP_USER_ATTR_MAP = {
    "first_name": "givenName",
    "last_name": "sn",
    "email": "mail"
}
```

### User Groups for Permissions

!!! info
    When using Microsoft Active Directory, support for nested groups can be activated by using `NestedGroupOfNamesType()` instead of `GroupOfNamesType()` for `AUTH_LDAP_GROUP_TYPE`. You will also need to modify the import line to use `NestedGroupOfNamesType` instead of `GroupOfNamesType` .

```python
from django_auth_ldap.config import LDAPSearch, GroupOfNamesType

# Essa pesquisa deve retornar todos os grupos que o usuário pertence. django_auth_ldap usa isso para determinar a hierárquia do grupo.
AUTH_LDAP_GROUP_SEARCH = LDAPSearch("dc=example,dc=com", ldap.SCOPE_SUBTREE,
                                    "(objectClass=group)")
AUTH_LDAP_GROUP_TYPE = GroupOfNamesType()

# Define o grupo exigido para logar.
AUTH_LDAP_REQUIRE_GROUP = "CN=NETBOX_USERS,DC=example,DC=com"

# Espelha a associação de grupos do LDAP
AUTH_LDAP_MIRROR_GROUPS = True

# Defien tipos especiais de grupos de usuários. Tenha bastante cuidado ao atrelar status de superuser.
AUTH_LDAP_USER_FLAGS_BY_GROUP = {
    "is_active": "cn=active,ou=groups,dc=example,dc=com",
    "is_staff": "cn=staff,ou=groups,dc=example,dc=com",
    "is_superuser": "cn=superuser,ou=groups,dc=example,dc=com"
}

# Para permissões mais granulares, nós podemos mapear os grupos do LDAP com os grupos do Django.
AUTH_LDAP_FIND_GROUP_PERMS = True

# Realiza o cache de grupos por uma hora para reduzir o tráfego LDAP
AUTH_LDAP_CACHE_TIMEOUT = 3600

```

* `is_active` - Todos os usuários devem ser mapeados com ao menos esse grupo para habilitar autenticação. Sem isso, usuários não podem realizar o login.
* `is_staff` - Os usuários mapeados com esse grupo têm a permissão de ferramentas de administração; isso é o equivalete a habilitar o box de "staff status" manualmente ao criar um usuário. Isso não garante qualquer permissão específica.
* `is_superuser` - Usuários mapeados com esse grupo receberão o status de superuser. Superuses tem todas as permissões garantidas de forma implícita.

!!! warning Aviso

    A autenticação irá falhar se os grupos (com os nomes diferentes) não existirem no diretório LDAP.

## Resolvendo Problemas do LDAP

`systemctl restart netbox` reinicia o serviço do NetBox e ativa quaisquer alterações feitas em `ldap_config.py`. Se houver erros de sintaxe, o processo do NetBox não irá criar uma instância e erros serão registrados como mensagens de log em `/var/log/messages`.

Para resolver os problemas de busca/pesquisa do LDAP, adicione e una (merge) a seguinte confiugração [logging](../configuration/system.md#logging) em `configuration.py`

```python
LOGGING = {
    'version': 1,
    'disable_existing_loggers': False,
    'handlers': {
        'netbox_auth_log': {
            'level': 'DEBUG',
            'class': 'logging.handlers.RotatingFileHandler',
            'filename': '/opt/netbox/local/logs/django-ldap-debug.log',
            'maxBytes': 1024 * 500,
            'backupCount': 5,
        },
    },
    'loggers': {
        'django_auth_ldap': {
            'handlers': ['netbox_auth_log'],
            'level': 'DEBUG',
        },
    },
}
```

Garanta que o arquivo e caminho (path) especificados no arquivo de log existem e são "graváveis" (writable) e executáveis pela conta de serviço da aplicação. Reinicie o serviço do NetBox para tentar realizar o login ao site para registrar entradas de log ao arquivo.