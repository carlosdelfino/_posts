---
title: "Dar Acesso Root a Usuários" 
tags: [Linux, Segurança, Sistemas de Arquivos, Administração, Invasão]
categories: [LinuxWindowsMac]
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

No Linux como no Windows possui um usuários com acesso a nível de Administrador, porém existe um usuário que representa o Administrador, e se chama `root`, este usuário tem acesso ilimitado ao Sistema Operacional Linux.

<!--more-->

Porém no linux os usuários são configurados para rodarem como Root ou seja "Super Usuário" para se ter acesso aos recursos administrátivos, há outros modos de acesso a recursos especificos, veremos aqui como transformar o usuário comum em um usuário com super poderes, ou seja um "Super Usuário".

Os *"Super Usuários"* são chamados de *"Sudor"* no singular e *"Sudores"* no plural, referencia ao comando "sudo" que transforma momentaneamente o usuário em "Super Usuário". 

Para isso acontecer naturalmente é preciso adicionar o usuário ao grupo "sudo", e como sabem isso é feito no arquivo `/etc/group`, mas não iremos fazer isso manualmente e diretamente neste arquivo, para isso usaremos o comando `usermod` como apresentado a seguir, primeiro faça o login como *root* usando o comando `su`, já que normalmente não é possível fazer logim diretamente:

```
$ su
$ usermod -a -G sudo nome_do_usuario
```

O primeiro comando, `su`, dá acesso também aos recursos de *Super Usuário*, mas não converte momentaneamente o usuário comum. Já o segundo comando `usermod` com as chaves de comando `-a -G` adiciona (_append_) ao grupo que segue, no caso *sudo* e por último *nome_do_usuário* é auto explicativo.

Agora se pode usuar o comando `sudo` para dar super poderes momentaneos ao usuário comum, experimente:

```
$ sudo ls /root
```

A linha pede para listar a pasta /root que é acessível apenas para `Super Usuários`.

Agora basta preceder com `sudo` qualquer comando que exige *Super POderes*, veja a diferença do comando `su` que abre um novo shell como *Super Usuário*, e só sai quanod se digita `exit`, o que é pouco prático para tarefas pontuais como editar um arquivo apenas.

O uso desta forma traz mais segurança ao ambiente.
