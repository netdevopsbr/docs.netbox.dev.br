# Configuração do Servidor HTTP

Essa documentação fornece exemplos de configuração para tanto o [nginx](https://www.nginx.com/resources/wiki/) quanto o [Apache](https://httpd.apache.org/docs/current/), embora qualquer servidor HTTP que suporte WSGI deve ser compatível.

!!! info Observação

    Para sermos breve, somente as instruções para o Ubuntu 20.04 são fornecidas nesse tutorial. Essas tarefas não são exclusivas do NetBox e devem serem portáveis para outras distribuições com alterações mínimas. Consulte a documentação da sua distribuição caso precise de uma assistência adicional.

## Obtendo o Certificado SSL

Para habilitar o acesso HTTPS ao NetBox, você precisará de um certificado SSL válido. Você pode comprar um certificado de uma fornecedora comercial confiável, ou obter um de graça da [Let's Encrypt](https://letsencrypt.org/getting-started/), ou ainda gerar o seu próprio (embora certificados auto-assinados / self-signed são geralmente não confiáveis). Ambos os arquivos público e privado precisam ser instalados no servidor NetBox em um local que seja lido pelo usuário `netbox`.

The command below can be used to generate a self-signed certificate for testing purposes, however it is strongly recommended to use a certificate from a trusted authority in production. Two files will be created: the public certificate (`netbox.crt`) and the private key (`netbox.key`). The certificate is published to the world, whereas the private key must be kept secret at all times.

O comando abaixo pode ser utilizado para gerar um certificado self-signed (auto-assinado) para finalidade de teste, no entato, é fortemente recomendado que se use um certificado emitido por uma autoridade confiável em um ambiente de produção. Os arquivos que serão criados são: certificado público (`netbox.crt`) e a chave privada (`netbox.key`). O certificado é publicado para o mundo, enquanto que a chave privada deve ser guardada em segredo para sempre.

```no-highlight
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
-keyout /etc/ssl/private/netbox.key \
-out /etc/ssl/certs/netbox.crt
```

O comando acima irá pedir detalhes adicionais para o certificado, mas todos eles são opcionais.

## Instalação do Servidor HTTP

### Opção A: nginx

Comece a instalação do nginx com:

```no-highlight
sudo apt install -y nginx
```

Uma vez que o nginx foi instalado, copie o arquivo de configuração fornecido pelo NetBox para `etc/nginx/sites-available/netbox`. Certifique-se de substituir `netbox.example.com` com o domínio ou endereço IP da sua própria instalação. (Esses valores devem estar em conformidade com a variável `ALLOWED_HOSTS` em `configuration.py`)
Once nginx is installed, copy the nginx configuration file provided by NetBox to `/etc/nginx/sites-available/netbox`. Be sure to replace `netbox.example.com` with the domain name or IP address of your installation. (This should match the value configured for `ALLOWED_HOSTS` in `configuration.py`.)

```no-highlight
sudo cp /opt/netbox/contrib/nginx.conf /etc/nginx/sites-available/netbox
```

Então, dele `/etc/nginx/sites-enabled/default` e crie um link simbólico no diretório `sites-enabled` do arquivo de configuração que você acabou de criar.

```no-highlight
sudo rm /etc/nginx/sites-enabled/default
sudo ln -s /etc/nginx/sites-available/netbox /etc/nginx/sites-enabled/netbox
```

Finalmente, reinicie o serviço `nginx` para que utilize a nova configuração.

```no-highlight
sudo systemctl restart nginx
```

### Opção B: Apache

Comece a instação do Apache com:

```no-highlight
sudo apt install -y apache2
```

Agora, copie o arquivo de configuração padrão (default) para `/etc/apache2/sites-available/`. Ceritique-se de modificar o parâmetro `ServerName` apropriado.

```no-highlight
sudo cp /opt/netbox/contrib/apache.conf /etc/apache2/sites-available/netbox.conf
```

Agora, garanta que os módulos do Apache exigidos estão habilitados para habilitar o site do `netbox` e recarregue o Apache:

```no-highlight
sudo a2enmod ssl proxy proxy_http headers rewrite
sudo a2ensite netbox
sudo systemctl restart apache2
```

## Confirme a Conectividade

Até aqui, você já tem que conseguir conectar-se ao serviço do HTTP com o nome do servidor e endereço IP que forneceu nos passos anteriores.

!!! info Observação

    Tenha em mente que as configurações fornecidas aqui são apenas pra instalações com requisitos mínimos para ter o NetBox up e rodando. Você talvez queira realizar alguns ajustes para melhor servir o seu ambiente de produção.

!!! warning

    Certos componentes do NetBox (como a exibição dos diagramas de elevação do rack) dependem do uso de objetos embutidos. Garanta que a configuração do servidor HTTP não sobescreve o cabeçalho de resposta `X-Frame-Options` definido pelo NetBox.

## Resolvendo possíveis Problemas

Se não você não conseguiu conectar-se ao servidor HTTP, verifique:

* Nginx/Apache está rodando e configurado para escutar na porta correta.
* Acesso não está sendo bloqueado pelo firewall em algum lugar pelo caminho. (Tente conectar-se localmente pelo servidor em si, talvez seja útil utilizar o `curl`)

Se você conseguiu se conectar mas recebeu um erro de código HTTP 502 (bad gateway), verifique:

* O processo worker do WSGI (gunicorn) está rodando (`systemctl status netbox` deve mostrar o status de "active (running)")
* Nginx/Apache está configurado para conectar-se na porta que o gunicorn está escutando (porta padrão é 8001).
* SELinux não está previnindo conexão com proxy reverso. Você talvez precise habilitar conexões de rede via HTTP com o comando `setsebool -P httpd_can_network_connect 1`