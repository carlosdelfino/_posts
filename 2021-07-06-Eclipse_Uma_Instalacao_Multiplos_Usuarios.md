---
title: Eclipse com Uma Instalação e Multiplos Usuários
tags: [programacao, eclipse, multiuser, rede]
categories: [eclipse]
layout: article
share: true
toc: true
comments: true
coinbase:
  show: false
google:
  plusone: true
ads:
 show: true
tagcloud: true
---

O Eclipse é uma IDE bastante flexível e dinámica, que permite uma grande váriedade de plugins e cenários de instalações, porém pode vir a ocupar muito espaço, a muitos anos eu uso o Eclipse, porém nos últimos 6 anos parei de acompanhar suas atualizações e não tenho mais usado, mas penso em retoma-lo como minha ferramenta principal de edição de programas.

<!--more-->

Apesar do meu ambiente apenas eu estar trabalhando nele, sempre gosto de configura-lo de forma a permitir portabilidade e transito entre sistemas, além de maquinas, ou seja, instalado num servidor de arquivos e acesso tudo remotamente de forma o mais distribuida possível, as vezes perco um pouco de desempenho, já que preciso transitar arquivos via rede e barramentos um pouco mais lentos que o acesso direto ao HD local, mas prático outros conceitos de redes.

No caso configuro abaixo o Eclipse para que ele possa ser acesso por múltplos usuários numa única instalação no linux.

Assim basta baixar a instalação padrão do linux, instala-la como sendo root num diretório como `/opt/eclipse`, no caso eu instalei o cpp, assim ele ficou no diretório `/opt/eclipse/cpp-2018-09`.

Então edito o arquivo `/opt/eclipse/cpp-2018-09/eclipse/configuration/config.ini` e adiciono ao final as seguintes linhas:

```
osgi.configuration.area=@user.home/eclipse/configuration
osgi.sharedConfiguration.area=INSTALL_DIR/configuration
osgi.configuration.cascaded=true
```
A variável `@user.home` é interna da configuração do eclipse e será substitítuida pelo direorio **home** do usuário que estiver chamando o eclipse, já onde está escrito *INSTALL_DIR* **deve** ser substituido pelo prefixo do diretório onde foi instalado, no meu caso foi `/opt/eclipse/cpp-2018-9/eclipse/`
 
