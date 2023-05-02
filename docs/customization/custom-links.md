# Links Customizados (Custom Links)

Links customizados permitem que os usuários mostrem links arbitrários com conteúdo externo dentro da visualizaçã ode objetos no NetBox. Eles são úteis para croos-referencing (referência cruzada) relacionadas a registros em sistemas fora do NetBox. Por exemplo, você pode querer criar um link customizado dentro da visualização do dispositivo que mande o usuário para o mesmo dispositivo no Network Monitoring System (ou sistema de monitoramento). 

Esses links customizados são criados navegando por **Customization > Custom Links.** Cada link é asssociado com um tipo de objeto em particular no NetBox (site, device, prefix, etc.) e será mostrado nessas views (páginas web). Cada link tem que mostrar um texto que contenha uma URL, e os dados do item do NetBox sendo visualizado pode ser incluido no link utilizando código de [template do Jinja2](https://jinja2docs.readthedocs.io/en/stable/) através da variável `obj` e campos customizados através de `obj.cf`.

Por exemplo, você talvez queira definir um link como o abaixo:
- Texto: `Visualizar no NMS`
- URO: `https://nms.example.com/nodes?name={{ obj.name }}`

Ao visualizar um dispositivo nomeado de `Router4`, esse link seria renderizado como:

```
<a href="https://nms.example.com/nodes/?name=Router4>Visualizar no NMS</a>
```

Links customizados aparecem como botões na parte superior direita da página. Peso número pode ser utilizado para influenciar como esses botões de link serão ordenados, e cada link pode ser habilitado ou desabilitado manualmente.

!!! warning

    Links customizados dependem de código criado pelo usuário para gerar a saída HTML, que pode ser perigoso. Apenas garanta permissões para criar ou modificar links customizados para usuários confiáveis.

## Dados de Contexto (Context Data)

Os seguintes dados de contexto estão disponívels dentro do template na hora de renderizar um o texto ou URL de um link customizado.

| Variável | Descrição |
|----------|-----------|
| `object` | O objeto do Netbox sendo exibido |
| `obj` | Mesma coisa que `object`; é mantido para compatibilidade com versões anteriores até a versão v3.5 do Netbox | 
| `debug` | Um booleano (boolean) indicando se debugging está habilitado. |
| `request` | Representa a requisição WSGI corrente |
| `user` | O usuário atual (se estiver autenticado) |
| `perms` | As [permissões](https://docs.djangoproject.com/en/stable/topics/auth/default/#permissions) atribuídas ao usuário |

Enquanto que a maioria das variáveis de contexto listadas acima possuem atributos consistentes, o objeto será uma instância específica do objeto sendo visualizado quando o link estiver sendo renderizado. Diferentes models (modelos) têm direntes campos e propriedades, então talvez você queira pesquisar para conseguir determinar se os atributos disponveís para uso no template estão disponíveis para o tipo de objeto em questão.

Verificar a representação REST API de um objeto é geralmente o jeito mais conveniente de verificar quais atributos estão disponíveis. Você também pesquisar pelo código fonte do NetBox diretamente para obter informações mais completas.

## Renderização condicional (Conditional Rendering)

Apenas links que renderizam textos não vários (non-empty) são incluídos nesta página. Como pode utilizar a lógica condicional do Jinja2 para controlar as condições pela qual um link é renderizado.

Por exemplo, se você quer mostrar links customizados somente para dispositivos que estejam ativos, você pode configurar o texto do link para:

```
{% if obj.status == 'active" %}Visualizar no NMS{% endif %}
```

O link acima não irá aparecer ao visualizar dispositivos que não tenham o status como "active" (ativo).

Outro exemplo é se você quiser mostrar somente os dispositivos que pertençam a um fabricante (manufacturer) específico, você poderia fazer algo como:

```
{% if obj.device_type.manufacturer.name == 'Cisco' %}Visualizar no NMS{% endif %}
```

O link apenas aparecerá ao visualizar dispositivos que a fabricante seja a "Cisco".

## Grupos de Link (Link Groups)

Nomes de grupo podem ser definidos para organizar links em grupos. Links com o mesmo nome de grupo serão renderizados como um menu de lista dropdown abaixo de um botão único com o nome do grupo.

## Tabela de Colunas (Table Columns)

Links customizados podem também ser incluídos na tabela de um objeto ao selecionar os links desejáveis na configuração do formulário da tabela. Quando exibidos, cada link irá renderizar um hyperlink para o objeto correspondente. Quando exportado (via CSV, por exemplo), cada link renderizará somente sua URL.