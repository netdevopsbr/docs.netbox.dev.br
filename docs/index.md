![NetBox](netbox_logo.svg "NetBox logo"){style="height: 100px; margin-bottom: 3em"}

# A mais completa ferramenta de Network Source of Truth

!!! warning

    **There is no relation to [Netbox Labs](https://netboxlabs.com) or its official repo's.** It is just a effort to increase brazilian community base.

    If you are portuguese native speaker and was able to easily read this warning, I highly recommend you to continue read the [Netbox Offial Docs in English](https://docs.netbox.dev)! It will surely be more productive to your learning.



Netbox é a solução lider para modelagem e **documentação das redes** modernas. Combinando as disciplinas tradicionais de gerência de **endereçamento IP (IPAM)** e **gerenciamento de infraestrutura de datacenter (DCIM)** com **API** e extensões poderosas, NetBox fornece o **"source of truth"** (fonte de verdade) ideal para a **automação de rede**. Continue lendo e descubra o porquê de milhares de organizações ao redor do mundo colocam o Netbox no coração de suas infraestruturas.

![Netbox UI](./images/netbox-ui.webp)

## :material-server-network: Feito para Redes (Networks)

Diferente de outros CMDBs (configuration management database), NetBox fez uma curadoria dos modelos de dados que provê especificamente as necessidades dos engenheiros e operadores de rede. Entrega uma variedade de tipos de objeto cuidadosamente feito para melhor servir as necessidades do design de infraestrutura e documentação. Essas características cobrem todas as vertentes de das tecnologias de rede, desde gerenciamento de endereços IP até cabeamento e overlays, e ainda mais:
- Regiões hierárquicas, sites, e localizações
- Racks, dispositivos, e componentes de dispositivos
- Cabos e conexões wireless (Wi-Fi)
- Mapeamento da distribuição de energia
- Máquinas virtuais e clusters
- Prefixos IP, ranges e endereços
- VRFs e route targets
- Grupos FHRP (VRRP, HSRP, etc.)
- Números de AS (ASN)
- VLANs e escopo de grupos de VLAN
- L2VPN overlays
- Atrelação de locação (aluguel)
- Gerencimento de contatos

## :material-hammer-wrench: Customizável e Extensível

Em adição ao seu modelo de dados robusto e extensivo, **NetBox oferece uma grande quantidade de mecanismos que podem ser customizados e extendidos**. Sua arquitetura robusta de plugins permite que os usuários extendam a aplicação para estar em conformidade com suas necessidades mínimas de esforço de desenvolvimento.
- Custom fields (Campos Customizados)
- Custom model validation (Validação customizada de modelos de dados)
- Export templates (Exportação de templates)
- Webhooks
- Plugins
- REST & GraphQL APIs

## :material-lock-open:  Sempre aberto (opensource)
Porque o NetBox é uma **aplicação open source** licenciada pela [Apache 2](https://www.apache.org/licenses/LICENSE-2.0.html), seu código fonte inteiro é acessível pelos usuários finais que utilizam o sistema e não existe risco algum de [vendor lock-in](https://pt.wikipedia.org/wiki/Aprisionamento_tecnol%C3%B3gico). Além disso, **o desenvolvimento do NetBox é completamente público**, movido pela comunidade, onde todos podem contribuir.


## :material-language-python: Feito em Python

Netbox é feito pelo framework muito popular **[Django](http://www.djangoproject.com/)** da **linguagem Python**, que já é a linguagem favorita entre os engenheiros de rede. Usuários podem alavancar suas habilidades existentes de criar código em Python para extender as já existentes funcionalidades do NetBox através de **scripts customizados** e **plugins**.

## :material-flag: Começando com o Netbox?
- Se quer pular a instalação, experimente a [versão demo](https://demo.netbox.dev/) disponível publicamente
- O [guia de instalação](https://docs.netbox.dev/en/stable/installation/) lhe ajudará a fazer sua própria instalação e torná-la disponível (up and running)
- Ou tente a [imagem Docker](https://github.com/netbox-community/netbox-docker) feita pela comunidade para uma abordagem "low-touch"
- [NetBox Cloud](https://netboxlabs.com/netbox-cloud) é uma solução ofertada pela [NetBox Labs](https://netboxlabs.com/)

---

!!! info Contributing to Project

    ### Português

    Se você é brasileiro ou nativo da língua portuguesa e tem segurança, tanto com o português quanto com o inglês, não deixe de visitar o [repositório no GitHub](https://github.com/netdevopsbr/docs.netbox.dev.br) e **revisar** os textos ou ainda **traduzir** o trabalho em andamento! Basta clonar o repositório, fazer as devidas alterações e abrir uma [Pull Request](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/about-pull-requests). Agradeço desde já!

    Se ainda quer contribuir de outra forma, ajudaria muito se pudesse virar um sponsor ou fazer uma contribuição individual [clicando aqui!](https://github.com/sponsors/emersonfelipesp)

    ---

    ### English
    
    If you are **english native speaker**, but knows the portuguese language and feels comfortable to **contribute**, feel free to visit the [github repository](https://github.com/netdevopsbr/docs.netbox.dev.br) and **review** or **translate** the ongoing work! Just clone the repo and make the desired changes, then open a Pull Request for approval!

    Or maybe you would like to financially contribute to this project, it would be very helpful if you sponsor at any value [using this link](https://github.com/sponsors/emersonfelipesp)! Thank you already!