# Exportar Templates 

NetBox permite que os usuário definam templates customizados que podem ser utilizados na hora de exportar os objetos. Para configurar um template de exportação, navegue até **Customization > Export Templates**.

Cada template de exportação é associado com um tipo de objeto. Por exemplo, você pode criar um template para as VLANs, seu template customizado irá aparecer abaixo do botão "Export" na lista de VLANs. Cada template de exportação deve ter um nome e pode opcionalmente designar um tipo de específico de [MIME type](https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/MIME_types) ou extensão do arquivo na hora de exportar.

Templates de exportação devem ser escritos em [Jinja2](https://jinja.palletsprojects.com/).

!!! note

    O nome `table` é reservado para uso interno.


!!! warning

    Templates de exportação são renderizados usando um code criado pelo usuário, que pode consequentemente causar riscos em certas situações. Apenas conceda permissões para criar ou modificar templates de exportação para usuários confiáveis.

A lista de objetos retornados do banco de dados ao renderizar uma exportação de template é armazenada na variável `queryset`, a qual você pode querer iterar (iterate) usando um `for` loop. Propriedades do objeto podem ser acessadas pelo nome. Por exemplo:

```
{% for rack in queryset %}
Rack: {{ rack.name }}
Site: {{ rack.site.name }}
Height: {{ rack.u_height }}U
{% endfor %}
```

Para acessar um campo customizado de um objeto dentro de um template, utilize o atributo `cf`. Por exemplo, {{ obj.cf.color }} irá retornar o valor (se houver) de um campo customizado nomeado de `color` dentro de `obj`. 

Se você precisa utilizar um dado de config context (configuração de contexto) em um template de exportação, você deve usar a função `get_config_context` para obter todas as configurações de contexto. Por exemplo:

```
{% for server in queryset %}
{% set data = server.get_config_context() %}
{{ data.syslog }}
{% endfor %}
```

O atributo `as_attachment` de um template de exportação controla o comportamento, quando renderizado. Se verdadeiro (true), o conteúdo renderizado será retornado ao usuário como um arquivo possível de realizar download. Se falso (false), será exibido no browser. (Isso pode ser útil na hora de gerar conteúdo HTML, por exemplo)

Um MIME type e extensão do arquivo pode opcionalmente ser definida para cada template de exportação. O MIME type default é `text/plain`.

## Integração REST API

Quando for necessário prover credenciais de autenticação (quando o `LOGIN_REQUIRED` estiver habilitado, por exemplo), é recomendado renderizar os templates de exportação pela API REST. Isso permite que o cliente especifique um token de autenticação. Para renderizar um template de exportação pela API REST, faça uma requisição `GET` para requisitar a lista de endpoints de modelo e coloque o parâmetro `export` ao final para exportar o nome do template. Por exemplo:

```
GET /api/dcim/sites/?export=MyTemplateName
```

Note que o corpo da resposta irá conter apenas o conteúdo do template de exportação renderizado, em oposição ao objeto ou lista do tipo JSON.

## Exemplo

Abaixo está um exemplo de exportação do template de um dispositivo que irá gerar uma simples configuração Nagios da lista de dispositivos.

```
{% for device in queryset %}{% if device.status and device.primary_ip %}define host{
        use                     generic-switch
        host_name               {{ device.name }}
        address                 {{ device.primary_ip.address.ip }}
}
{% endif %}{% endfor %}
```

A saída (output) gerada irá ser algo como:

```
define host{
        use                     generic-switch
        host_name               switch1
        address                 192.0.2.1
}
define host{
        use                     generic-switch
        host_name               switch2
        address                 192.0.2.2
}
define host{
        use                     generic-switch
        host_name               switch3
        address                 192.0.2.3
}
```