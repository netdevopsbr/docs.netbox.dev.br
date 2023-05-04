# Webhooks

O NetBox pode ser configurado para transmitir webhooks de saída para sistemas remotos em resposta às mudanças do objeto interna. O recebedor pode agir nesses dados sobre essas mensagens de webook para tarefas relacionadas.

Por exemplo, suponha que você quer automaticamente configurar um sistema de monitoramento para começar o monitoramento de um dispositivo quando o status operacional for mudado para ativo (active), e remove do monitoramento para qualquer outro status. Você pode criar um webhook no NetBox para um modelo de dispositivo e construir o conteúdo a URL de destino para afetar a mudança desjada no sistema recebedor. Webhooks serão enviados automaticamente pelo NetBox independente as configurações não forem atendidas.

Cada webook deve ser associado com ao menos um tipo de objeto doNetBox e ao menos um evento (criar, atualizar ou remover). Usuários podem especificar o recebedor da URL, o tipo da requisição HTTP (`GET`, `POST`, etc.), tipo do conteúdo (content type) e cabeçalhos. Se deixado vazio, terá uma representação serializada por padrão para o objeto afetado.

!!! warning Aviso sobre Segurança

    Webhooks suportam a inclusão de código submetido pelo usuário para gerar a URL, cabeçalhos customizados e payloads, que possam ter riscos de segurança sobre certas circunstâncias. Apenas garanta permissão para criar ou  modificar webhooks para usuários confiáveis.

## Suporte ao Template do Jinja2

O [template do Jinja2](https://jinja.palletsprojects.com/) é suportado para a `URL`, `additional_headers` e `body_template` campos. Isso habilita o usuário que transfira os dados do objeto no cabeçalho da requisição, assim como construir um corpo da requisição customizada. O conteúdo da requisição pode ser construída para habilitar a interação direta com um sistema externo ao garantir uma mensagem de saída no formato que o recebedor espera e entende.

Por exemplo, você pode criar um webook do NetBox [para enviar uma mensagem ao Slack](https://api.slack.com/messaging/webhooks) à qualquer momento que um endereço IP seja criado. Você pode realizar isso com a seguinte configuração:

* Tipo de objeto: `IPAM > IP address`
* Método HTTP: `POST`
* URL: URL recebida do Webhook do Slack
* Tipo do Conteúdo HTTP: `application/json`
* Tipo do Corpo: `{"text": "IP address {{ data['address'] }} was created by {{ username }}!"}`

### Contexto Disponível

Os dados à seguir estão disponíveis para o contexto de templates do Jinja2:

* `event` - O tipo de evento que a webhook será ativa: criada, atualizada ou removida.
* `model` - O modelo do NetBox que ativou a mudança.
* `timestamp` - A data que o evento ocorreu (no formato [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601))
* `username` - O nome da conta do usuário associada com a mudança.
* `request_id` - O ID da requisição único. Isso pode ser correlacionado com múltiplas mudanças associadas com uma única requisição.
* `data` - A representação detalhada do objeto e seu estado atual. Isso é tipicamente equivalente à representação do modelo na REST API do NetBox.
* `snapshots` - "Snapshots" mínimas do estado do objeto de antes e depois antes da mudança ter sido feita; fornecido com um dicionário de chaves nomeadas com `prechange` e `postchange`. Isso não é tão extensiva como a representação completamente serializada, mas certas informações são suficientes para comunicar aquilo que foi mudado.

### Corpo Padrão da Requisição (Request)

Se nenhum corpo (body) do template for especificado, o corpo da requisição será populado com um objeto JSON contedo os dados de contexto. Por exemplo, um site (local) recentemente criado pode aparecer, como:

```json
{
    "event": "created",
    "timestamp": "2021-03-09 17:55:33.968016+00:00",
    "model": "site",
    "username": "jstretch",
    "request_id": "fdbca812-3142-4783-b364-2e2bd5c16c6a",
    "data": {
        "id": 19,
        "name": "Site 1",
        "slug": "site-1",
        "status": 
            "value": "active",
            "label": "Active",
            "id": 1
        },
        "region": null,
        ...
    },
    "snapshots": {
        "prechange": null,
        "postchange": {
            "created": "2021-03-09",
            "last_updated": "2021-03-09T17:55:33.851Z",
            "name": "Site 1",
            "slug": "site-1",
            "status": "active",
            ...
        }
    }
}
```

## Webhooks Condicionais

Um webhook pode incluir um grupo de lógica condicional expressada pelo JSON usado para controlar se um webhook ativa um objeto específico. Por exemplo, você pode querer ativar um webhook para dispositivos somente quando o campo de `status` do objeto estiver como "active" (ativo):

```json
{
  "and": [
    {
      "attr": "status.value",
      "value": "active"
    }
  ]
}
```

Para mais detalhes, veja a documentação do NetBox para referência sobre [lógica condicional](../reference/conditions.md).

## Processamento do Webhook

Quando uma mudança for detectada, qualquer resultados de webhooks são postos na fila de Redis para processamento. Isso permite a requisição do usuário para permite que não seja necessário esperar a saída do webhook para ser processada. Os webhooks são então extraídos da fila pelo processo `rqworker` a as requisições HTTP são enviadas para seus destinos respectivos. A fila de webhook atual e qualquer webhooks com falha podem ser inspecionadas na interface web do admin `System > Background Tasks`.

A requisição é considerada que tee sucesso, caso a resposta do status do código seja 2XX; de outra forma, a requisição é marcada como se tivesse falhado. Requisições com falha podem ser manualmente extraídas através da interface admin do NetBox.

## Resolvendo os Problemas (Troubleshooting)

Para auxiliar na verificação se o conteúdo renderizado pela saída do webhook está correta, o NetBox fornece um simples "escutador" (listener) HTTP para rodar localmente, receber e exibir as requisições de webhook. Primeiro, modifique a URL de destino para o webhook desejado para `http://localhost:9000/`. Isso irá instruir o NetBox à enviar as requisições para o servidor local na porta TCP 9000. Então, inicie (start) o serviço recebedor (receiver) do webhook no diretório root do NetBox:

```no-highlight
$ python netbox/manage.py webhook_receiver
Listening on port http://localhost:9000. Stop with CONTROL-C.
```

Você pode testar o recebedor (receiver) em si ao enviar a requisição HTTP. Por exemplo:

```no-highlight
$ curl -X POST http://localhost:9000 --data '{"foo": "bar"}'
```

O servidor irá retornar uma saída similar como a seguinte:

```no-highlight
[1] Tue, 07 Apr 2020 17:44:02 GMT 127.0.0.1 "POST / HTTP/1.1" 200 -
Host: localhost:9000
User-Agent: curl/7.58.0
Accept: */*
Content-Length: 14
Content-Type: application/x-www-form-urlencoded

{"foo": "bar"}
------------
```

Observe que o `webhook_receiver` _não_ faz nada com a informação recebida: Ele meramente retorna os cabeçalhos da rquisição e o corpo (body) para inspeção.

Agora, então o webhook do NetBox é ativado e processado, você deve ver que os cabeçalhos e o conteúdo da página aparecem no terminal onde o recebedor do webhook está escutando. Se não, verifique se o processo `rqworker` está rodando e que os eventos do webhook estão sendo postos na fial (queue). Isto é visível na interface de admin do NetBox.