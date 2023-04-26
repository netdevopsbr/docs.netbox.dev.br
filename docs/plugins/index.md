# Plugins

Plugins são pacotes de Django app que podem ser instalados juntos com o NetBox para fornecer funcionalidades customizadas não presentes na aplicação em si. Plugins introduzem seus próprios modelos e visualizações (views), mas não podem interferir em componentes existentes. Um usuário do NetBox pode optar por instalar os plugins criados pela comunidade ou criar o seu próprio.

Plugins são suportados desde a v2.8.

## Capacidades

A arquitetura de plugins no NetBox permite:

* **Adicionar novos modelos de dados.** Um plugin pode introduzir um ou mais modelos para armazenar dados. Um modelo é essencialmente uma tabela dentro do banco de dados SQL.
* **Adicionar novas URLs e páginas web (views).** Plugins podem registrar suas próprias URLs abaixo do caminho root (path root) `/plugins` para fornecer páginas ao usuário facilmente "pesquisáveis" (browsable).
* **Adicionar conteúdo aos templates de modelos existentes.** A classe de template de conteúdo pode ser utilizada para injetar HTML customizado dentro de uma página (view) do modelo nativo do NetBox. Esse conteúdo pode aparecer no lado esquerdo, direito ou ainda no final da página.
* **Adicionar items no menu de navegação.** Cada plugin pode registrar novos links no menu de navegação. Cada link pode ter um conjunto de botões com ações específicas, similar aos botões de navegação nativos.
* **Declarar um middleware customizado.** Um middleware Django customizado pode ser registrado por cada plugin.
* **Limitar a instalação por versão do Netbox.** Um plugin pode especificar uma versão mímima e/ou máxima a qual o NetBox é compatível.

## Limitações

Seja por políticas or por limitações técnicas, a interação dos plugins com o código nativo do NetBox é restrita de certas formas. Um plugin não pode:

* **Modificando os modelos nativos.** Plugins não podem alterar, remover ou sobrepor os modelos nativos do NetBox de forma alguma. Essa regra existe para garantir a integridade dos modelos de dados nativos.
* **Registrar URLs fora de `/plugins`.** Todas as URLs dos plugins estão restritas nesse caminho (path) para previnir colisões de caminhos com as URLs do próprio NetBox ou de outros plugins.
* **Sobrepor os templates nativos.** Plugins podem injetar conteúdo adicional onde houver suporte, mas não pode manipular ou remover qualquer conteúdo nativo.
* **Modificar as configurações nativas do NetBox.** O registro de configuração é fornecida para os plugins, mas não podem alterar ou deletar as configurações nativas.
* **Disabilitar componentes nativos.** Plugins não podem disabilitar ou esconder componentes nativos do NetBox.

## Instalando Plugins

As instruções abaixo detalham o processo de instalação e como habilitar um plugin.

### Instalando o Pacote

Fazer o Download e instale o pacote do plugin utilizando sua própria orientação de instalação. Plugins publicados via PyPI são tipicamente instalados usando pip. Certifique-se de instalar o plugin dentro do ambiente virtual do Python.

```no-highlight
$ source /opt/netbox/venv/bin/activate
(venv) $ pip install <package>
```

Alternativamente, você talvez queira instalar o plugin manualmente utilizando o comando `python setup.py install`. Se você estiver desenvolvendo um plugin e quiser instalar de forma temporária, utilize o comando `python setup.py develop` no lugar.

### Habilitando o Plugin

Em `configuration.py`, adicione o nome do plugin na lista `PLUGINS`:

```python
PLUGINS = [
    'nome_do_plugin',
]
```

### Configurando o Plugin

Se o plugin precisar de qualquer configuração, defina-a em `configuration.py` no parâmetro `PLUGINS_CONFIG`. Os parâmetros de configuração disponíveis devem ser detalhados no arquivo README do plugin.

```no-highlight
PLUGINS_CONFIG = {
    'nome_do_plugin': {
        'foo': 'bar',
        'buzz': 'bazz'
    }
}
```

### Rodando as Migrações do Banco de Dados

Se o plugin introduz modelos novos do banco de dados, rode o esquema fornecido de migrações:

```no-highlight
(venv) $ cd /opt/netbox/netbox/
(venv) $ python3 manage.py migrate
```

### Coletando Arquivos Estático

Plugins podem empacotar arquivos estáticos para serem servidos diretamente pelo front end HTTP. Garanta que esses arquivos sejam copiados para o diretório root estático (static) com o comando de gerência `collectstatic`:

```no-highlight
(venv) $ cd /opt/netbox/netbox/
(venv) $ python3 manage.py collectstatic
```

### Reiniciando o Serviço WSGI

Reiniciando o serviço WSGI para carregar o novo plugin:

```no-highlight
# sudo systemctl restart netbox
```

## Removendo os Plugins

Siga os passos para completamente remover o plugin.

### Atualizando as Configurações

Remova o plugin da lista `PLUGINS` em `configuration.py`. Também remova qualquer parâmetro de configuração que seja relevante de `PLUGINS_CONFIG`.

### Removendo o Pacote Python

Utilize o `pip` para remover o plugin instalado:

```no-highlight
$ source /opt/netbox/venv/bin/activate
(venv) $ pip uninstall <package>
```

### Reinicando o Serviço WSGI

Para reiniciar o serviço WSGI:

```no-highlight
# sudo systemctl restart netbox
```

### Removendo (Drop) as Tabelas do Banco de Dados

!!! note Observação

    Esse passo é necessário somente se o plugin criou uma ou mais tabelas do banco de dados (geralmente através da introdução de novos modelos). Verifique a documentação do plugin se você estiver incerto.

Entre no shell do banco de dados do PostgreSQL para verificar se o plugin criou alguma tabela SQL. Substitua `pluginname` no exemplo abaixo pelo nome do plugin que está sendo removido.

```no-highlight
netbox=> \dt pluginname_*
                   List of relations
                   List of relations
 Schema |       Name     | Type  | Owner
--------+----------------+-------+--------
 public | pluginname_foo | table | netbox
 public | pluginname_bar | table | netbox
(2 rows)
```

!!! warning Aviso

    Tenha extremo cuidado ao remover tabelas. Usuários são fortemente encoragados de realizar um backup do seu banco de dados imediatamente antes de tomar qualquer ação.

Drop (delete) cada tabela listada para remover do banco de dados:

```no-highlight
netbox=> DROP TABLE pluginname_foo;
DROP TABLE
netbox=> DROP TABLE pluginname_bar;
DROP TABLE
```