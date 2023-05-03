# Visão Geral sobre a API GraphQL

O NetBox fornece uma API [GraphQL](https://graphql.org/) de leitura (read-only) para complementar a API REST. A API é fornecida pela biblioteca [Graphene](https://graphene-python.org/) e [Graphene-Django](https://docs.graphene-python.org/projects/django/en/latest/).

## Buscas (Queries)

O GraphQL permite que o cliente especifique uma lista aninhada arbitrária de campos para serem inclusos na resposta. Todas as queries são feitas para o endpoint root da API `/graphql`. Por exemplo, para retornar o ID do circuito e o nome do fornecedor (provider) de cada circuito com um status active (ativo), você pode utilizar uma requisição como a abaixo:

```
curl -H "Authorization: Token $TOKEN" \
-H "Content-Type: application/json" \
-H "Accept: application/json" \
http://netbox/graphql/ \
--data '{"query": "query {circuit_list(status:\"active\") {cid provider {name}}}"}'
```

A resposta incluirá os dados requisitados formatados em JSON:

```json
{
  "data": {
    "circuits": [
      {
        "cid": "1002840283",
        "provider": {
          "name": "CenturyLink"
        }
      },
      {
        "cid": "1002840457",
        "provider": {
          "name": "CenturyLink"
        }
      }
    ]
  }
}
```

!!! note Observação

    É recomendado passar os dados retornados através de um parser JSON como o `jq` para uma melhor leitura.

O NetBox fornece tanto uma query singular ou plural de campos para cada tipo de objeto:

* `$OBJECT`: Retorna um objeto único. Deve especificar o ID único do objeto como `(id: 123)`.
* `$OBJECT_list`: Retorna uma lista de objetos, opcionalmente filtrados pelos parâmetros fornecidos.

Por exemplo, utilize `device(id:123)` para obter um dispositivo específico (identificado pelo seu ID único, e busque por `device_list` (com um grupo de filtros opcionais) para obter todos os dispositivos.

Para mais detalhes sobre a construção de queries GraphQL, veja a [documentação do Graphene](https://docs.graphene-python.org/en/latest/), assim como a [documentação de queries do GraphQL](https://graphql.org/learn/queries/).

## Filtrando

A API do GraphQL utiliza a mesma lógica de filtros como a interface web (UI) e a API REST. Filtros podem especificados por pares de chave-valor (key-value) dentro de parênteses imediatamente seguido do nome da busca (query name). Por exemplo, o exemplo abaixo irá retornar somente os sites dentro da região North Carolina com o status de active:

```
{"query": "query {site_list(region:\"north-carolina\", status:\"active\") {name}}"}
```

Além disso, filtros podem ser feitos em uma lista de objetos relacionados como mostrado na query abaixo:

```
{
  device_list {
    id
    name
    interfaces(enabled: true) {
      name
    }
  }
}
```

## Múltiplos Tipos de Retorno (Return Types)

Certas queries podem retornar múltiplos tipos de objetos, por exemplo, a terminação de cabos podem retornar a terminação de circuitos, portas console e muitas outras. Elas podem ser buscadas utilizando [inline fragments](https://graphql.org/learn/schema/#union-types) como mostrado abaixo:

```
{
    cable_list {
      id
      a_terminations {
        ... on CircuitTerminationType {
          id
          class_type
        }
        ... on ConsolePortType {
          id
          class_type
        }
        ... on ConsoleServerPortType {
          id
          class_type
        }
      }
    }
}

```

O campo "class_type" é uma maneira fácil de distinguir qual tipo de objeto está sendo utilizado ao visualizar os dados retornados, ou quando estiver filtrando. Contém o nome da classe, por exemplo "CircuitTermination" ou "ConsoleServerPort".

## Autenticação (Authentication)

A API do GraphQL utiliza os mesmos tokens de autenticação que a API REST. Tokens de autenticação são incluidos dentro da requisição ao colocar o cabeçalho HTTP `Authorization` dentro do formulário a seguir:

```
Authorization: Token $TOKEN
```

## Desabilitando a API GraphQL

Se não necessária, a API do GraphQL pode ser desabilitada ao configurar o parâmetro de configuração [`GRAPHQL_ENABLED`](../configuration/miscellaneous.md#graphql_enabled) para `False` e reiniciar o NetBox.