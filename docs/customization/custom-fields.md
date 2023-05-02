# Campos Customizados (Custom Fields)

Cada modelo do NetBox é representado no banco de dados como uma tabela distinta e cada atributo do modelo existe como uma coluna dentro desta tabela. Por exemplo, sites (locais) são armazenados na tabela `dcim_site`, na qual tem colunas seguintes colunas `name`, `facility`, `physical_address` e por aí vai. Como novos atributos são adicionados aos objetos através do desenvolvimento do NetBox, tabelas são expandidas para incluir novas linhas.

No entanto, alguns usuários podem querer armazenar atributos de objetos adicionais que são de alguma forma incomum em sua natureza, e que não faria sentido incluir no código principal do NetBox de schema do banco de dados. Por exemplo, suponha que sua organização precise associar cada dispositivo com um número de ticket correlacionando-o com seu sistema de suporte. Isso é certamente um uso legítimo dentro do NetBox, mas não é uso suficientemente comum para ser adicionado como um campo em cada instalação do NetBox. Porém, você pode criar um campo customizado para armazenar essa informação.

Dentro do banco de dados, campos customizados são armazenados como um JSON junto com cada objeto. Isso alivia a necessidade de queries (pesquisas) complexas na hora de pesquisar e obter os dados dos objetos no banco de dados.

## Criando Campos Customizados

Campos customizados podem ser criados navegando em Customization > Custom FIelds. NetBOx suporta diversos tipos de campos customizados:

- Text: Texto free-form (texto usado para somente uma linha)
- Long text: Suporta de qualquer tamanho; suporta renderização de Markdown
- Integer: Um número inteiro (positivo ou negativo)
- Decimal: Um número decimal de precisão fixa (04 casas decimais possíveis)
- Boolean: Verdadeiro ou falso
- Date: Uma data no formato ISO 8601 (YYYY-MM-DD)
- URL: Será representado como um link na web UI (interface web)
- JSON: Dados arbitrários armazenados no formato JSON
- Selection: Uma seleção de uma ou várias escolhas customizadas pré-definidas
- Multiple selection:Uma seleção de campos que suportam a atreção de diversos valores
- Object: Um tipo de objeto único definido por `object_type`
- Multiple object: Um ou mais tipos de objetos definidos em `object_type`

Cada campo customizado deve ter um nome. O nome pode ser um simples nome (como `tps_report`) ou um ainda que contenha caracteres alfa-númericos e underscores. Você pode também atribuir uma tag (label) facilmente lida por humanos como "TPS report"; essa label aparecerá nos formúlarios web. Um peso também é obrigatório: Pesos (weight) maiores serão orderados embaixo dentro de um formulário (O peso padrão é 100). Se uma descrição é fornecida, ela aparecerá abaixo do campo no formulário.

Marcar um campo como obrigatório irá forçar o usuário a fornecer o valor para aquele campo quando criar um novo objeto ou quando estiver salvando um objeto existente. Um valor padrão (default value) para o campo pode também ser fornecido. Use "true" (verdadeiro) ou "false" (falso) para campos booleanos (boolean).

Um campo customizado deve ser atrelado a um ou mais tipos de objetos, ou modelos (models) dentro do NetBox. Uma vez criado, campos customizados aparecerão automaticamente como parte desses modelos dentro da interface web e consultas REST API. Note que nem todos os modelos do NetBox suportam campos customizados.

## Filtros (Filtering)

A lógico de filtros controla como os valores são dão match (correspondem) na hora de filtrar objetos utilizando os campos customizados. Loose filtering (filtro padrão) dá match em um valor parcial, enquanto que o matching exato requer um match completo do valor de um campo em string. Por exemplo, filtragem exata dará match em "vermelho" somente se o valor fornecido na pesquisa for exatamente "vermelho", enquanto que a filtragem "folgada" (loose) dará match em "vermelho", "vermelho escuro", "vermelho claro" se o valor "vermelho" for fornecido. Mudando a configuração da lógica do filtro (filter logic) para "disabled" (desabilitado) desativa a filtragem por campo inteiramente.

## Agrupamento (Grouping)

!!! note

    Essa feature (função) foi introduzida na versão v3.3 do NetBox.

Campos customizados relacionados podem ser agrupados juntos dentro da interface do usuário (UI) ao atrelar cada objeto ao mesmo nome de grupo. Quando ao menos um campo customizado de um tipo de objeto tem seu grupo definido, ele aparecerá abaixo do grupo dentro do painel de cumpos customizados na visualização do objeto. Todos os campos cutomizados dentro do mesmo grupo aparecerá abaixo do mesmo grupo nomeado. (Note que o nome do grupo deve ser exato para que apareçam juntos.)

Esse parâmetro não tem efeito na representação dos dados de campos customizados na API.

## Visibilidade

!!! note

    Essa feature (função) foi introduzida na versão v3.3 do NetBox.

Ao criar um campo customizado, há três opções de visibilidade na interface do usuário (UI). Essas opções controlam como ou quando um campo customizado deve ser exibilido dentro do NetBox.
- **Read/write:** (padrão) O campo customizado é incluído ao ver e editar objetos (leitura e escrita).
- **Read-only:** O campo customizado é exibido ao ver um objeto, mas não pode ser editado pela interface web (UI). O campo aparecerá no formulário como um campo de leitura somente.
- **Hidden:** O campo customizado nunca será exibido dentro da interface web (UI). Essa opção é recomendada para campos que não têm a finalidade de serem utilizadas por usuários humanos (apenas para automação via API).

## Validação

NetBox suporta validação customizada e limitada para os valores dos campos customizados. Abaixo estão os tipos de validação disponíveis para cada tipo de campo:
- Text: Expressão regular de texto (opcional)
- Integer: Valor mínimo e máximo (opcional)
- Selection: Deve dar match exato um dos valores pré-definidos (lista de valores pré-definidos)

## Seleção customizada de Campos

Cada seleção customizada de campos deve ter ao menos duas escolhas. Essa lista é definida separando os valores por vírgula. Escolhas (choices) aparecerem no formulário na ordem em que são listados. Note que os valores de escolha são salvados exatamente como aparecem, então é melhor evitar pontuações e símbolos aonde for possível.

Se um valor padrão (default value) for especificado em um campo de seleção (selection field), este valor deve dar match exato em um ou mais das escolhas fornecidas. O valor de um campo de seleção múltipla sempre retornará como uma lista, mesmo que somente um valor seja escolhido.

## Campos de Objeto customizados

Um campo de objeto ou multi-objetos pode ser usado para referenciar um objeto em particular do NetBox como o valor de um campo customizado (custom fields). Esse campo customizado deve definir um `object_type`, que determina o tipo de objeto que o campo customizado pode apontar.

## Campos Customizados em Templates

Varias features (funções ou características) do NetBox, como a exportação de templates e webhooks, utiliza o Jinja2. Por conveniência, objetos que suportam a atrelação de campos customizados exbie os dados do campo customizado pela propriedade `cf`. Isso é fica um pouco mais limpo do que acessar o campo customizado pelo seu nome real (`custom_field_data`).

Por exemplo, um camo customizado nomeado de `foo123` no modelo (model) de Site (local) é acessível usando `{{ site.cf.foo123}}`.

## Campos Customizados e a API REST

Ao obter os dados de um objeto pela API REST, todos os dados do campo customizados serão incluídos dentro do atributo `custom_fields`. Por exemplo, abaixo está a exibido o resultado parcial de um site (local) com dois campos customizados definidos:

```json
{
    "id": 123,
    "url": "http://localhost:8000/api/dcim/sites/123/",
    "name": "Raleigh 42",
    "custom_fields": {
        "deployed": "2018-06-19",
        "site_code": "US-NC-RAL42"
    },
}
```

Para definir ou mudar esses valores, simplesmente inclua os dados do JSON de forma aninhada, por exemplo:

```json
{
    "name": "New Site",
    "slug": "new-site",
    "custom_fields": {
        "deployed": "2019-03-24"
    }
}
```
