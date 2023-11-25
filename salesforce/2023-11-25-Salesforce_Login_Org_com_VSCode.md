---
title: Salesforce - Fazendo Login em Organização pelo VSCode
tags: [programacao, linguagens, salesforce, apresentacao, introducao, basico, vscode, apex]
categories: [programacao, salesforce]
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

Como logar em uma Organização (Playground)

<!--more-->

Listar todas as organizações que já se está logado:

``` bash
sf org list --all
```

Fazer login em uma organização através da página Web e dando o alias "Playground #5"

``` bash
sf org login web --alias "Playground #5"
```

Fazer o logout de uma Org, no caso a que tem o alias "Playground #6"

```bash
sf org logout --target-org "Playgroud #6"
```
