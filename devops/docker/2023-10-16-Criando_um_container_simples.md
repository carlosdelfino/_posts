---
title: "Criando um Container simples no Docker" 
tags: [Linux, Segurança, Sistemas de Arquivos, Administração, Invasão, Docker]
categories: [LinuxWindowsMac, Docker]
layout: article
share: true
toc: true
comments: true
feature:
 category: true
 index: true
image:
 feature: linuxmacwindows/allOS-logos-900x210.png
 teaser: linuxmacwindows/macwindowslinux-500x210.png
ads: 
 show: true
tagcloud: true
coinbase:
 show: true
---

Com o Docker é possível criar ambientes simples porém com grande poder, como por exemplo um Sandbox para testar aplicativos ou mesmo ambientes com configurações e pacotes especialmente instalados para uma tarefa pontual.

<!--more-->

Este tutorial pode ser considerado como um primeiro passo com o Docker, pois iremos usar-lo de forma bem simples e pontual, ou seja como criar um container padrão e abrir o shell deste container.

## Criando o Container

O Primeiro passo é baixar a imagem que será usada para criar o container com base num modelo pré  disponiblizado em um repositório, para quem conhece o GIT perceberá algumas semelhanças conceituais. Esta imagem ficará guardada para usuo futuro. As alterações realizadas no container não interferem na imagem.

Para uma imagem pré instalada do Ubuntu, use o comando abaixo:

```
$ docker pull ubuntu
```

## Abrindo o Shell do novo container

Agora você possui uma imagem do *Ubuntu* que possui um commit inicial chamado `latest`, cada vez que efetuar alterações no container você pode _commita-la_, assim ao abrir o container você faz referência ao estágio desejado, conforme o commit realizado. Estes commits se tornam imagens que podem ser usadas para criar novos containers no estágio registrado.

Use o comando abaixo para inicializar um container no modo interativo (`-i`) e com um pseudo TTY ()`-t`) para interação com o shell:

```
$ docker run -i -t ubuntu /bin/bash
```

A cada vez que executar este comando um novo container será criado, ao sair as alterações realizadas ficam armazenadas no container e não na imagem, observe que no prompt do Shell que se abriu é dado um nome para a maquina, algo como *"e7905bb010bf"* você pode dar um nome a seu cotaniner usando a chave `--name`. Mesmo dando um nome ao Container ele continuará nomeando a seção linux com um ID aleatório, a importancia em se dar um nome é para referir a imagem nos comandos do Docker.

```
$ docker run --name "MeuPrimeiroContainer" -i -t ubuntu /bin/bash
```

## Listando as Imagens disponiveis

Para vermos quais imagens estão disponiveis localmente use o comando:

```
$ docker image
```

## Listando os containers em execução ou criados

Para listarmos todos os containers, inclusive os que já foram finalizados (parados), use o comando:

```
docker ps -a
```

A chave `-a` informa ao docker para listar todos os processos de container em execução inclusive os  já interrompidos.

## Commitando um container alterado

Para commitar (depositar/registrar) um container como imagem, use o comando a seguir:

```
$ docker commit -a "Carlos Delfino" -m "Meu primeiro container com atualizações especificas" MeuPrimeiroContainer primeirocontainer
```

No comando acima temos a chave `-a` que informa o autor do commit, em seguida temos a chave `-m` que fornece uma mensagem que descreve o commit e finalmente temos o nome do container usado como referência para criação da nova imagem e o nome que será dado a imagem, sendo este último obrigatoriamente todas as letras em minúsculo.

Liste as imagens e verá que foi criado uma nova imagem com a *tag* _latest_, caso queira fazer um novo commit você pode executar o comando novamente com os mesmos parametros, o Docker irá a imagem anterior sem nome e sem Tag, criando uma nova imagem.

Para preservar os nomes e identificar cada estágio da imagem com uma tag, basta adicionar o nome da tag separando com 2pontos o nome desejado para a tag:

```
$ docker commit -a "Carlos Delfino" -m "Meu primeiro container com atualizações especificas" MeuPrimeiroContainer primeirocontainer:nomedatag
```
