---
title: Tutorial para Desenvolvimento de Plugins
description: 
published: true
date: 2022-05-06T14:48:58.412Z
tags: tutorial, plugin, desenvolvimento, guia
editor: markdown
dateCreated: 2022-05-06T14:36:47.919Z
---

# Tutorial para Desenvolvimento de Plugins

Esse guia procura demonstrar o processo de desenvolvimento de um plugin customizado para o Netbox na versão v3.2 ou maior. Seguindo cada um dos passos descritos, o leitor será capaz de criar um plugin desde o início para o gerenciamento de Listas de Acesso (ACLs) no Netbox, utilizando a maioria dos componentes do framework de plugins do Netbox.

Uma cópia completa do plugin demo criado neste tutorial está disponível no repositório [`netbox-plugin-demo`](https://github.com/netbox-community/netbox-plugin-demo) para ser utilizado de referência. Para a sua conveniência, o código completo correspondente à cada passo do tutorial está salvo como em uma branch específica no repositório demo. Por exemplo, se você quer começar do passo 05, basta mudar para a branch `step04-forms` realizando o checkout.

<br>

## Pré-requisitos
 
> Antes de tentar criar um plugin, primeiro verifique suas habilidades atuais. 
> Os autores de um plugin devem ter alguma proficiência nos seguintes pontos:
>
> - **[Programação em Python](https://www.python.org/)**
>
> - **[Framework Django](https://www.djangoproject.com/)**
>
> - **Fundamentos da API REST (em algums projetos, será opcional)**
> 
> - **Instalar, configurar e utilizar o Netbox** :warning: 
>
> Caso tenha dúvidas, você pode acessar a [comunidade no Discord]() e procurar o canal **#netbox-plugin-tutorial**
{.is-info}



<br>

---


  
* ## Conteúdos

  <br>
  
  &nbsp;&nbsp;&nbsp;&nbsp;Comece por aqui! :arrow_down: 
  * ### Passo 01: [Setup Inicial](./netbox-plugin-tutorial/step01-initial-setup.md)
  * ### Passo 02: [Models](./netbox-plugin-tutorial/step02-models.md)
  * ### Passo 03: [Tables](./netbox-plugin-tutorial/step03-tables.md)
  * ### Passo 04: [Forms](./netbox-plugin-tutorial/step04-forms.md)
  * ### Passo 05: [Views](./netbox-plugin-tutorial/step05-views.md)
  * ### Passo 06: [Templates](./netbox-plugin-tutorial/step06-templates.md)
  * ### Passo 07: [Navigation](./netbox-plugin-tutorial/step07-navigation.md)
  * ### Passo 08: [Filter Sets](./netbox-plugin-tutorial/step08-filter-sets.md)
  * ### Passo 09: [REST API](./netbox-plugin-tutorial/step09-rest-api.md)
  * ### Passo 10: [GraphQL API](./netbox-plugin-tutorial/step10-graphql-api.md)
{.links-list}

</div>

<br><br>


<div>

## Referência
* [NetBox Plugin Development Documentation](https://netbox.readthedocs.io/en/feature/plugins/development/)
{.links-list}

  
<br>
  
## Precisa de Ajuda?
Se você fala inglês e precisa de alguma ajuda, pode recorrer ao canal oficial `#netbox` dentro da [Comunidade NetDev no Slack](https://netdev.chat/).
  
Ou ainda, entre na **[Comunidade Brasileira no Discord](https://discord.gg/ZwMbsyqn)** e procure pelo canal `#netbox-plugin-tutorial`