# Populando NetBox com Dados

Essa página cobre os mecanismos que estão disponíveis para popular os dados dentro do NetBox.

## Criação de Objeto Manual

A forma mais simples e direta de popular dados dentro do NetBox é utilizar a criação de objetos via formulário dentro da interface do usuário.

!!! warning Não idela para Importações Grandes

    Enquanto é conveniente e acessível para até mesmo os mais novos usuários, criar objetos um por um manualmente completando esses formulários obviamente não escalam bem. Para importações grandes, você provavelmente estará bem servido ao usar um dos outros métodos discutidos nessa seção.

Para criar um novo objeto dentro do NetBox, encontre o tipo de objeto dentro do menu de navegação e clique no botão verde de "Add" (Adicionar)

!!! info Não está achando o Botão?

    Se você não encontrar o botão de "Add" em alguns tipos de objeto, é porque sua conta não tem permissões suficientes para criar esses tipos. Peça ao administrador do seu NetBox para que lhe atribua as permissões necessárias.

    Note também que para alguns tipos de objetos, como os componentes de dispositivos, você não pode criá-los diretamente do menu de navegação. Eles devem ser criados dentro do contexto de um objeto pai (como no dispositivo pai).

<!-- TODO: Screenshot -->

## Importação em Grupo (CSV/YAML)

O NetBox suporta uma importação em grupo de novos objetos e a atualização existente dos objetos utilizando dados formatados em CSV. Esse método pode ser o ideal para a importação de planilhas, que são facilmente convertidas para CSV. Dados em CSV podem ser importados, seja como um campo de formulário, ou fazendo o upload próprio do arquivo em CSV já formatado.

Ao visualizar o formulátio de importação do CSV de um tipo de objeto, você irá notar cabeçalhos (headers) para as colunas obrigatórios foram pré-populadas. Cada formulário tem uma tabela abaixo com o título de "CSV Field Options", que lista _todas_ as colunas suportadas para ser utilizada de referência. (Geralmente, isso mapeia os campos que você vê no formulário de criação correspondente para cada objeto individual.)

<!-- TODO: Screenshot -->

Se o campo "id" é adicionado aos dados da planilha CSV, esses dados serão utilizados para atualizar os registros existentes e não importar novos objetos.

Note que alguns modelos (como os device types & module types) não suportam a importação do CSV. No lugar, eles aceitam dados formatados em YAML para facilitar a importação de ambos objetos pai quando os componentes filho.

## Scripting

Às vezes você irá encontrar dados que precisam ser populados no NetBox de forma facilmente reduzida em um padrão (pattern). Por exemplo, suponha que você tem 100 locais (branch sites) e cada local tem 05 VLANs, numeradas de 101 até 105. Enquanto que é certamente possível de explicitamente definir cada uma dessas 500 VLANs dentro da importação de um arquivo CSV, pode ser rapidamente rascunhado em um simples script para automaticamente criar essas VLANs de acordo com um padrão definido. Isso garante um alto nível de confiança na validação dos dados, já que é impossível para um script "esquecer" uma VLAN ou outra.

!!! tip Reconstruindo os Dados Existentes com Scripts

    Às vezes, você pode talvez possa querer escrever um script para popular os objetos mesmo que você tenha os dados necessários prontos para importação. Isso é porque ao utilizar um script, é eliminada a necessidade de manualmente verificar se os dados existem antes da importação.

## REST API

Você pode usar a API REST para facilitar na população de dados dentro do NetBox. A API REST oferece um controle completo e programático para a criação de objetos, sujeitos as mesmas regras de validação forçadas pelos formulários da interface do usuário (UI). Além disso, a API REST suporta a criação em grupos de múltiplos objetos usando somente uma requisição (single request).

Para mais informações sobre essa opção, veja a [documentação sobre API REST](../integrations/rest-api.md).