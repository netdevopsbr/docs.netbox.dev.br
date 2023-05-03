# Planejamento seus Passos

Esse guia resume os passos necessários para fazer o planejamento de migração para o NetBox. Embora é feito sobre o contexto de uma instalação nova, a abordagem geral esboçada aqui funciona normalmente para a adição de novos dados ao deploy (instalação) existente do NetBox.


## Identifique as Atuais Fontes de Verdade (Current Source of Truth)

Antes de começar o uso do NetBox para os seus próprios dados, é crucial primeiro entender onde estão as fontes de verdade (dados) existentes. Uma "fonte de verdade" (source of truth) é justamente qualquer repositório de dados que é autoridade para aquele domínio específico. Por exemplo, você pode ter uma planilha que mapeia todos os endereços IP em uso na sua rede. Enquanto todo mundo concordar que essa planilha é _autoritativa_ para a rede inteira, isso se torna a fonte de verdade (source of truth) para os prefixos de IP de sua rede.

Qualquer coisa pode ser a fonte de verdade (source of truth), se estas duas condições existirem:

1. Existe um acordo entre todos os participantes relevantes que essa fonte de verdade está correta.
2. O domínio que ela está aplica é definido.

<!-- TODO: Example SoT -->

Dedique algum tempo para mapear todas as suas fontes de verdades atuais de sua infraestrutura. Na entativa de catalogar e categorizar essas fontes de verdade, você provavelmente terá alguns desafios, como:

* **Múltiplas fontes conflitantes** para um domínio específico. Por exemplo, devem existir múltiplas versões de sua planilha circulando entre as pessoas, odne cada uma tem um grupo de dados conflitantes, provavelmente.
* **Fontes com domínios específicos.** Você pode encontrar um time diferente dentro de sua organização que utiliza ferramentas para o mesmo propósito, com uma definição de quando deve ser utilizada.
* **Formatos de dados inacessíveis.** Algumas ferramentas servem melhor para um uso programático que outras. Por exemplo, planilhas são normalmente fácies de fazer o "parse" e exportar, no enquanto páginas em wikis ou aplicações similares são normalmente difíceis de "consumir".
* **Não há nenhuma fonte de verdade.** Às vezes você irá encontrar uam fonte de verdade que simplesmente não existe para um domínio específico. Por exemplo, ao configurar endereços IP, operadores podem apenas presumir que existe um IP disponível de certa subrede sem nunca registrar o uso deste mesmo endereço IP.

Veja se você consegue identificar cada domínio de dados da infraestrutura da sua organização, e a fonte de verdade de cada. Uma vez que você tenha juntado isso, você precisará detarminar quais dados pertencem ao NetBox.

## Determine os Dados para Migrar

Como regra geral, para determinar os dados que serão movidos para o NetBox baseiam-se em: se existe um modelo (model) para isso, esse dado pertence ao NetBox. Por exemplo, o NetBox tem modelos relacionados a racks, dispositivos (devices), cabos, prefixos IP, VLANs e por aí vai. Eles têm o uso bem "direto". No entanto, você inveitavelmente irá chegar nos limites do modelo de dados do NetBox e questionar quais dados adicionais podem fazer sentido registrar no NetBox. Por exemplo, você pode querer que o NetBox sirva como fonte de verdade para registros DNS e servidores DHCP, ainda que não necessariamente esteja no escopo nativo do projeto.

O NetBox fornece dois mecanismos para extender seu modelo de dados (data model). O primeiro é os campos customizados (custom fields): A maioria dos modelos suportam campos de dados adicionais para armazenar informações adicionais aos campos nativos. Por exemplo, você pode querer adicionar um campo de "inventory ID" (ID do inventári) para o modelo do dispositivo (device model).

Dito isso, não faz sentido migrar todos os domínios de dados par ao NetBox. Por exemplo, muitas organizações optam por usar somente os componentes IPAM ou somente os componentes DCIM do NetBox, e integrar as outras fontes de verdade de diferentes domínios. Isso é uma abordagem muito válida (desde que todos os envolvidos concordem quais ferramentas são autoritativas (possuem autoridade) para cada domínio de dados.). Por fim, você irá precisar pesar o valor de ter modelos de dados não nativos no NetBox contra o esforço necessário para definir e manter esses modelos.

Considere que o NetBox está sob constante desenvolvimento. Embora a versão atual possa não suportar um tipo particular de objeto, existem planos para adicionar suporte a isso em versões futuras. (E mesmo que não haja, considere criar uma requisição de função/característica (feature request) citando o seu caso de uso, em particular).

## Validando Dados Externos

O último passo antes de migrar os dados para o NetBox é a **validação** mais crucial. O princípio GIGO (garbage in, garbage out) está no seu efeito máximo: A sua foonte de verdade é tão boa quanto os dados que ela armazena. Enquanto que o NetBox é uma ferramenta de validação de dados poderosa (incluindo o suporte de regras de validação customizada), oque irá decidir seu poder é o operador humano adicionando e dando a manutenção correta aos dados. Por exemplo, o NetBox pode validar a conexão de cabo entre duas interfaces, mas não pode dizer se o cabo _deveria ou não existir_.

Aqui está algumas dicas para ajudar na garantia que somente dados válidos serão importados ao NetBox:

* Garanta que você está começando com um dado completo e bem formatado. JSON ou CSV são altamente recomendados para uma melhor portabilidade.
* Considere definir regras de validação customizados dentro do NetBox antes de realizara importação. (Por exemplo, formar o esquema de nomes dos dispositivos.)
* Utilize scripts customizados para auomaticamente popular dados padronizados. (Por exemplo, para automaticamente criar um grupo de VLANs padrões pada cada site.)

Há vários métodos disponíveis para a importação de dados no NetBox, os quais nós iremos cobrir na próxima seção.
There are several methods available to import data into NetBox, which we'll cover in the next section.

## Ordem das Operações

Ao começar com um banco de dados limpo, pode não ser claro logo de início por onde começar. Muitos modelos dentro do NetBox dependem da criação de outros tipos. Por exemplo, você não pode criar um tipo de dispositivo (device type) até que tenha criado seu fabricante (manufacturer).

Abaixo está a ordem recomendada pela qual os objetos do NetBox devem ser criados ou importados. Enquanto que não é necessário seguir essa lista na ordem exata, fazer desta maneira irá ajudar a ter um trabalho fluído. 

1. Grupos de Locação (Tenant Groups) e Locatários (Tenants)
2. Regiões, Grupos de Locais (Site Groups), Locais (Sites) e Localizações (Locations)
3. Funções de cada Rack (Rack Roles) e Racks em si
4. Fabricantes (Manufacturers), Tipos de Dispositivos (Device Types), Tipos de Módulos (Module Types)
5. Plataformas (Sistemas), Funções do Dispositivo (Device Roles)
6. Dispositivos e Módulos
7. Fornecedores (Provider) e Redes de Fornecedor 
8. Tipos de Circuitos e Circuitos
9. Grupos de Wireless LAN (WiFi) e Wireless
10. Route targets & VRFs
11. RIRs e Agregados (aggregates)
12. Funções de cada IP/VLAN
13. Prefixos, Ranges de IP e endereços IP
14. Grupos de VLAN & VLANs
15. Tipos de Clusters, Grupos de Cluster e Clusters
16. Máquinas Virtuais (Virtual Machines) e interfaces de VMs

Isso não é uma lista que inclui tudo, mas deve ser suficiente para iniciar a importação de dados. Além disso, a ordem pela qual os objetos são adicionados não tem qualquer impacto.

Os gráficos abaixo ilustram algumas das dependências principais entre os diferentes modelos do NetBox, para referência.

!!! note Modelos Auto-Aninhados (Self-Nesting)

    Cada modelo no gráfico abaixo que mostra uma flecha em looping apontando para si mesmo pode ser aninhado em uma hierarquia recursiva. Por exemplo, você pode ter regiões que representam tanto países, quanto cidades, que posteriormente pode ser uma aninhada (atrelada) à outra.

### Tenancy (Locação)

```mermaid
flowchart TD
    TenantGroup --> TenantGroup & Tenant
    Tenant --> Site & Device & Prefix & VLAN & ...

click Device "../../models/dcim/device/"
click Prefix "../../models/ipam/prefix/"
click Site "../../models/dcim/site/"
click Tenant "../../models/tenancy/tenant/"
click TenantGroup "../../models/tenancy/tenantgroup/"
click VLAN "../../models/ipam/vlan/"
```

### Locais (Sites) & Racks & Dispositivos (Racks)

```mermaid
flowchart TD
    Region --> Region
    SiteGroup --> SiteGroup
    DeviceRole & Platform --> Device
    Region & SiteGroup --> Site
    Site --> Location & Device
    Location --> Location
    Location --> Rack & Device
    Rack --> Device
    Manufacturer --> DeviceType & ModuleType
    DeviceType  --> Device
    Device & ModuleType ---> Module
    Device & Module --> Interface

click Device "../../models/dcim/device/"
click DeviceRole "../../models/dcim/devicerole/"
click DeviceType "../../models/dcim/devicetype/"
click Interface "../../models/dcim/interface/"
click Location "../../models/dcim/location/"
click Manufacturer "../../models/dcim/manufacturer/"
click Module "../../models/dcim/module/"
click ModuleType "../../models/dcim/moduletype/"
click Platform "../../models/dcim/platform/"
click Rack "../../models/dcim/rack/"
click RackRole "../../models/dcim/rackrole/"
click Region "../../models/dcim/region/"
click Site "../../models/dcim/site/"
click SiteGroup "../../models/dcim/sitegroup/"
```

### VRFs, Prefixos, IP Addresses, and VLANs

```mermaid
flowchart TD
    VLANGroup --> VLAN
    Role --> VLAN & IPRange & Prefix
    RIR --> Aggregate
    RouteTarget --> VRF
    Aggregate & VRF --> Prefix
    VRF --> IPRange & IPAddress
    Prefix --> VLAN & IPRange & IPAddress

click Aggregate "../../models/ipam/aggregate/"
click IPAddress "../../models/ipam/ipaddress/"
click IPRange "../../models/ipam/iprange/"
click Prefix "../../models/ipam/prefix/"
click RIR "../../models/ipam/rir/"
click Role "../../models/ipam/role/"
click VLAN "../../models/ipam/vlan/"
click VLANGroup "../../models/ipam/vlangroup/"
click VRF "../../models/ipam/vrf/"
```

### Circuitos

```mermaid
flowchart TD
    Provider & CircuitType --> Circuit
    Provider --> ProviderNetwork
    Circuit --> CircuitTermination

click Circuit "../../models/circuits/circuit/"
click CircuitTermination "../../models/circuits/circuittermination/"
click CircuitType "../../models/circuits/circuittype/"
click Provider "../../models/circuits/provider/"
click ProviderNetwork "../../models/circuits/providernetwork/"
```

### Clusters & Máquinas Virtuais

```mermaid
flowchart TD
    ClusterGroup & ClusterType --> Cluster
    Cluster --> VirtualMachine
    Site --> Cluster & VirtualMachine
    Device & Platform --> VirtualMachine
    VirtualMachine --> VMInterface

click Cluster "../../models/virtualization/cluster/"
click ClusterGroup "../../models/virtualization/clustergroup/"
click ClusterType "../../models/virtualization/clustertype/"
click Device "../../models/dcim/device/"
click Platform "../../models/dcim/platform/"
click VirtualMachine "../../models/virtualization/virtualmachine/"
click VMInterface "../../models/virtualization/vminterface/"
```