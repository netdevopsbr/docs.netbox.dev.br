# Introdução ao NetBox

NetBox foi originalmente desenvolvido pelo seu mantenedor principal, [Jeremy Stretch](https://github.com/jeremystretch), enquanto ele estava trabalhando como engenheiro de rede na [DigitalOcean](https://www.digitalocean.com/) em 2015 como parte de um esforço para automatizar o provisionamento de rede. Reconhecendo o potencial da nova ferramenta, DigitalOcean concordou em lançar o projeto como open source em Junho de 2016.

Desde então, milhares de organizações pelo mundo começaram a utilizar o NetBox como centro de fonte de verdade da rede (network source of truth) para ajudar e empoderar ambos operadores de redes e automação, em geral. hoje, o projeto open source é administrado pela [NetBox Labs](https://netboxlabs.com/) e um time de mantenedores voluntários. Além do produto principal, vários plugins foram desenvolvidos pela comunidade para melhorar e expandir as funções existentes do NetBox.

## Principais características e funções

NetBox foi criado especificamente para servir as necessidades dos engenheiros e operadores de rede. Abaixo está uma breve lista das principais funções que existem no sistema.
- Gerenciamento de endereçamento de IP (IPAM) com paridade completa entre IPv4 e IPv6
- Provisionamento automático do próximo endereço IP disponível
- VRFs com importação & exportação de route targets
- VLANs com grupos de escopos variáveis
- Gerenciamento de ASN (AS number)
- Altura de rack com renderização em SVG
- Modelagem de dispositivos utilizando tipos de dispositivos pré-definidos
- Chassis virtuais e contextos de dispositivos
- Rede, energia e cabeamento de console com restreamento via SVG
- Modelagem de distribuiçaõ de energia
- Circuito de dados e rastreamento de fornecedor
- Wireless LAN e links point-to-point
- L2VPN overlays
- Grupos FHRP (VRRP, HSRP, etc.)
- Vinculação de aplicações e serviços (porta tcp/udp com respectivo app)
- Máquinas virtuais & Clusters
- Hierarquia flexível para locais (sites) e localizações (locations)
- Atrelação de locatário responsável
- Configuração de contextos para Dispositivo & VM com intuito de realizar configurações avançadas de renderização
- Campos customizados para extender os modelos de dados existentes
- Regras customizadas de validação
- Relatórios customizados & scripts executáveis diretamente pela UI (User-Interface)
- Framework completo para que plugins adicionem funcionalidades customizadas
- Autenticação Single Sig-On (SSO)
- Sistema de permissões robusto baseado em objetos
- Change logging automático e detalhado
- Engine global de pesquisa
- Integração com NAPALM

## O que o NetBox não é

Enquanto que o NetBox se esforça para cobrir muitas áres do gerenciamento de rede, o escopo de suas funções é necessariamente limitado. Isso garante que o desenvolvimento foque nas funcionalidades principais e que o escopo seja razoavelmente contido. Consequentemente, isso ajuda a explicar alguns **exemplos de funcionalidades que o NetBox não tem:*

- Monitoramento de Rede
- Servidor DNS
- Servidor RADIUS
- Gerenciamento de configuração
- Gerenciamento de facilities (instalações)

Dito isso, NetBox pode ser usado com certa facilidade para popular outras ferramentas externas com as informações que estas mesmas precisam para funcionar.

## Filosofia do Design

NetBox foi desenhado com os seguintes princípios básicos fundamentais.

### Replicar o mundo real

Uma cuidadosa análise foi feita para que os modelos de dados replicassem redes do mundo real. Por exemplo, endereços IP são atrelados não a dispositivos, mas interfaces específicas deste dispositivo, e uma interface pode ter vários endereços IP atrelados a ela, também.

### Servir como "Fonte de Verdade" (Source of Truth)

O NetBox tem a inteção de representar o __estado da rede desejável__ ao invés de representar o __estado operacional__ . Com isso, a importação automática da rede operacional é fortemente desencourajada. Todos os dados criados pelo NetBox deveriam primeiro ter passado por um humano para assegurar sua integridade. O NetBox pode então ser usado para popular sistemas de monitoramento e provisionamento com um alto nível de confiança e credibilidade.

### Manter simples

Quando estamos entre uma escolha relativamente simples que soluciona 80% do problema e outra que é completa, porém complexa, a primeira escolha será normalmente favorecida e escolhida. Isso garante que tenhamos um código limpo com uma linha de aprendizado pequena.

## Stack da Aplicação

NetBox é construído em cima do Django, um framework Python e utiliza o banco de dados PostgreSQL. Roda como um serviço WSGI atrás do servidor HTTP de sua escolha.

| Função | Componente |
|--------|------------|
| Serviço HTTP | nginx ou Apache |
| Serviço WSGI | gunicorn ou uWSGI |
| Aplicação | Django/Python |
| Banco de Dados | PostgreSQL 11+ | 
| Enfileiramento de tarefas | Redis/django-rq|
| Acesso a dispositivos | NAPALM (opcional) |
