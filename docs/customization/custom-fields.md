# Campos Customizados (Custom Fields)

Cada modelo do NetBox é representado no banco de dados como uma tabela distinta e cada atributo do modelo existe como uma coluna dentro desta tabela. Por exemplo, sites (locais) são armazenados na tabela `dcim_site`, na qual tem colunas seguintes colunas `name`, `facility`, `physical_address` e por aí vai. Como novos atributos são adicionados aos objetos através do desenvolvimento do NetBox, tabelas são expandidas para incluir novas linhas.

No entanto, alguns usuários podem querer armazenar atributos de objetos adicionais que são de alguma forma incomum em sua natureza, e que não faria sentido incluir no código principal do NetBox de schema do banco de dados. Por exemplo, suponha que sua organização precise associar cada dispositivo com um número de ticket correlacionando-o com seu sistema de suporte. Isso é certamente um uso legítimo dentro do NetBox, mas não é uso suficientemente comum para ser adicionado como um campo em cada instalação do NetBox. Porém, você pode criar um campo customizado para armazenar essa informação.

Dentro do banco de dados, campos customizados são armazenados como um JSON junto com cada objeto. Isso alivia a necessidade de queries (pesquisas) complexas na hora de pesquisar e obter os dados dos objetos no banco de dados.

