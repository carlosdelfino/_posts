---
title: Editando um velho Commit.
categories: [Series, Series Trabalhando com o Git]
tags: [Series, GIT, Programação, Gestão, Controle de Versao, Commit]
layout: article
share: true
comments: true
feature:
 index: true
 category: true
ads: 
 show: true
coinbase:
  show: true
toc: true
image:
  teaser: frameworks/react_redux.png
---

Quandas vezes após longos dias de trabalho, você descobriu que aquele commit realizado semana que passou está com um erro bem simples no código ou mesmo no texto descritivo do trabaho realizado? ai vc precisa voltar nele para fazer uma correção e evitar de gerar um novo commit para expor este erro e corrigi-lo sem problemas futuros?

<!--more-->


Bem hoje a solução é simples, mas quem usa o GIT a muito tempo, sabe que ouve épocas que não hávia solução, uma vez enviado o trabalho realizado para o repositório não havia como resupera-lo para edição, a não ser criar um novo commit.

E de certa forma isso é a ação correta, deve se evitar o máximo que possível a edição dos commits.

Mas erros acontecem e o amadurecimento do GIT agora nos permite não só sobreescrever o repositório, como editar também aquele velho commit que está com um pequeno erro que não influi nas versões ou mesmo não é um BUG, ou algo a ser documentado como correção de código.

Para isso basta usar o recurso de Rebase do GIT, assim identifique a quantos commits atrás está seu trabalho e vamos lá edita-los. Você pode também fazer referência direta a ele, mas vamos ver primeiro como editar uma sequência de commit que está lá atrás e corri-los.

Supondo que nos 20 ultimos commits descobrimos que precisamos corrigir 5 deles, então fazemos o rebase dos últimos 20. veja o comando usa a chava `-i` para dizer ao git que é um processo interativo.

```shell
meu-proj $ git rebase -i @~20
```

O Git irá abrir uma janela com o seu editor padrão, o mesmo que é usado para fazer as descrições do commit, com a lista dos commits, neste arquivo na primeira coluna você irá indicar oq ue deseja fazer, e nossa intenção é editar os commits. portanto basta colocar o comando `edit` em cada commit que assim será. Abaixo um exemplo com os últimos 16 commits que realizei no meu site.

'''git
pick b6676144 mais informações sobre velocidade do motor
pick 03a6f649 Lista de ocmponentes entre outras itens.
pick 3b001248 corrigindo o link
pick 9fbb1c76 adicionado hotmart analytics
pick 121dcbcb Atualizado a lista de serviços
pick e4adefc6 Atualização da fonte ionicons
pick c4e78e5e Atualização dos serviços
pick 8828876e Atualização dos fatos do dia a dia
pick d47a071e ajuste no tamanho da imagem.
pick 1076a5a8 Melhorias na diagramação e uso de outros recursos
pick 5cb4599a adicionado include para uso do circuitjs1
pick b2837119 link ara o comercial simples
pick 3ac1d647 sempre que há maiusculo e minusculo envolvido há problemas
pick c92c6b89 buscando uma forma de selecionar multiplos redirecionamentos quanod envolve case.
pick 370e3f7f migrando configurações para uso de novos plugins do jekyll
pick fe26762f iremovido temporiamente
'''

Dos commits acima irei corrigir: o primeiro o terceiro, o quarto, nono e o decimo primeiro. Editando o arquivo ele fica assim: 

'''git
edit b6676144 mais informações sobre velocidade do motor
pick 03a6f649 Lista de ocmponentes entre outras itens.
edit 3b001248 corrigindo o link
edit 9fbb1c76 adicionado hotmart analytics
pick 121dcbcb Atualizado a lista de serviços
pick e4adefc6 Atualização da fonte ionicons
pick c4e78e5e Atualização dos serviços
pick 8828876e Atualização dos fatos do dia a dia
edit d47a071e ajuste no tamanho da imagem.
pick 1076a5a8 Melhorias na diagramação e uso de outros recursos
edit 5cb4599a adicionado include para uso do circuitjs1
pick b2837119 link ara o comercial simples
pick 3ac1d647 sempre que há maiusculo e minusculo envolvido há problemas
pick c92c6b89 buscando uma forma de selecionar multiplos redirecionamentos quanod envolve case.
pick 370e3f7f migrando configurações para uso de novos plugins do jekyll
pick fe26762f iremovido temporiamente
'''

Agora basta salvar e sair do seu editor.

Para cada commit que será editado, ele irá apresetnar uma mensagem similar a que vemos abaixo:

'''shell
Stopped at b6676144...  mais informações sobre velocidade do motor
You can amend the commit now, with

  git commit --amend

Once you are satisfied with your changes, run

  git rebase --continue
'''

Veja, no meu caso o código ```b6676144 representa o primeiro commit, é o hash do commit.

O Git lhe instrui o que fazer, você pode editar tanto o código que está no seu diretório de trablaho quando adicionar novos aquivos a este commit, então ao finalizar vc addiona as alterações normalmente, e executa o commit adicionando a chamve de comando `--amend`, assim o Git irá reaproveitar o commit existente.

Ao finalizar a correção, vc usa o coamndo `git rebase --continue` para que ele continue a processar seu rebase até que finalize as orientadas dadas no aquivo de rebase que editamos no inicio do processo.

Caso não queira mais editar o commit basta continuar, se por qualquer motivo desejar destir do processo de `rebase`, basta usar o comando `git rebase --abort`.

Ao final do processo você receberá a mensagem: 

```shell
Successfully rebased and updated refs/heads/master.
```

## E como fazer a edição de um commit especifico sem percorrer toda a arvore?

Como você pode ver o rebase percorre toda a arvore contando a quantidade de commits informadas, se você desejar fazer o rebase apenas de um commit especifico basta apontar para ele usando o hash dele ou parte final deste hash, vejamos na lista acima o 14 commit, ele tem o hash final: `c92c6b89`, portando basta eu usar o comando:

```shell
meu-proj $ git rebase -i 'c92c6b89^'
```

Veja que é preciso colocar o sinal `^` no final do hash, para indicar que o rebase começa nele, e você ainda receberá mais 6 commits antes dele para poder intervir.

Observe também que a ordem do arquivo está do commit mais atual para o mais antigo.

## Enviando para o servidor de respositórios suas atualziações

Como você interviu em commits que podem já ter sido enviados para o servidor, você precisa sobreescrever o servidor, mas antes atualize seu repositório localmente com alguma mudança que possa ter sido realizada por seus amigos e colegas de trabalho no servidor, e então so ai force suas correções sobre o servidor com o comando:

```shell
git push --force
```

Isso irá apagar o servidor e sobrecreve-lo com seu novo repositório local.

Boa sorte.
