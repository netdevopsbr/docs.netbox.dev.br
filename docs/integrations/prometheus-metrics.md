# Métricas do Prometheus

O NetBox suporta opcionalmente export as métricas do Prometheus nativas da aplicação. [Prometheus](https://prometheus.io/) é uma plataforma de métrica popular de time series utilizada para monitoramento.

O NetBox expõe métricas no endpoint HTTP `/metrics`, por exemplo: `https://netbox.local/metrics`. A exposição de métricas podem ser habilitadas através da configuração `METRICS_ENABLED`. Métricas não são expostas por padrão.

## Tipos de Métricas

O NetBox utiliza a biblioteca [django-prometheus](https://github.com/korfuri/django-prometheus) para exportar um número de diferentes tipos de métricas, incluindo:

- Contadores de inserção, atualização e remoção por modelo
- Contadores de requisição por view (página)
- Histograma de latência por requisição de view (página)
- Histograma do tamanho do corpo (body) da requisição
- Histograma do tamanho do corpo (body) da resposta
- Contadores de código de resposta
- Conexão com o banco de dados, execução e contadores de erros
- Cache hit (uso de cache), miss (perda) e contadores de invalidação (invalidation)
- Histogramas da latência do middleware Django
- Outras métricas de metadados relacionadas com o Django

Para uma lista completa das métricas expostas, visite o endpoint `/metrics` na sua instância do NetBox.

## Observações Sobre Multi Processamento

Ao subir o NetBox de uma maneira multi-processo (como ao rodar múltiplos workers do Gunicorn), a biblioteca cliente do Prometheus exige o uso de um diretório compartilhado para a coleta das métricas de todos os processos de workers. Para configurar isso, primeiro crie ou designe um diretório local no qual os processos de worker tem permissão de leitura, então configure seu serviço WSGI (como o Gunicorn) para definir o caminho (path) do diretório na variável de ambiente `prometheus_multiproc_dir`.

!!! warning Aviso
    
    Se não ter métricas de longo prazo com precisão for crucial para o seu ambiente, é recomendando utilizar a biblioteca `uwsgi` no lugar de `gunicorn`. O problema ocorre na forma como o `gunicorn` acompanha os processos dos workers (versus o `uwsgi`) que ajuda gerenciar os arquivos de métricas criados pelas configurações acima. Se você estiver usando o NetBox com o gunicorn em um ambiente que utiliza containers seguindo a metodologia um-processo-por-container (one-proccess-per-container), então provavelmente não não precisa mudar para `uwsgi`. Mais detalhes podem ser encontrados em [issue #3779](https://github.com/netbox-community/netbox/issues/3779#issuecomment-590547562).