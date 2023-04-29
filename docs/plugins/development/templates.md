# Templates

Templates são utilizados para renderizar o conteúdo HTML gerados pelo grupo de dados de contexto. O NetBox fornece um grupo de templates nativos para ser utilizados pelas visualizações (views) do plugin. Os autores de plugins podem extender esses templates para minimar o trabalho necessário para criar templates customizados enquanto que garante que o conteúdo produzido pelo plugin esteja de acordo com o layout e estilo do NetBox. Esses templates são escritos em [Django Template Language (DTL)](https://docs.djangoproject.com/en/stable/ref/templates/language/).

## Template File Structure Estrutura de Arquivos do Template

Os templates do plugin devem estar dentro do caminho do arquivo `templates/<plugin-name>` dentro do root do plugin. Por exemplo, se o nome do seu plugin `my_plugin` e cria um template nomeado de `foo.html`, deve ser salvo como `templates/my_plugin/foo.html`. (Você com certeza pode utilizar sub diretórios abaixo desse ponto também). Isso garante que a engine de template possa localizar o template para renderização.

## Blocos Padrões (Standard Blocks)

Os blocos de templates abaixo estão disponíveis em todos os templates.

| Nome           | Obrigatório | Descrição                                                                  |
|----------------|-------------|----------------------------------------------------------------------------|
| `title`        | Sim         | Título da página                                                           |
| `content`      | Sim         | Conteúdo da págiina                                                        |
| `head`         | -           | Conteúdo para incluir o elemento `<head>` no HTML                          |
| `footer`       | -           | Conteúdo do rodapé (footer) da página                                      |
| `footer_links` | -           | Seção de links do rodapé da página                                         |
| `javascript`   | -           | Conteúdo JavaScript para ser incluso no final do elemento `<body>` do HTML |

!!! note Observação

    Para mais informações em como o blocos de templates funcionam, consulte a [documentação do Django](https://docs.djangoproject.com/en/stable/ref/templates/builtins/#block).

## Templates Base

### layout.html

Caminho (path): `base/layout.html`

O NetBox fornece um template base para garantir uma experiência do usuário consistente, que os plugins podem extender seu próprio conteúdo. Esse template é utilizado por um propósito geral que pode ser utilizado quando uma função específica do template abaixo são suportados.

#### Blocos (Blocks)

| Nome      | Obrigatório | Descrição                               |
|-----------|----------|--------------------------------------------|
| `header`  | -        | Cabeçalho da Página                        |
| `tabs`    | -        | Tabs de navegação horizontais              |
| `modals`  | -        | Elementos de modelo (modal) do Bootstrap 5 |

#### Exemplo

Um exemplo do template do plugin que extende o `layout.html` está incluso abaixo.

```jinja2
{% extends 'base/layout.html' %}

{% block header %}
  <h1>My Custom Header</h1>
{% endblock header %}

{% block content %}
  <p>{{ some_plugin_context_var }}</p>
{% endblock content %}
```

A primeira linha do template instrui o Django a extender o template base do NetBox, e as seções `block` injetam nosso conteúdo customizado dentro dos blocos `header` e `content`.

!!! note Observação

    O Django renderiza os templates com sua própria [linguagem customizada](https://docs.djangoproject.com/en/stable/topics/templates/#the-django-template-language). Isso é muito similar ao Jinja2, no entanto há distinções importantes as quais os autores devem estar cientes. Certifique-se de se familiarizar com a linguagem template do Django antes de tentar criar seus novos templates.

## Templates Genéricos da Visualização (Generic View Templates)

### object.html

Caminho (path): `generic/object.html`

Esse template é usado pela visualização genérica `ObjectView` (generic view) para exibir apenas um objeto.

#### Blocos (Blocks)

| Nome                | Obrigatório | Descrição                                        |
|---------------------|-------------|-----------------------------------------------------| 
| `breadcrumbs`       | -           | Breadcumb da lista de items (elementos `<li>` HTML) | 
| `object_identifier` | -           | Um indentificador único (string) do objeto          | 
| `extra_controls`    | -           | Botões de ações adicionais para serem exibidos      | 
| `extra_tabs`        | -           | Tabs adicionais para serem inclusas                 | 

#### Contexto (Context)

| Nome     | Obrigatório | Descrição                               |
|----------|-------------|-----------------------------------------|
| `object` | Sim         | A instância do objeto sendo visualizada | 

### object_edit.html

Caminho (path): `generic/object_edit.html`

Esse template é utilizado pela visualização genérica (generic view) para criar ou modificar um único objeto.

#### Blocos (Blocks)
 
| Nome      | Obrigatório | Descrição                                                             |
|-----------|-------------|-----------------------------------------------------------------------|
| `form`    | -           | Conteúdo de formulário customizado (dentro do elemento HTML `<form>`) |
| `buttons` | -           | Botões do formulário para submetê-lo                                  | 

#### Contexto (Context)

| Nome         | Obrigatório | Descrição                                                        |
|--------------|----------|---------------------------------------------------------------------|
| `object`     | Sim      | A instância do objeto sendo modificada (none, se estiver criando)   | 
| `form`       | Sim      | A classe do formulário do objeto sendo criada/modificada            | 
| `return_url` | Sim      | A URL que o usuário é redirecionado depois de submeter o formulário | 

### object_delete.html

Caminho (path): `generic/object_delete.html`

Esse template é utilizado pela visualização genérica (generic view) `ObjectDeleteView` para deletar um objeto único.

#### Blocos (Blocks)

None

#### Contexto (Context)

| Nome         | Obrigatório | Descrição                                                                    |
|--------------|-------------|------------------------------------------------------------------------------|
| `object`     | Sim         | A instância do objeto sendo deletada                                         | 
| `form`       | Sim         | A classe do formulário confirmando a remoção do objeto                       | 
| `return_url` | Sim         | A URL que o usuário está sendo redirecionado depois de submeter o formulário | 

### object_list.html

Caminho (path): `generic/object_list.html`

O template sendo utilizado pela visualização genérica `ObjetListView` para ser exibida em uma lista filtrável de objetos múltiplos.

#### Blocos (Blocks)

| Nome              | Obrigatório | Descrição                                                                                          |
|------------------|--------------|----------------------------------------------------------------------------------------------------|
| `extra_controls` | -            | Botões de ações adicionais                                                                         | 
| `bulk_buttons`   | -            | Grupo de ações (bulk actions) de botões adicionais para serem exibidos  abaixo da lista de objetos | 

#### Contexto (Context)

| Nome          | Obrigatório | Descrição                                                                                         |
|---------------|-------------|---------------------------------------------------------------------------------------------------|
| `model`       | Sim         | A classe do objeto                                                                                |
| `table`       | Sim         | A classe da tabela utilizada para renderizar a lista de objetos                                   | 
| `permissions` | Sim         | Um mapeamento para adicionar, alterar e deletar as permissões do uusário corrente                 | 
| `actions`     | Sim         | Uma lista de botões para exibir (`add`, `import`, `export`, `bulk_edit`, e/ou `bulk_delete`)      | 
| `filter_form` | -           | O formulário de filterset atrelado para filtragem da lista de objetos                             | 
| `return_url`  | -           | A URL retornada para ser passada ao submeter um formulário de operações em grupo (bulk operation) | 

### bulk_import.html

Caminho (path): `generic/bulk_import.html`

O template utilizado pela visualização genérica (generic view) `BulkImportView` para importar múltiplos objetos de uma vez dos dados de um CSV.

#### Blocos (Blocks)

None

#### Contexto (Context)

| Nome         | Obrigatório | Descrição                                                                           |
|--------------|-------------|-------------------------------------------------------------------------------------|
| `model`      | Sim         | A classe do objeto                                                                  |
| `form`       | Sim         | A classe do formulário para importar o CSV                                          |
| `return_url` | -           | A URL retornada para ser passada ao submeter uma operação em grupo (bulk operation) |
| `fields`     | -           | Um diretório de campos de formulário, para opções de importação de exibição         |

### bulk_edit.html

Caminho (path): `generic/bulk_edit.html`

O template utiliado pela visualização genérica (generic view) `BulkEditView` para modificar múltiplos objetos simultaneamente.

#### Blocos (Blocks)

None

#### Contexto (Context)

| Nome         | Obrigatório | Descrição                                                         |
|--------------|-------------|-------------------------------------------------------------------| 
| `model`      | Sim         | A classe do objeto                                                |
| `form`       | Sim         | Classe de formulário para edição em grupo                         | 
| `table`      | Sim         | Uma classe de tabela utilizada para renderizar a lista de objetos | 
| `return_url` | Sim         | A URL que o usuário é redirecionado ao submeter um formulário     | 

### bulk_delete.html

Caminho (path): `generic/bulk_delete.html`

O template utilizado pela visualização genérica (generic view) `BulkDeleteView` para deletar múltiplos objetos simultaneamente.

#### Blocos (Blocks)

| Nome            | Obrigatório | Desrição                                     |
|-----------------|-------------|----------------------------------------------|
| `message_extra` | -           | Um conteúdo de mensagem de aviso suplementar | 

#### Contexto (Context)

| Nome         | Obrigatório | Descrição                                                      |
|--------------|-------------|-------------------------------------------------------------------|
| `model`      | Yes         | A classe do objeto                                                |
| `form`       | Yes         | A classe de formulário para remoção em grupo (bulk delte)         |
| `table`      | Yes         | A classe da tabela utilizada para renderizar uma lista de objetos |
| `return_url` | Yes         | A URL que o usuário é redireiconado após submeter o formulário    | 

## Tags

O template customizado de tags abaixo estão disponíveis no NetBox.

!!! info Informação

    Esses tamplates de backend são automaticamente carregados: Você _não_ precisa incluir uma tag `{% load %}` no seu template para ativá-los.

::: utilities.templatetags.builtins.tags.badge

::: utilities.templatetags.builtins.tags.checkmark

::: utilities.templatetags.builtins.tags.customfield_value

::: utilities.templatetags.builtins.tags.tag

## Filtros (Filters)

Os filtros de template customizado estão disponíveis no NetBox.

!!! info Informação

    Esses templates de backend são automaticamente carregados: Você _não_ precisa incluir uma tag `{% load %}` no seu template para ativá-los.

::: utilities.templatetags.builtins.filters.bettertitle

::: utilities.templatetags.builtins.filters.content_type

::: utilities.templatetags.builtins.filters.content_type_id

::: utilities.templatetags.builtins.filters.linkify

::: utilities.templatetags.builtins.filters.meta

::: utilities.templatetags.builtins.filters.placeholder

::: utilities.templatetags.builtins.filters.render_json

::: utilities.templatetags.builtins.filters.render_markdown

::: utilities.templatetags.builtins.filters.render_yaml

::: utilities.templatetags.builtins.filters.split

::: utilities.templatetags.builtins.filters.tzoffset
